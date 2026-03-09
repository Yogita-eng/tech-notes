# 2. Microservices Architecture

## Overview
An approach to building an application as a **collection of small, independently deployable services**, each responsible for a specific business capability.

```
[API Gateway]
     ↓
[User Service] [Order Service] [Payment Service] [Notification Service]
     ↓               ↓               ↓
   [DB]            [DB]            [DB]
```

## Key Principles
- Each service does **one thing well** (Single Responsibility)
- Services are **loosely coupled** and communicate via APIs or events
- Each service has its **own database** (Database-per-Service pattern)
- Services are **independently deployable** and scalable
- Small teams own individual services (**Conway's Law alignment**)

## Communication Patterns
- **Synchronous:** REST, gRPC (request-response)
- **Asynchronous:** Message queues, event bus (Kafka, RabbitMQ)

## Benefits
- Independent deployment → faster release cycles
- Independent scaling → scale only what needs it
- Technology flexibility (polyglot — each service can use different tech)
- Fault isolation — one service failing doesn't bring down the whole system
- Smaller codebases → easier to understand

## Challenges
- Distributed system complexity
- Network latency and failures between services
- Data consistency across services (no distributed ACID)
- Observability: tracing requests across services
- Operational overhead (many services to deploy/monitor)
- Testing complexity (integration tests across services)

## Supporting Infrastructure Required
- **API Gateway:** Single entry point for clients
- **Service Discovery:** Consul, Eureka, Kubernetes DNS
- **Distributed Tracing:** Jaeger, Zipkin, AWS X-Ray
- **Centralized Logging:** ELK Stack, Splunk
- **Circuit Breaker:** Hystrix, Resilience4j
- **Container Orchestration:** Kubernetes, ECS

## When to Use
- Large teams (> 10-20 engineers)
- Complex domain with clear bounded contexts
- Independent scaling requirements per service
- High deployment frequency needed

## When NOT to Use
- Small teams or early-stage startups
- Simple domains
- When you don't yet know your domain boundaries (premature decomposition)

---
