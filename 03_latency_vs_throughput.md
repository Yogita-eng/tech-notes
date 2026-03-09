# Latency vs Throughput

## Latency

**Definition:** Time it takes for a single request or operation to complete (response time).

- Measured in: **milliseconds (ms)** or microseconds (µs)
- Low latency = fast, responsive system
- High latency = lag, poor user experience

### Latency Reference Numbers (Memorize These)

| Operation | Typical Latency |
|-----------|-----------------|
| L1 Cache read | ~0.5 ns |
| L2 Cache read | ~7 ns |
| RAM read | ~100 ns |
| SSD random read | ~100 µs |
| HDD random read | ~10 ms |
| Network (same data center) | ~0.5 ms |
| Network (cross-continent) | ~150 ms |

---

## Throughput

**Definition:** Amount of work done or data processed per unit of time.

- Measured in: **RPS** (Requests per second), **TPS** (Transactions per second), or **MB/s**
- High throughput = system handles many operations efficiently

---

## Key Relationship

> **Latency and throughput often trade off against each other.**

- **Batching** increases throughput but increases latency per item
- **Parallelism** can improve both, up to a point
- **Caching** reduces latency and can improve throughput simultaneously

### Little's Law
```
Throughput = Concurrency / Latency
```
If latency doubles and concurrency stays the same, throughput halves.

---

## How to Improve Each

| Goal | Techniques |
|------|------------|
| **Reduce Latency** | Caching, CDN, faster DB queries, connection pooling, co-location |
| **Increase Throughput** | Horizontal scaling, async processing, batching, load balancing |

---

## Latency Targets by Use Case

| Use Case | Target |
|----------|--------|
| Search results | < 100 ms |
| Social feed load | < 500 ms |
| Video start time | < 2 s |
| Payment processing | < 3 s |
| Background jobs | Minutes acceptable |

---

## Interview Tip

> "This system is read-heavy, so I'd focus on reducing latency for reads by adding a Redis cache in front of the database and serving static assets via CDN. Throughput for writes can be improved by making them asynchronous via a message queue."
