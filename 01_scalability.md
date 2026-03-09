# Scalability

## What Is It?
Scalability is the ability of a system to handle a growing amount of load by adding resources. A system that can continuously evolve to support increasing work is **scalable**.

As a system grows, performance starts to degrade unless the architecture is adapted.

---

## Types of Scalability

| Type | Description | Example |
|------|-------------|---------|
| **Horizontal** | Add more machines | Add more app servers |
| **Vertical** | Upgrade existing machine | Add more RAM/CPU |
| **Diagonal** | Combination of both | Common in production |

---

## Key Metrics

- **RPS (Requests Per Second)** — how many requests the system handles
- **Concurrent Users** — simultaneous active users at any moment
- **Data Volume** — how much data the system stores and processes over time

---

## How to Achieve Scalability

- **Stateless services** — any server can handle any request; easier to replicate
- **Horizontal scaling + Load Balancers** — distribute traffic across many servers
- **Database sharding and replication** — split data across nodes
- **Caching** — serve frequently accessed data from fast in-memory stores
- **Async processing** — offload work to message queues so servers aren't blocked
- **CDNs** — serve static content from edge nodes close to users

---

## Scalability vs Performance

These are related but different:
- **Performance** = how fast a single request is handled
- **Scalability** = how well the system handles increasing load

A system can be performant at low load but not scalable (e.g., a single fast server that collapses under 10x traffic).

---

## Interview Tip

> "I'd design this as a stateless service behind a load balancer so we can scale horizontally by simply adding more instances. Database reads would be offloaded to read replicas and a Redis cache layer."
