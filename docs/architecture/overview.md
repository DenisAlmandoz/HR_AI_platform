# Architecture Overview

The HR AI Assistant is an API-first, local-development prototype built from independent FastAPI business services. Docker Compose is the initial runtime environment; Kubernetes and an event broker are explicitly out of scope.

External clients enter through the API Gateway. Services own their operational data, are independently testable, and communicate using authenticated REST APIs. The prototype uses synthetic data only.

Initial services are Employee, Leave Management, Approval, and HR Knowledge. LangGraph orchestration, Power Automate, Microsoft Fabric, and live Workday integration are future capabilities.

See [component architecture](component-architecture.md), [service boundaries](service-boundaries.md), and [data ownership](data-ownership.md).
