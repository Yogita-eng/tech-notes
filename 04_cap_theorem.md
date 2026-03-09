# CAP Theorem

## What Is It?
In a **distributed system**, you can only guarantee **two of the following three** properties simultaneously:

- **C — Consistency:** Every read returns the most recent write, or an error
- **A — Availability:** Every request gets a non-error response (data may not be the latest)
- **P — Partition Tolerance:** System continues operating despite network partitions (message drops/delays between nodes)

> **Network partitions are unavoidable** in real distributed systems. So in practice, you always have P — and must choose between **C** and **A**.

---

## CP vs AP Systems

| Type | Behavior During Partition | Examples |
|------|--------------------------|---------|
| **CP** | Returns error or times out rather than return stale data | HBase, Zookeeper, MongoDB (strong mode) |
| **AP** | Returns stale data but stays available | Cassandra, DynamoDB, CouchDB |

---

## Visualization

```
        C ─────────── CA (RDBMS, single node)
       / \
      /   \
    CP     AP
   /         \
  P ─────────── (pure partition tolerance, not useful alone)
```

Real distributed systems live on the CP or AP edge. "CA" is only possible if you can guarantee no partitions (single node, or same data center).

---

## When to Choose What

| Scenario | Choose |
|----------|--------|
| Banking, financial transactions | **CP** — wrong balance is worse than downtime |
| Inventory management | **CP** — overselling is catastrophic |
| Social media feeds | **AP** — slightly stale feed is fine |
| DNS | **AP** — availability over instant propagation |
| Shopping cart | **AP** — losing a cart item is minor |
| Authentication / login | **CP** — must see latest password/revocation |

---

## PACELC Extension

CAP only describes behavior *during a partition*. **PACELC** extends it:

> Even without partitions (normal operation), there's a tradeoff between **Latency (L)** and **Consistency (C)**.

- Systems like DynamoDB: PA/EL — available during partition, low latency normally
- Systems like Zookeeper: PC/EC — consistent during partition, consistent normally

---

## Common Misconceptions

- **"NoSQL = AP"** — Not always. MongoDB with write concern `majority` is CP.
- **"SQL = CP"** — Only if single node. Distributed SQL (CockroachDB) can be CP.
- **"You can't have CA"** — You can, if you never have partitions (not realistic at scale).

---

## Interview Tip

> "Since network partitions are inevitable in a distributed system, I need to decide between consistency and availability. For this use case [e.g. user balances], I'd choose CP — I'd rather return an error than show incorrect data. I'd use a CP database like Zookeeper-backed storage or a relational DB with synchronous replication."
