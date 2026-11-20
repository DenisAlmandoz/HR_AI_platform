# Workday Adapter Contract

Employee and Leave services each define an adapter interface for their own Workday operations. The prototype implementation is an in-service mock backed by synthetic data; a future implementation calls Workday behind the same service-facing contract.

Adapters must support correlation IDs, idempotent command handling, effective-dated records, source versions, and explicit conflict reporting. They do not grant direct Workday access to other services.
