# ADR 0006: Prototype Data Ownership and Future Workday Authority

## Status

Accepted

## Context

No Workday tenant or production HR data is available during the prototype, but enterprise integration is planned.

## Decision

Employee and Leave Services own synthetic operational data in the prototype and use internal mock Workday adapters. Approval Service owns local approval workflows and immutable manager-decision audit records; Leave owns request lifecycle and business effects. When enabled, Workday is authoritative only for an explicitly approved field/process-level matrix of worker or absence records. Services retain local projections and local-only metadata. Use versioned, effective-dated synchronization and explicit conflict review.

## Consequences

Prototype development does not depend on Workday. Future adapters must preserve service-facing contracts and distinguish authoritative fields, local projections, and local workflow audit. Workday does not automatically become authoritative for every approval decision.

## Alternatives considered

Treating local records as permanently authoritative would make enterprise reconciliation unreliable. Calling Workday directly from multiple services would duplicate integration concerns.
