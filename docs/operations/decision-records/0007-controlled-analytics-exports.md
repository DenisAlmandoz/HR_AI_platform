# ADR 0007: Controlled Exports for Microsoft Fabric

## Status

Accepted

## Context

Microsoft Fabric is intended for analytics, while service databases remain operational stores.

## Decision

Send curated, versioned service exports to Microsoft Fabric through controlled ingestion/export pipelines. Fabric does not use API Gateway as its primary integration and has no write access to operational databases.

## Consequences

Analytical data requires documented schemas, lineage, retention, and access controls. Fabric remains a future capability and does not affect the prototype runtime.

## Alternatives considered

Direct database access would couple analytics to operational schemas and increase data-governance risk.
