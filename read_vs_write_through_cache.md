# Read-Through vs Write-Through Cache

## Quick Decision

```
Read-heavy, lazily cache what's needed?        → Cache-Aside or Read-Through
Write-heavy, cache must stay in sync?          → Write-Through
Write-heavy, can tolerate brief data loss?     → Write-Back
```

---

## Cache-Aside (Most Common)
Application manually manages cache. Check cache first; on miss, load from DB and populate cache.

```
READ:
  1. App checks cache for key
  2. Cache HIT  → return cached value
  3. Cache MISS → app reads from DB → app writes to cache → return

WRITE:
  1. App writes to DB
  2. App deletes (invalidates) the cache key
  3. Next read will repopulate cache from DB
```

### Pros
- App has full control over what gets cached
- Cache failure doesn't break the app (falls through to DB)
- Only caches data that is actually read
- Most widely used pattern

### Cons
- First request after a cache miss is slow (cold start)
- Risk of stale data between write and cache invalidation
- Cache code scattered throughout application logic

### Best For
- General-purpose caching
- Read-heavy systems
- When different data needs different caching logic

---

## Read-Through Cache
Cache sits transparently between app and DB. App always reads from cache; cache handles DB lookups on miss.

```
READ:
  App → Cache → (HIT) return
               (MISS) Cache fetches from DB → stores → returns to App
```

### Pros
- Transparent to application — same interface as DB
- Cache always populated on first read

### Cons
- Cold start misses on first access
- Less control — harder to customize caching logic

### Best For
- ORM/framework-level caching
- When you want caching without changing application code

---

## Write-Through Cache
Every write goes to **both cache and DB simultaneously**. Write is only complete when both succeed.

```
WRITE:
  App → writes to Cache → Cache writes to DB → both ACK → return to App
```

### Pros
- Cache is always up-to-date with DB
- No stale reads after writes
- Good read performance (cache always warm)

### Cons
- Higher write latency (must wait for both cache and DB)
- Cache filled with data that may never be read (wasted memory)
- Not helpful for write-heavy, read-light data

### Best For
- Systems where data is written and then read frequently
- When consistency between cache and DB is required

---

## Write-Back (Write-Behind) Cache
Write to cache first; DB updated **asynchronously later** in batches.

```
WRITE:
  App → writes to Cache (immediate ACK) ... (async after delay) → DB updated
```

### Pros
- Fastest write performance
- Reduces DB write pressure (batches updates)
- Great for write-heavy workloads

### Cons
- **Risk of data loss** if cache fails before DB flush
- Eventual consistency between cache and DB
- Complex to implement correctly

### Best For
- Write-heavy workloads where small data loss is acceptable
- High-throughput write scenarios (logging, counters)
- IoT data ingestion

---

## Side-by-Side Summary

| Strategy | Read Source | Write Flow | Consistency | Write Speed | Complexity |
|----------|-------------|------------|-------------|-------------|------------|
| **Cache-Aside** | Cache, then DB | App → DB, invalidate cache | Eventual | Normal | Low |
| **Read-Through** | Always cache | App → DB directly | Eventual | Normal | Low |
| **Write-Through** | Always cache | App → Cache → DB | Strong | Slower | Medium |
| **Write-Back** | Always cache | App → Cache, async → DB | Eventual | Fastest | High |

---

## Interview Tip
> "I'd use Cache-Aside for most reads — check Redis first, fall back to PostgreSQL on miss, and cache the result with a 5-minute TTL. For writes, I'd invalidate the cache key immediately after the DB write. For high-frequency counters like view counts, I'd use Write-Back — increment in Redis atomically and flush to the DB in batches every 30 seconds. The performance gain is worth the slight delay in DB persistence."
