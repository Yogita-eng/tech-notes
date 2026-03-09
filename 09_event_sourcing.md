# 9. Event Sourcing

## Overview
Instead of storing current state, store the **full history of events** that led to the current state.

```
Traditional:  User table → { id: 1, balance: 150 }
Event Sourcing: Events → [ AccountOpened(100), Deposited(100), Withdrawn(50) ]
```

## How It Works
1. Every state change is captured as an immutable **event** and appended to an **event store**
2. Current state is derived by **replaying** all events from the beginning (or from a snapshot)
3. **Snapshots** periodically capture current state to avoid replaying entire history

## Benefits
- **Complete audit trail** — full history of what happened and when
- **Time travel** — reconstruct state at any point in time
- **Debugging** — understand exactly how you got to current state
- **Event-driven integration** — other services can subscribe to events
- **CQRS-friendly** — read models built from event projections

## Challenges
- **Complexity** — paradigm shift from CRUD thinking
- **Event schema evolution** — changing events without breaking replays
- **Performance** — replaying many events can be slow (mitigated with snapshots)
- **Querying** — can't easily query current state without a read model

## Best For
- Financial systems (transactions, ledgers)
- Collaborative editing (Google Docs)
- Audit-critical domains
- Systems needing time-travel debugging

---
