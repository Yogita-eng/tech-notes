# Strong vs Eventual Consistency

## Quick Decision

```
Correctness is critical (money, inventory, auth)?   → Strong Consistency
Brief staleness acceptable (feeds, counts, DNS)?    → Eventual Consistency
```

---

## Strong Consistency
Every read returns the **most recent write**. All nodes see the same data at the same time.

- Once a write is acknowledged, all subsequent reads reflect it — everywhere
- Writes are synchronously replicated before acknowledgment
- **Tradeoff:** Higher latency, lower availability during network partitions

### When to Use
- Bank account balances
- E-commerce inventory (can't oversell)
- Authentication / session revocation
- Flight/hotel booking (no double-booking)
- Distributed locking / leader election

### Systems
PostgreSQL, MySQL, Zookeeper, HBase, Google Spanner

---

## Eventual Consistency
Given enough time with no new updates, all nodes will **eventually converge** to the same value.

- Reads may return stale data temporarily
- Writes are asynchronously propagated to replicas
- **Tradeoff:** Lower latency, higher availability

### When to Use
- Social media likes/view counts (off by a few is fine)
- News feed (slightly stale post order is OK)
- DNS propagation
- Product recommendations
- Shopping cart (Amazon Dynamo paper)
- Email delivery

### Systems
Cassandra, DynamoDB, CouchDB, Redis (async replication)

---

## Other Consistency Models (Between the Two Extremes)

| Model | Description | Example |
|-------|-------------|---------|
| **Linearizability** | Strongest real-time ordering | Zookeeper operations |
| **Sequential Consistency** | All nodes see ops in same order | |
| **Causal Consistency** | Related ops appear in correct order | MongoDB causally consistent sessions |
| **Read-your-writes** | You always see your own writes | Most SQL DBs |
| **Monotonic Reads** | You never see data go backwards | |
| **Bounded Staleness** | Reads lag behind by at most X time | Azure Cosmos DB option |

---

## The Tradeoff Visualized

```
Strong ←──────────────────────────────→ Eventual
  ↑                                         ↑
Higher latency                       Higher availability
Lower availability                   Lower latency
Simpler app logic                    Complex conflict resolution
```

---

## Conflict Resolution in Eventual Consistency

When two nodes accept conflicting writes, you need a resolution strategy:
- **Last Write Wins (LWW):** Most recent timestamp wins (simple, may lose data)
- **Vector Clocks:** Track causal history, detect conflicts precisely
- **CRDTs (Conflict-free Replicated Data Types):** Mathematically mergeable data structures (counters, sets)
- **Application-level merge:** Let the application define how to merge conflicts

---

## Interview Tip
> "For payment and inventory data, I'd use strong consistency — I can't show a user their balance is $500 when another transaction just spent it. For the activity feed and notification counts, eventual consistency is fine — if someone's follower count is off by one for a few seconds, that's completely acceptable. Different parts of the same system can have different consistency requirements."
