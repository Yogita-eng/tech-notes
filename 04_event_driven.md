# 4. Event-Driven Architecture

## Overview
System components communicate through **events** — records of something that happened. Components are decoupled; producers don't know about consumers.

```
[Producer] → [Event Bus/Message Broker] → [Consumer 1]
                                        → [Consumer 2]
                                        → [Consumer N]
```

## Core Concepts
- **Event:** Immutable record of something that happened (e.g., `OrderPlaced`, `PaymentProcessed`)
- **Producer (Publisher):** Emits events when something happens
- **Consumer (Subscriber):** Reacts to events of interest
- **Event Bus / Broker:** Kafka, RabbitMQ, AWS EventBridge, SNS

## Event Types
- **Domain Events:** Business events (`UserRegistered`, `OrderShipped`)
- **Integration Events:** Events shared between bounded contexts/services
- **Command Events:** Tell another service to do something (`SendEmail`)

## Patterns

### Pub/Sub (Publish-Subscribe)
- Producers publish to a **topic**
- Multiple consumers subscribe to the topic
- Fan-out: one event → many consumers

### Event Streaming
- Events stored in a **durable log** (Kafka)
- Consumers can replay history
- Enables event sourcing

## Benefits
- **Loose coupling:** Producers don't know about consumers
- **Scalability:** Consumers can scale independently
- **Resilience:** Events buffered in queue if consumer is down
- **Auditability:** Event log acts as audit trail
- **Easy to add new consumers** without touching producers

## Challenges
- **Eventual consistency** — consumers process asynchronously
- **Ordering** — hard to guarantee global event order
- **Debugging** — tracing event flows across services is complex
- **Duplicate events** — consumers must handle idempotently
- **Schema evolution** — changing event schemas without breaking consumers

## When to Use
- Microservices communication (async preferred over sync)
- Real-time data pipelines
- Audit logging
- Multi-consumer notifications
- Decoupling services that change independently

## Real-World Example (E-commerce order flow)
```
OrderService → OrderPlaced event
    → InventoryService (reserve stock)
    → PaymentService (charge card)
    → EmailService (send confirmation)
    → AnalyticsService (record conversion)
```
All triggered from one event, completely decoupled.

---
