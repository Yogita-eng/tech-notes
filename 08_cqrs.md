# 8. CQRS Pattern

## Overview
**Command Query Responsibility Segregation** — separate the read model (queries) from the write model (commands).

```
Write:   App → [Command] → [Write Model] → Database (normalized)
Read:    App → [Query]   → [Read Model]  → Database (denormalized / optimized view)
```

## Why CQRS?
In most systems, reads vastly outnumber writes. Optimizing both with the same model is hard.
- **Write model:** Normalized, transactional, enforces business rules
- **Read model:** Denormalized, optimized for specific queries, may be a different DB entirely

## Benefits
- Scale reads and writes independently
- Optimized read models for different use cases (search, reporting, dashboards)
- Simpler domain models (each side handles less)
- Better performance

## Challenges
- Increased complexity
- Data synchronization lag between write and read models
- Eventual consistency between models

## Best Paired With
- **Event Sourcing** — events update the write model, projections build read models
- **Microservices** — each service can have its own CQRS setup

---
