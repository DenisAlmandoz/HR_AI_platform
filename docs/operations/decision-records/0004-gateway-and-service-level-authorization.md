# ADR 0004: Gateway for External Access and Service-Level Authorization

## Status

Accepted

## Context

The platform must support external API consumers now and Microsoft Entra ID later while protecting employee and manager-scoped data.

## Decision

Require external clients to use API Gateway. The gateway validates identity and carries trusted context, but every service independently verifies caller identity, role/scope, and resource authorization. Initial roles are Employee, Manager, and HR Administrator. Internal callers use least-privilege service identities.

## Consequences

Authorization remains effective if a service is called internally. Each service must implement its own resource checks and test them.

## Alternatives considered

Gateway-only authorization centralizes policy but makes a routing layer the sole security boundary and does not protect direct internal calls.
