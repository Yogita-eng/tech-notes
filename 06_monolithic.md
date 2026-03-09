# 6. Monolithic Architecture

## Overview
The entire application is built and deployed as a **single unit**. All components (UI, business logic, data access) are tightly coupled and run in one process.

```
┌─────────────────────────────────┐
│           Monolith               │
│  [UI] [Business Logic] [Data]   │
└─────────────────────────────────┘
            ↓
        [Database]
```

## Benefits
- **Simple to develop** — one codebase, one deployment
- **Easy to test** end-to-end
- **Low latency** — no network calls between components
- **Easy debugging** — everything in one place
- **Great for early-stage** startups

## Challenges
- **Scaling** — must scale entire app even if only one component is bottlenecked
- **Deployment risk** — any change requires redeploying everything
- **Technology lock-in** — hard to mix tech stacks
- **Large codebase** — becomes hard to navigate over time
- **Team coordination** — many engineers on same codebase = merge conflicts

## Monolith vs Microservices Migration
Most successful companies started as monoliths and extracted services as they scaled.

**Strangler Fig Pattern:** Gradually extract parts of the monolith into services:
1. New functionality → build as new service
2. Existing functionality → extract piece by piece
3. Eventually → monolith replaced entirely

## When Monolith Makes Sense
- Early-stage startups (move fast, validate ideas)
- Small teams (< 10 engineers)
- Simple domain
- When deployment simplicity is a priority

---
