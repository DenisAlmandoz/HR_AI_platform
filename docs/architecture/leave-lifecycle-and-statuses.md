# Leave Lifecycle and Approval Statuses

Leave owns the effective request lifecycle: `APPROVAL_PENDING`, `PENDING_MANAGER`, `APPROVED`, `REJECTED`, `CANCELLED`, and `EXPIRED`. It owns balance reservations, releases, and consumption.

```mermaid
stateDiagram-v2
    [*] --> APPROVAL_PENDING: request + reservation + outbox commit
    APPROVAL_PENDING --> PENDING_MANAGER: workflow creation acknowledged
    APPROVAL_PENDING --> CANCELLED: caller cancellation
    APPROVAL_PENDING --> EXPIRED: deadline reached
    PENDING_MANAGER --> APPROVED: reconciled approval
    PENDING_MANAGER --> REJECTED: reconciled rejection
    PENDING_MANAGER --> CANCELLED: caller cancellation
    PENDING_MANAGER --> EXPIRED: deadline reached
    APPROVED --> [*]
    REJECTED --> [*]
    CANCELLED --> [*]
    EXPIRED --> [*]
```

Approval owns workflow state: `PENDING`, `APPROVED`, `REJECTED`, `EXPIRED`, `CANCELLED`, and `SUPERSEDED`. It also owns immutable manager-decision records containing the decision, actor, timestamp, and idempotency key.

```mermaid
stateDiagram-v2
    [*] --> PENDING: idempotent workflow creation
    PENDING --> APPROVED: manager approves
    PENDING --> REJECTED: manager rejects
    PENDING --> EXPIRED: approval deadline reached
    PENDING --> CANCELLED: Leave cancellation tombstone
    APPROVED --> SUPERSEDED: Leave already cancelled or expired
    REJECTED --> SUPERSEDED: Leave already cancelled or expired
    note right of APPROVED
      Immutable manager decision:
      APPROVE, actor, timestamp, idempotency key
    end note
    note right of REJECTED
      Immutable manager decision:
      REJECT, actor, timestamp, idempotency key
    end note
    note right of SUPERSEDED
      Effective workflow status only.
      Audit decision is not changed.
    end note
```

`SUPERSEDED` is an effective workflow state, not a changed decision. A decision remains in the approval audit forever. It becomes superseded when Leave has already cancelled or expired the request, so the decision has no leave effect.

Leave applies a reconciled decision only while its request is active and the decision occurred before the deadline. Cancellation or expiry locks the request row, changes lifecycle state, releases the reservation, and records a workflow-cancellation outbox event atomically.
