# API Standards

Expose versioned REST endpoints under `/api/v1/`. Use FastAPI, Pydantic 2 schemas, type hints, OpenAPI documentation, and consistent JSON errors. Validate input at the receiving service and return appropriate HTTP status codes.

Mutating endpoints require an `Idempotency-Key` where retries could create a duplicate business action. Propagate a correlation ID across gateway and internal calls. Define connect/read timeouts and bounded retries only for safe or idempotent operations.
