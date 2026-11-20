# ADR 0001: Independent Services and Database per Service

## Status

Accepted

## Context

Employee data, leave balances, manager approvals, and policy retrieval have different business ownership and change independently.

## Decision

Use independent FastAPI services for Employee, Leave Management, Approval, and HR Knowledge. Each service exclusively owns a separate PostgreSQL database and exposes data through versioned APIs. Local databases may share one PostgreSQL server or container, but every service has exclusive credentials and access only to its own database. Cross-service database access and foreign keys are prohibited.

## Consequences

Services can be tested and deployed independently and retain clear data ownership. Cross-service workflows cannot use distributed transactions and require API calls, outbox delivery, and reconciliation.

## Alternatives considered

A shared database or modular monolith would simplify early joins but would weaken ownership and make later service separation riskier.
