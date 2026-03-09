# 1. Design a URL Shortener (TinyURL)

## Functional Requirements
- Generate a unique short URL for a given long URL
- Redirect users to the original URL via short URL
- Optional: Custom aliases, link expiration, click analytics

## Non-Functional Requirements
- High availability (99.9%+)
- Low latency redirects (< 10ms)
- Scalable to millions of URLs per day
- Links should persist for years (durability)

## Capacity Estimation
```
Writes: 100M URLs/day → ~1,160 writes/sec
Reads: 10:1 read ratio → 1B redirects/day → ~11,600 reads/sec
Storage: 100M × 500 bytes = 50 GB/day → ~18 TB/5 years
Cache: 20% of daily reads in memory → ~100 GB RAM
```

## High-Level Design
```
[Client] → [Load Balancer] → [URL Shortener Service]
                                      ↓             ↓
                               [Cache (Redis)]   [Database]
                                                 [Analytics Queue (Kafka)]
```

## Key Design Decision: Short URL Generation

### Option 1: MD5/SHA256 Hash (take first 6-8 chars)
- Hash the long URL → take first 7 characters
- ❌ Collisions: different long URLs can produce same short code
- Need collision detection + retry logic

### Option 2: Base62 Encoding of Auto-Increment ID
- DB generates sequential ID → encode to Base62 (a-z, A-Z, 0-9)
- 7 chars × Base62 = 62^7 = 3.5 trillion unique URLs
- ✅ No collisions, ✅ predictable length
- ❌ Sequential IDs are guessable (security concern for private URLs)

### Option 3: Distributed ID Generator (Recommended)
- Use Snowflake IDs (Twitter) or ULID for unique IDs across servers
- Encode to Base62 for short code
- ✅ No collisions, ✅ not guessable, ✅ scalable

## Database Schema
```sql
urls:
  id          BIGINT PRIMARY KEY
  short_code  VARCHAR(7) UNIQUE INDEX
  long_url    TEXT
  user_id     BIGINT
  created_at  TIMESTAMP
  expires_at  TIMESTAMP (nullable)

clicks:
  id          BIGINT PRIMARY KEY
  short_code  VARCHAR(7) INDEX
  user_agent  TEXT
  ip_address  VARCHAR(45)
  country     VARCHAR(2)
  clicked_at  TIMESTAMP
```

## Redirect Flow
1. Client requests `GET /abc123`
2. Check Redis cache for `abc123`
3. Cache hit → 301/302 redirect to long URL
4. Cache miss → query DB, cache result, redirect

## 301 vs 302 Redirect
- **301 (Permanent):** Browser caches redirect → reduces server load but you lose click analytics
- **302 (Temporary):** Every click hits your server → full analytics ✅

## Scaling Deep Dive
- **Read scaling:** Redis cache in front of DB (99% cache hit rate expected)
- **Write scaling:** Single DB is fine for 1,160 writes/sec; shard by short_code hash if needed
- **Analytics:** Async write to Kafka, consumer writes to analytics DB (don't slow redirects)

---
