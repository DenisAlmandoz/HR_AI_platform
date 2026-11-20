
## 1. Project Overview

Build an enterprise-grade, AI-powered HR Employee Assistant using a microservices architecture.

The platform will allow employees and HR personnel to:

- Retrieve employee information.
- Check annual leave balances.
- Submit and track leave requests.
- Manage manager approvals.
- Ask questions about HR policies using Retrieval-Augmented Generation (RAG).
- Interact with specialised AI agents.
- Automate HR workflows.

The initial implementation must work locally using synthetic data. No real Workday tenant or production HR system is available.

The architecture must support future integration with Workday, Microsoft Fabric, Power Apps and Power Automate.

## 2. Technology Stack

### Backend
- Python 3.12
- FastAPI
- Pydantic 2
- SQLAlchemy 2.0
- PostgreSQL
- Alembic
- HTTPX for service-to-service HTTP communication

### Data Engineering
- PySpark
- Parquet
- Delta Lake
- Microsoft Fabric as the intended cloud data platform
- Databricks as an alternative execution environment

### AI Engineering — Future Phase
- LangGraph for multi-agent orchestration
- Grok for local LLM inference
- Chroma for initial vector retrieval
- Embedding models for semantic search
- MLflow for evaluation and observability

### Development and Infrastructure
- Docker
- Docker Compose
- Git and GitHub
- Pytest
- GitHub Actions
- Terraform in a later infrastructure phase

## 3. Architectural Principles

Use a microservices architecture with clearly defined business boundaries.

Each microservice must:

- Own a specific business capability.
- Have its own application code and configuration.
- Own its database or database schema with exclusive access.
- Expose functionality through documented API contracts.
- Be independently testable.
- Be independently deployable.
- Have its own automated tests.
- Avoid direct access to another service's database.

Use database-per-service ownership. Multiple PostgreSQL databases may initially run in one local PostgreSQL server, but each service must access only its own database.

Do not introduce unnecessary infrastructure or distributed systems complexity.

Prefer synchronous REST communication for straightforward request-response operations.

Introduce asynchronous messaging and event-driven communication when there is a genuine business requirement.

Keep services loosely coupled and independently replaceable.

## 4. Initial Microservices

### 4.1 API Gateway

Responsibilities:
- Provide a single entry point for frontend applications.
- Route requests to appropriate services.
- Handle cross-cutting request concerns.
- Support authentication and authorisation integration.
- Apply request validation and rate limiting where appropriate.

### 4.2 Employee Service

Responsibilities:
- Employee profiles.
- Departments and organisational structure.
- Manager relationships.
- Employee status and country information.

Database: employee_db.

### 4.3 Leave Management Service

Responsibilities:
- Leave balances.
- Leave entitlements.
- Leave requests.
- Leave calculations.
- Leave validation.
- Leave request lifecycle.

Database: leave_db.

### 4.4 Approval Service

Responsibilities:
- Manager approval workflows.
- Approval records.
- Approval decisions.
- Approval history.
- Approval status tracking.

Database: approval_db.

### 4.5 HR Knowledge Service

Responsibilities:
- HR policy ingestion.
- Document processing.
- Chunking and metadata extraction.
- Embedding generation.
- Vector retrieval.
- Policy search and source citations.

Initially implement document ingestion and retrieval independently of LangGraph.

### 4.6 AI Orchestrator Service

Future implementation using LangGraph.

Responsibilities:
- Supervisor agent.
- Specialised HR agents.
- Agent state management.
- Intent classification.
- Tool execution.
- Agent-to-service integration.
- Human-in-the-loop interactions.

Agents must consume controlled service APIs or explicitly exposed tools. They must never directly access another microservice's database.

## 5. Database and Persistence

Use PostgreSQL for operational transactional data.

Use SQLAlchemy 2.0 for persistence and Alembic for database migrations.

Each service owns its data.

Use Pydantic models for API request and response contracts.

Define:
- Primary keys.
- Foreign keys within service-owned databases.
- Unique constraints.
- Indexes.
- Validation rules.
- Audit timestamps.

Do not implement cross-service foreign keys.

Use service APIs or events to reference entities owned by other services.

Avoid distributed transactions. Use appropriate consistency patterns and compensating operations when necessary.

## 6. Synthetic Data

All initial development must use synthetic data.

Generate fictional records for:
- Employees.
- Departments.
- Managers.
- Leave balances.
- Leave requests.
- Approvals.
- Public holidays.
- HR policies.

Use Faker and deterministic random seeds.

Validate relationships and business rules before loading records.

Never introduce real employee personal information.

## 7. API and Integration Standards

Use REST APIs with versioning:

/api/v1/

Use:
- Pydantic schemas.
- Type hints.
- Appropriate HTTP status codes.
- Consistent error responses.
- OpenAPI documentation.
- Dependency injection.

External enterprise systems must be integrated through adapters.

Implement a Mock Workday Adapter initially.

Keep the future Workday API implementation behind the same integration contract.

Never expose internal credentials or unrestricted database operations through APIs.

Use HTTPX for communication between Python microservices.

Define timeouts, error handling and retry policies for service-to-service communication.

Avoid circular dependencies between services.

## 8. Security

Implement security as a cross-cutting architectural concern.

Future production integration should support Microsoft Entra ID authentication.

Requirements:
- Authentication and authorisation.
- Role-based access control.
- Employee-level data access restrictions.
- Manager-level approval permissions.
- Service-to-service authentication.
- Secrets through environment configuration or a secret manager.
- Audit logging.
- Input validation.

Never trust employee identifiers supplied directly by an unauthenticated frontend.

Never allow an LLM to make authoritative HR approval decisions.

Enforce access control in backend services and retrieval systems, not only through LLM prompts.

## 9. Data Engineering

Keep data engineering independent from operational microservices.

Use PySpark for:
- Synthetic data processing.
- Data validation.
- Data cleansing.
- Data enrichment.
- Analytical transformations.

Implement Bronze, Silver and Gold layers.

Microsoft Fabric is the intended analytical platform.

Keep transformations modular and reusable so they can run locally or be adapted to Databricks.

Do not use the analytical Lakehouse as a replacement for operational transactional databases.

## 10. AI Engineering

Introduce LangGraph only after the core services and their API contracts are stable.

Implement specialised capabilities:

- HR Knowledge Agent.
- Employee Services Agent.
- Leave Management Agent.
- Workflow Agent.
- Supervisor / Orchestrator.

Use controlled tools to invoke service functionality.

Implement RAG with metadata filtering, source attribution and access control.

Evaluate:
- Retrieval precision and recall.
- Answer groundedness.
- Answer relevance.
- Tool selection.
- Agent routing.
- Task completion.
- Latency and cost.

Use MLflow or an appropriate evaluation framework.

## 11. Testing Standards

Use Pytest.

Every microservice must have:

- Unit tests.
- API tests.
- Repository tests.
- Integration tests.
- Business rule tests.

Test service contracts and error scenarios.

Test that one service cannot bypass another service's authorisation rules.

Use synthetic test fixtures.

Run automated tests before committing changes.

## 12. Development and Deployment

Use Docker to containerise each microservice.

Use Docker Compose for local orchestration.

Provide independent environment configuration for every service.

Maintain separate database configuration and migrations per service.

Use GitHub Actions for:
- Linting.
- Formatting.
- Unit tests.
- Integration tests.
- Security checks.
- Build validation.

Introduce Kubernetes only when deployment requirements justify it.

## 13. Coding Standards

- Python 3.12.
- Type hints everywhere.
- Clear naming conventions.
- Small, focused functions.
- Separation of concerns.
- Dependency injection.
- Pydantic for API contracts.
- SQLAlchemy for persistence.
- Structured logging.
- Explicit exception handling.
- No unnecessary abstractions.
- No hardcoded secrets.
- No duplicated business logic.

Prefer readable and maintainable code over unnecessary complexity.

## 14. Codex Development Workflow

Work incrementally.

Before implementing a significant feature:

1. Inspect the existing repository.
2. Understand the current architecture.
3. Present an implementation plan.
4. Identify affected microservices.
5. Identify API contract changes.
6. Identify database changes.
7. Identify required tests.
8. Wait for approval when architectural decisions are involved.

After implementation:

1. Run relevant tests.
2. Review generated code.
3. Explain architectural decisions.
4. Update documentation.
5. Report any limitations.
6. Identify the next logical task.

Never rewrite unrelated modules without justification.

Never implement the entire platform in one task.

## 15. Initial Development Restriction

At the beginning of the project:

DO NOT implement application code until the architecture and data model have been reviewed.

The first deliverables must be:

- System architecture diagram.
- Microservices architecture diagram.
- Service responsibility definitions.
- Database ownership model.
- Entity Relationship Diagrams.
- API contract definitions.
- Synthetic data specification.
- Repository structure.
- Development roadmap.
- Architecture Decision Records (ADRs).

Save architecture documentation under docs/.

Wait for review before implementation.