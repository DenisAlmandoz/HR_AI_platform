# ADR 0005: Docker Compose for the Initial Runtime

## Status

Accepted

## Context

The project is at an architecture and prototype stage and must be easy to run locally.

## Decision

Use Docker Compose as the initial local runtime for services and separately owned PostgreSQL databases. Do not introduce Kubernetes, Terraform-managed infrastructure, or an event broker in this phase.

## Consequences

Local development stays approachable. Production deployment and scaling concerns are deferred and must be reconsidered before a production rollout.

## Alternatives considered

Kubernetes would add deployment parity but is disproportionate for the current team and prototype scope.
