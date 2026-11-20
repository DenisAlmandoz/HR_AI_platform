# ADR 0003: Transactional Outbox for Approval Workflow Commands

## Status

Accepted

## Context

Submitting a leave request requires a request record, a balance reservation, and reliable approval-workflow creation without a distributed transaction or broker.

## Decision

Leave Service atomically persists the request, reservation, and outbox event. Events have immutable IDs and monotonically increasing per-request sequences; cancellation events are tombstones. A polling worker delivers idempotent REST commands to Approval, which records receiver-side deduplication and rejects stale creates after a cancellation tombstone. The worker polls every five seconds, uses a lease, retries with jittered exponential backoff from five seconds to five minutes, and marks events `FAILED` after ten attempts for monitoring and controlled redrive.

## Consequences

Workflow delivery is at-least-once and eventually consistent. Approval must keep inbox/deduplication and tombstone records. A failed event leaves the request recoverable in `APPROVAL_PENDING` and its reservation protected until successful transition, cancellation, or expiry; operators are alerted and must authorize any redrive.

## Alternatives considered

Direct synchronous workflow creation alone can lose the creation request after a Leave commit. A broker is deferred because it exceeds prototype infrastructure needs.
