# Future Power Automate Integration

Power Automate will be an external workflow client and enters through the API Gateway. It uses documented, least-privilege APIs and never accesses operational databases directly.

Any future callback or automation action must preserve idempotency, correlation IDs, and service-level authorization.
