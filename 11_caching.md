# Caching

## What Is It?
Caching temporarily stores copies of data in a **fast, high-speed storage layer** to reduce the time taken to access data and decrease load on the primary data store.

```
App → [Cache (Redis)] → hit? return fast
                     → miss? fetch from DB → store in cache → return
```

---

## Why Cache?

- Reduce **latency** — RAM access is ~100ns vs disk/DB at ~10ms
- Reduce **database load** — serve repeated reads from cache, not DB
- Improve **throughput** — cache can handle far more RPS than a DB
- **Cost savings** — DB queries are expensive; cache reads are cheap

---

## Caching Strategies

### 1. Cache-Aside (Lazy Loading) — Most Common
Application controls caching logic:
```
READ:  check cache → miss → read from DB → write to cache → return
WRITE: write to DB → invalidate or update cache
```
✅ Only caches what's actually needed  
✅ Cache failure doesn't break the app (falls back to DB)  
❌ First request always hits DB (cold start miss)  
❌ Stale data risk if DB is updated without cache invalidation  

### 2. Read-Through Cache
Cache sits in front of DB; **cache itself** fetches from DB on miss:
```
App always reads from cache → cache handles DB lookup on miss
```
✅ Transparent to application  
❌ Cold start misses  

### 3. Write-Through Cache
Every write goes to **both cache and DB simultaneously**:
```
App writes → cache + DB updated together → ACK returned
```
✅ Cache always in sync with DB  
❌ Write latency is higher (must wait for both)  
❌ Cache fills with data that may never be read  

### 4. Write-Back (Write-Behind)
Write to cache first, DB updated **asynchronously later**:
```
App writes → cache updated immediately → DB updated in background
```
✅ Fastest write performance  
❌ Risk of data loss if cache fails before DB flush  
❌ Complex to implement correctly  

---

## Cache Eviction Policies

When cache is full, which item gets removed?

| Policy | How It Works | Best For |
|--------|-------------|---------|
| **LRU** (Least Recently Used) | Remove item not accessed for longest time | General-purpose — most common |
| **LFU** (Least Frequently Used) | Remove item with lowest access count over time | Popularity-based access patterns |
| **FIFO** | Remove the oldest added item regardless of usage | Simple queues |
| **TTL** (Time-to-Live) | Auto-expire after a set duration | Time-sensitive data |
| **Random** | Remove a random item | Simple, very low overhead |

---

## Cache Invalidation (The Hard Problem)

> *"There are only two hard things in Computer Science: cache invalidation and naming things."* — Phil Karlton

Approaches:
- **TTL-based:** Data expires after N seconds — simple but may serve stale data briefly
- **Event-driven:** Invalidate cache key when underlying data changes (requires coordination)
- **Versioning:** Embed version/hash in cache key — old key naturally expires

---

## What to Cache

✅ User sessions and auth tokens  
✅ User profile data (read far more than written)  
✅ Computed/aggregated values (follower count, total price)  
✅ DB query results for popular queries  
✅ Rendered page fragments  
✅ API responses from external services  

❌ Rapidly changing data (real-time stock prices)  
❌ User-specific sensitive data without proper scoping  
❌ Very large objects (check memory limits)  

---

## Redis vs Memcached

| | **Redis** | **Memcached** |
|-|-----------|---------------|
| Data structures | Strings, Lists, Sets, Hashes, Sorted Sets | Strings only |
| Persistence | Optional (AOF / RDB snapshots) | None |
| Pub/Sub | ✅ Yes | ❌ No |
| Clustering | ✅ Built-in | Manual sharding only |
| Best for | Complex caching, queues, leaderboards, sessions | Simple high-throughput key-value cache |

**Redis is the default choice** for most use cases today.

---

## Caching Layers

```
Browser Cache
    ↓
CDN Cache (static assets, API responses)
    ↓
Reverse Proxy Cache (Nginx, Varnish)
    ↓
Application Cache (Redis / Memcached)
    ↓
Database Query Cache
    ↓
Database (source of truth)
```

---

## Interview Tip

> "I'd add Redis as a cache in front of the database using Cache-Aside strategy. The top 20% of data (hot users, trending posts) would cover ~80% of reads. I'd set TTLs appropriate to how frequently data changes — short for dynamic data like counts, longer for stable data like user profiles. Cache invalidation on write would be handled by deleting the key, so the next read repopulates it fresh."
