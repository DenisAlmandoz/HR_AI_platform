# Authorization Model

Initial roles are Employee, Manager, and HR Administrator. The gateway validates external identity and forwards trusted identity context; it does not replace service authorization.

Every service verifies the authenticated caller and applies resource-level rules. Employees can act only on their own data where permitted. Managers can act only for their current direct reports. HR Administrators have explicit administrative scopes. Approval verifies manager assignment before accepting a decision.

Internal callers use service identities with least-privilege scopes. The future AI Orchestrator may invoke documented tools but has no unrestricted database access and no authority to approve leave. Production identity integration is planned through Microsoft Entra ID.
