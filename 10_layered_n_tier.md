# 10. Layered (N-Tier) Architecture

## Overview
Organizes code into **horizontal layers**, each with a specific responsibility. Most common pattern in enterprise software.

```
┌─────────────────────┐
│  Presentation Layer │  (UI, API controllers)
├─────────────────────┤
│  Business Layer     │  (Domain logic, services)
├─────────────────────┤
│  Data Access Layer  │  (Repositories, ORM)
├─────────────────────┤
│  Database Layer     │  (DB, cache, external APIs)
└─────────────────────┘
```

## Rules
- Each layer only communicates with adjacent layers
- Higher layers depend on lower layers (not vice versa)
- Layers can be swapped independently (e.g., change DB without touching business logic)

## Benefits
- Clear separation of concerns
- Easy to understand and maintain
- Testable (mock lower layers when testing upper)
- Familiar to most developers

## Challenges
- Can lead to "pass-through" layers with no real logic
- Changes often ripple through all layers
- Performance overhead (each layer adds processing)

## Clean Architecture / Hexagonal Architecture
Modern variants that keep the **domain at the center** and dependencies pointing inward:
```
[External: DB, UI, APIs] → [Application] → [Domain] 
```
Domain has no dependencies on external concerns — fully testable in isolation.

---

# Choosing the Right Architecture

| Requirement | Recommended Pattern |
|-------------|-------------------|
| Simple app, small team | Monolith |
| Independent scaling per component | Microservices |
| Event-driven, async workflows | Event-Driven + Message Queue |
| Sporadic workloads, low ops burden | Serverless |
| Decentralized, no single point of failure | P2P |
| High read/write asymmetry | CQRS |
| Full audit trail needed | Event Sourcing |
| File/content distribution at scale | P2P + CDN |

---

*Last updated: 2026 | Real-world systems often combine multiple patterns.*
