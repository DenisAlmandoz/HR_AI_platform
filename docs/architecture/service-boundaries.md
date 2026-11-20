# Service Boundaries

| Service | Responsibility | Explicit exclusions |
|---|---|---|
| API Gateway | External routing, token validation, request context, rate limits | Business-data ownership and sole authorization enforcement |
| Employee | Employee profile, organization, manager relationships, employment status | Leave balances, approval decisions |
| Leave Management | Policies, balances, reservations, leave requests, lifecycle | Manager-decision authority, employee master-data writes |
| Approval | Single-step manager workflows, decisions, audit trail | Leave balance changes and final leave lifecycle |
| HR Knowledge | PDF, DOCX, and Markdown ingestion; retrieval and citations | Transactional employee or leave data |

Permitted synchronous dependencies are `Leave → Employee`, `Leave → Approval`, and `Approval → Employee`. Approval never calls Leave. The Leave Service queries Approval to reconcile outcomes, preserving the one-way dependency design.

Future AI orchestration consumes narrowly scoped APIs as tools. It never accesses databases directly and cannot make authoritative approval decisions.
