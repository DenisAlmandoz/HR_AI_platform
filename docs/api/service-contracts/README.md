# Service Contracts

Keep one versioned contract document per public or internal service API as endpoints are introduced. Contracts must specify caller identity/scope, request and response schemas, idempotency behavior, error responses, ownership of referenced IDs, timeouts, and retry safety.

Do not expose database operations, credentials, or internal implementation details through service contracts.
