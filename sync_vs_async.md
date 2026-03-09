# Synchronous vs Asynchronous Processing

## Quick Decision

```
Need result immediately to proceed?        → Synchronous
Long-running task / can notify later?     → Asynchronous
Decoupling services / traffic spikes?     → Asynchronous (message queue)
```

---

## Synchronous Processing
Caller **waits** for the operation to complete before continuing.

```
Client → Request → [Server processes] → Response → Client continues
         (client blocked the entire time)
```

### Pros
- Simple — easy to understand and debug
- Immediate feedback (success or error)
- Natural for sequential, ordered operations
- Easier error handling (try/catch flows naturally)

### Cons
- Caller is blocked — poor resource utilization
- Cascading slowdowns — if Service B is slow, Service A is slow
- Hard to handle traffic spikes (no buffering)

### Use When
- Result is needed immediately to continue the flow
- Operations are fast (< 100ms)
- Simple request/response (DB read, simple API call)

---

## Asynchronous Processing
Caller submits work and **continues immediately**. Result arrives later.

```
Client → Request → Immediate ACK → Client continues
                   [Server processes in background]
                   → Notifies client when done (callback/event/polling)
```

### Pros
- Non-blocking — better resource utilization
- Higher throughput
- Handles traffic spikes (queue absorbs load)
- Decouples services — producer doesn't depend on consumer availability

### Cons
- More complex — callbacks, retries, idempotency
- Harder to debug and trace
- Client must handle delayed results
- Need to handle duplicate delivery

### Use When
- Long-running operations (video transcoding, email sending, report generation)
- Tasks that don't require immediate result
- Decoupling microservices
- Background jobs

---

## Async Patterns

| Pattern | How | Example |
|---------|-----|---------|
| **Message Queue** | Drop job in queue, worker picks it up | Order placed → queue → send email |
| **Webhooks** | Server calls back your URL when done | Stripe calls your webhook on payment |
| **Polling** | Client checks repeatedly for result | `GET /jobs/123/status` every 5s |
| **Long Polling** | Client waits for server to respond with result | Notification systems |
| **WebSockets** | Server pushes result when ready | Chat, live updates |

---

## Interview Tip
> "When a user places an order, I don't want the API to wait for the email confirmation to send before responding. I'd drop an `OrderPlaced` event onto a Kafka topic and immediately return `202 Accepted` to the user. The email service consumes from Kafka independently — if it's slow or down, orders still succeed, and emails are eventually sent."
