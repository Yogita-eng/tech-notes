# Message Queues

## What Is It?
A message queue is an **asynchronous communication mechanism** where producers send messages to a queue, and consumers read and process them independently — without needing to be available at the same time.

```
[Producer] → [Message Queue / Broker] → [Consumer]
```

---

## Why Use Message Queues?

| Problem | How Queues Solve It |
|---------|-------------------|
| **Tight coupling** | Producer/consumer don't need to know about each other |
| **Traffic spikes** | Queue buffers requests — consumers process at their own pace |
| **Slow processing** | Long tasks run in background without blocking the user |
| **Reliability** | If consumer crashes, message stays in queue for retry |
| **Scalability** | Add more consumers to increase throughput |

---

## Core Concepts

| Term | Meaning |
|------|---------|
| **Producer** | Sends messages to the queue |
| **Consumer** | Reads and processes messages from the queue |
| **Broker** | The middleware that manages the queue (e.g., Kafka, RabbitMQ) |
| **Topic / Queue** | Named channel where messages are stored |
| **ACK (Acknowledgment)** | Consumer confirms successful processing |
| **Dead Letter Queue (DLQ)** | Stores messages that failed after max retries |

---

## Queue vs Pub/Sub

| | **Queue (Point-to-Point)** | **Pub/Sub (Fan-out)** |
|-|---------------------------|----------------------|
| Consumers per message | One | Many |
| Message deleted after? | After one consumer reads it | After all subscribers read it |
| Use case | Task distribution | Broadcasting events |
| Example | AWS SQS, RabbitMQ | Kafka, Google Pub/Sub, AWS SNS |

---

## Delivery Guarantees

| Guarantee | Meaning | Tradeoff |
|-----------|---------|---------|
| **At-most-once** | Delivered 0 or 1 time | May lose messages |
| **At-least-once** | Delivered 1 or more times | May get duplicates — consumers must be idempotent |
| **Exactly-once** | Delivered exactly once | Hardest to achieve, most expensive |

In practice, **at-least-once + idempotent consumers** is the most common approach.

---

## Popular Message Queue Tools

| Tool | Type | Best For |
|------|------|---------|
| **Apache Kafka** | Distributed log / streaming | High-throughput event streaming, event sourcing, replay |
| **RabbitMQ** | Traditional message broker | Complex routing, task queues, low-medium throughput |
| **AWS SQS** | Managed queue | Simple, reliable queuing on AWS — no infra to manage |
| **AWS SNS** | Managed pub/sub | Fan-out notifications to many subscribers |
| **Redis Streams** | Lightweight streaming | Real-time, low-latency, already using Redis |
| **Google Pub/Sub** | Managed pub/sub | GCP-native, global scale |

---

## When to Use a Message Queue

✅ Sending emails, SMS, push notifications after an action  
✅ Processing uploaded files/images asynchronously  
✅ Charging payments after order confirmation  
✅ Feeding analytics events without slowing the main flow  
✅ Decoupling microservices  
✅ Absorbing traffic spikes (e.g., flash sale orders)  

---

## Kafka Deep Dive (Most Common in Interviews)

```
[Producers] → [Kafka Topic (partitioned)] → [Consumer Group]

Topic "orders":
  Partition 0: msg1, msg4, msg7...
  Partition 1: msg2, msg5, msg8...
  Partition 2: msg3, msg6, msg9...
```

- Messages stored durably on disk (can replay history)
- Consumers track their own **offset** (position in the log)
- Scale throughput by adding partitions
- Consumer groups allow parallel processing

---

## Interview Tip

> "I'd use a message queue between the API server and the notification service. When a user places an order, the API immediately acknowledges the request and drops an event onto the queue. The notification worker consumes from the queue and sends the email — this way, a slow email provider doesn't slow down order placement. If the worker crashes, the message stays in the queue and is retried automatically."
