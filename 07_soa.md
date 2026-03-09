# 7. Service-Oriented Architecture (SOA)

## Overview
An architectural style where software components provide **services** to other components via a communication protocol over a network, typically using an **Enterprise Service Bus (ESB)**.

SOA is the predecessor to microservices — similar principles but larger, heavier services.

## SOA vs Microservices

| | SOA | Microservices |
|-|-----|---------------|
| **Service Size** | Larger, coarser-grained | Small, fine-grained |
| **Communication** | ESB (complex middleware) | Lightweight (HTTP/REST, Kafka) |
| **Data** | Shared database common | Database per service |
| **Governance** | Centralized | Decentralized |
| **Deployment** | Less frequent | Continuous deployment |

## ESB (Enterprise Service Bus)
Central hub that handles routing, transformation, security, and orchestration between services.
- **Risk:** ESB becomes a bottleneck and single point of failure
- Microservices replaced ESB with "smart endpoints, dumb pipes"

---
