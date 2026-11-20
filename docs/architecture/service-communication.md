# Service Communication

All APIs use `/api/v1/`, Pydantic request/response contracts, correlation IDs, explicit timeouts, and consistent errors. External callers use the API Gateway only. Internal calls use mTLS or equivalent service authentication and service-specific authorization.

Use REST for request-response operations:

- Leave validates employee eligibility and manager assignment through Employee.
- Leave creates, cancels, and queries approval workflows through Approval.
- Approval validates a manager relationship through Employee.

The Leave outbox delivers workflow commands at least once through REST. Approval deduplicates messages and detects stale per-request event sequences. A separate Leave-owned reconciliation worker periodically queries Approval for workflow updates; Approval never calls Leave. There is no event broker in the prototype. See [outbox and idempotency](transactional-outbox-and-idempotency.md).
