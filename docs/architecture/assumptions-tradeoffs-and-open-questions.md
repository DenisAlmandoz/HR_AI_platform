# Assumptions, Trade-offs, and Open Questions

## Confirmed assumptions

- The first release is API-first and uses only synthetic data.
- Poland is the sole enabled leave jurisdiction; policy boundaries support a future Czechia rollout.
- Approval is a single manager step. Initial roles are Employee, Manager, and HR Administrator.
- Docker Compose is the local runtime. Kubernetes and an event broker are not part of this phase.
- LangGraph, live Workday, Power Automate, Microsoft Fabric, and a web UI are future integrations.

## Trade-offs

REST and a database transactional outbox are preferred over an event broker to keep the prototype operable locally. This trades immediate event fan-out for polling and reconciliation complexity. Database-per-service protects service independence but makes cross-service workflows eventually consistent. The Leave Service therefore owns balance effects while Approval owns decision history.

## Open questions before implementation

1. Which Polish leave types, accrual rules, partial-day rules, and cancellation rules are required in the first policy set?
2. What request expiry duration and post-approval cancellation policy should apply?
3. Which authentication mechanism substitutes for Microsoft Entra ID during local development?
4. What retention, access-control, and source-language requirements apply to HR policy documents?
5. What Workday integration mechanism will be available later (API, event feed, or scheduled extract)?

These questions do not change current service ownership or dependency direction. Resolve them in ADRs before they become implementation constraints.
