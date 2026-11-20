# Testing Strategy

Each service owns unit, API, and persistence tests. Test service contracts with synthetic fixtures and verify authorization, idempotency, outbox retry, duplicate delivery, expiration, cancellation, and approval-race scenarios.

Run the full test suite and lint checks locally before review. Integration tests must use only synthetic data and must never access another service's database directly.
