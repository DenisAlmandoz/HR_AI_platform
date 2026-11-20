# ADR 0008: Defer AI Orchestration and Use Controlled Tools

## Status

Accepted

## Context

The platform will eventually support specialised AI agents, but the initial scope is deterministic HR service workflows.

## Decision

Defer LangGraph AI Orchestrator implementation. When introduced, it will use controlled, authenticated service APIs as tools and the Knowledge retrieval API for policy context. It will not access service databases or make authoritative approval decisions.

## Consequences

The initial architecture remains simpler and enforces clear business authority. Future agent capabilities require explicit tool contracts, auditability, and human authorization.

## Alternatives considered

Introducing orchestration in the first release would add agent-state, safety, and observability requirements before core HR workflows are validated.
