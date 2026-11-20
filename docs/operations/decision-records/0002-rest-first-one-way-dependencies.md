# ADR 0002: REST-First Communication and One-Way Dependencies

## Status

Accepted

## Context

The prototype needs straightforward local request-response workflows without messaging infrastructure.

## Decision

Use authenticated REST APIs for synchronous communication. External clients enter through API Gateway. Internal calls use direct authenticated service APIs with authorization at the receiving service. Allowed business dependencies are `Leave → Employee`, `Leave → Approval`, and `Approval → Employee`; Approval does not call Leave.

## Consequences

The design avoids circular dependencies and limits operational complexity. Leave reconciles Approval outcomes by querying Approval, which introduces intentional eventual consistency.

## Alternatives considered

An event broker or bidirectional callbacks could reduce polling but adds infrastructure and coupling not justified for the initial scope.
