# Transactional Outbox and Idempotency

Submitting a leave request runs one Leave database transaction: create an `APPROVAL_PENDING` request, reserve balance, and insert a `workflow.requested` outbox event. A cancellation or expiration similarly writes `workflow.cancelled` while releasing its reservation.

Each outbox event has an immutable event ID, leave-request ID, event type, and monotonically increasing per-request sequence number. A cancellation event is a tombstone for that request version. The worker claims ready events every five seconds and preserves sequence order for a leave request when possible. Ordering alone is not trusted: Approval records the highest processed sequence and cancellation tombstone for each source request. It rejects a stale `workflow.requested` event that is older than, or superseded by, a known cancellation tombstone. If creation arrives before its later cancellation, Approval records the workflow and then applies cancellation; it never treats a delayed create as a new active workflow after a processed cancellation.

The worker claims rows using a lease, invokes Approval with the immutable event ID and sequence, and marks events delivered only after a successful response. A crashed worker's expired lease is reclaimable. Approval processes the event ID idempotently; duplicate delivery returns the original result without duplicating a workflow or changing its effective status.

Failed delivery retries with 0–20% jitter and `min(5 seconds × 2^(attempt-1), 5 minutes)`. Events store attempt count, next retry timestamp, lease data, and last error. After 10 failed attempts they become `FAILED`, remain retained, emit an operator alert, and require controlled, audited redrive using the original event ID.

A failed workflow-creation event leaves its request recoverable in `APPROVAL_PENDING`; it does not silently release the balance reservation. The reservation remains protected until a successful workflow/lifecycle transition, explicit cancellation, or expiry releases it. The Leave Service operational owner monitors failed-event count, delivery age, and oldest failed event; operators investigate the recorded error and authorize redrive.

## Reconciliation

A Leave-owned reconciliation worker runs every 60 seconds by default; this is a configurable prototype setting. It selects non-terminal requests with a known approval workflow (`PENDING_MANAGER`) and requests whose locally recorded Approval workflow version is behind the last observed version. It compares Approval's monotonic workflow version with the last applied Leave version, then conditionally applies a decision only when the Leave request remains active and the decision is within the request deadline.

Transient reconciliation failures leave request and balance state unchanged and are retried on the next scheduled run with bounded HTTP retry behavior. The worker records errors and exposes pending-request age, reconciliation lag, and stale-pending-request counts. Requests whose pending age exceeds the configured operational threshold raise an alert for the Leave Service operator.

| Operation | Duplicate protection |
|---|---|
| Leave submission | Caller-and-endpoint-scoped `Idempotency-Key`, payload hash, stored original response |
| Workflow creation | Outbox event ID, source leave-request ID, per-request sequence, Approval inbox and cancellation tombstone |
| Manager decision | Workflow-and-manager-scoped idempotency key; first valid terminal decision wins |
| Reconciliation | Monotonic Approval workflow version; Leave stores last applied version |

At-least-once delivery is safe because Approval deduplicates. Conflicting reuse of an idempotency key with different payloads is rejected.
