# Database Sharding

## What Is It?
Database sharding is a **horizontal scaling technique** that splits a large database into smaller, independent pieces called **shards**, each stored on a separate server.

```
Before sharding:
[Single DB — 3 million rows]

After sharding:
[Shard 1 — 1 million rows] [Shard 2 — 1 million rows] [Shard 3 — 1 million rows]
```

Each shard is an independent database with the same schema but different data.

---

## Why Shard?

- Single DB server hits its **storage or memory ceiling**
- **Write throughput** exceeds what one server can handle
- **Query performance** degrades with massive data volume
- Need to scale **beyond what vertical scaling** can offer

---

## Sharding Strategies

### 1. Hash-Based Sharding
`shard = hash(shard_key) % number_of_shards`

```
User ID 1001 → hash → shard 2
User ID 1002 → hash → shard 0
User ID 1003 → hash → shard 1
```

✅ Even data distribution  
❌ Range queries hit all shards  
❌ Adding shards requires remapping (use consistent hashing to mitigate)

### 2. Range-Based Sharding
Split by value ranges:
```
User IDs 1–1,000,000       → Shard 1
User IDs 1,000,001–2,000,000 → Shard 2
```

✅ Range queries stay on one shard  
❌ Hotspot risk — new users all go to the latest shard  
❌ Uneven distribution if data is skewed

### 3. Directory-Based Sharding
A lookup table (directory) maps each key to its shard:
```
user_id: 1001 → shard: 3
user_id: 1002 → shard: 1
```

✅ Flexible — easy to reassign keys to different shards  
❌ Directory becomes a bottleneck and single point of failure  
❌ Extra lookup per query

### 4. Geographic Sharding
Shard by user region:
```
US users → US shard
EU users → EU shard
Asia users → Asia shard
```

✅ Data locality — low latency for local users  
✅ Compliance (data residency laws)  
❌ Uneven distribution if user base is concentrated  

---

## Challenges of Sharding

| Challenge | Description |
|-----------|-------------|
| **Cross-shard queries** | JOINs across shards are expensive and complex |
| **Rebalancing** | Adding new shards requires migrating data |
| **Distributed transactions** | Cross-shard ACID transactions are very hard |
| **Hotspots** | Uneven data can overload one shard |
| **Schema changes** | Must apply to all shards |
| **Increased complexity** | App must know which shard to query |

---

## Sharding vs Partitioning

| | Partitioning | Sharding |
|-|-------------|---------|
| Scope | Within a single DB instance | Across multiple DB instances |
| Who manages it? | Usually the database engine | Application or middleware layer |
| Example | PostgreSQL table partitioning | Manually split across 5 DB servers |

---

## When to Shard

Only shard when you've exhausted these options first (in order):
1. **Vertical scaling** (bigger server)
2. **Read replicas** (scale read-heavy load)
3. **Caching** (reduce DB reads entirely)
4. **Query optimization + indexes**

Sharding adds significant complexity — it should be a last resort.

---

## Real-World Usage

- **Instagram** — sharded PostgreSQL by user_id
- **Uber** — sharded by geographic region
- **Discord** — sharded Cassandra by channel_id

---

## Interview Tip

> "If we hit the limits of a single database, I'd shard by user_id using hash-based sharding. Each shard handles a subset of users. Most queries (get user profile, get user posts) are scoped to a single user, so they'd hit only one shard. I'd keep shard count fixed initially and use consistent hashing so adding capacity later requires minimal remapping."
