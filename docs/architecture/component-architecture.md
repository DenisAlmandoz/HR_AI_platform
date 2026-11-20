# Component Architecture

```mermaid
flowchart TB
    Client[External API Clients] -->|HTTPS /api/v1 + Idempotency-Key| Gateway[API Gateway]
    Web[Future Web UI] --> Gateway
    PowerApps[Future Power Apps] --> Gateway
    PowerAutomate[Future Power Automate] --> Gateway
    Entra[Future Microsoft Entra ID] -. signed claims .-> Gateway

    subgraph Compose[Docker Compose local runtime]
      Gateway -->|authenticated REST| Employee[Employee Service]
      Gateway -->|authenticated REST| Leave[Leave Management Service]
      Gateway -->|authenticated REST| Approval[Approval Service]
      Gateway -->|authenticated REST| Knowledge[HR Knowledge Service]
      Leave -->|authenticated REST| Employee
      Leave -->|authenticated REST| Approval
      Approval -->|authenticated REST| Employee
      Worker[Leave Transactional Outbox Worker] -->|poll and claim events| LeaveDB
      Worker -->|idempotent REST| Approval
      Reconciler[Leave Reconciliation Worker] -->|query pending workflow versions| Approval
      Reconciler -->|apply conditional lifecycle updates| LeaveDB
      Employee --> EmployeeDB[(employee_db PostgreSQL)]
      Leave --> LeaveDB[(leave_db PostgreSQL + outbox)]
      Approval --> ApprovalDB[(approval_db PostgreSQL + inbox)]
      Knowledge --> KnowledgeDB[(knowledge_db PostgreSQL)]
      Knowledge --> Vector[(HR-owned vector store)]
      Employee --> EmployeeMock[Internal Mock Workday Adapter: employee]
      Leave --> LeaveMock[Internal Mock Workday Adapter: leave]
    end

    EmployeeMock -. future contract .-> Workday[Future Workday]
    LeaveMock -. future contract .-> Workday
    AI[Future LangGraph AI Orchestrator] -. authenticated direct tool APIs .-> Employee
    AI -. authenticated direct tool APIs .-> Leave
    AI -. authenticated direct tool APIs .-> Approval
    AI -. policy retrieval .-> Knowledge
    Employee -. controlled export .-> Pipeline[Future analytical ingestion/export pipeline]
    Leave -. controlled export .-> Pipeline
    Approval -. controlled export .-> Pipeline
    Pipeline -. curated data .-> Fabric[Future Microsoft Fabric]
```

The gateway is mandatory for external clients. Internal service calls, including future AI tool calls, use direct authenticated REST APIs; every receiving service authorizes the caller and resource.
