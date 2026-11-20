# Data Ownership

| Owner | Store | Entities | Other-service access |
|---|---|---|---|
| Employee | `employee_db` | employees, departments, organization units, managers, employment status, country, role assignment | API only; retain external IDs only |
| Leave | `leave_db` | policies, holidays, entitlements, balance ledger, reservations, leave requests, outbox, idempotency records | API only; sole balance and lifecycle owner |
| Approval | `approval_db` | workflows, steps, decisions, audit history, inbox, idempotency records | API only; references leave request IDs without cross-DB FKs |
| Knowledge | `knowledge_db`, vector store | documents, versions, jobs, chunks, metadata, embeddings, citations | Retrieval API only |

No service accesses another service's database. Every service owns a separate PostgreSQL database; local databases may share one PostgreSQL server or container, but each service receives exclusive credentials for its own database only. Cross-service foreign keys and distributed transactions are prohibited. Each service independently manages SQLAlchemy migrations and its database schema.
