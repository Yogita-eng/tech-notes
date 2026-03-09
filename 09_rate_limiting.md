# Rate Limiting

## What Is It?
Rate limiting controls how many requests a client can make to a service within a given time window. It protects services from being overwhelmed by too many requests.

---

## Why Rate Limit?

- Prevent **DDoS and brute-force** attacks
- Ensure **fair resource usage** across all clients
- Protect **downstream services** from overload
- Enforce **API monetization tiers** (free: 100 req/min, paid: 10,000 req/min)
- Prevent **runaway clients** from degrading service for others

---

## Rate Limiting Algorithms

### 1. Token Bucket
- A bucket holds `N` tokens, refilled at a constant rate
- Each request consumes one token; if no tokens → reject
- **Allows bursts** up to bucket capacity, then smooths out
- ✅ Most flexible and widely used

```
Bucket capacity: 10 tokens
Refill rate: 2 tokens/second
Burst allowed: up to 10 requests instantly
```

### 2. Leaky Bucket
- Requests enter a queue (the "bucket") and are processed at a **constant, fixed rate**
- Excess requests that overflow the bucket are dropped
- ✅ Smooth, predictable output — good for shaping traffic

### 3. Fixed Window Counter
- Count requests in a fixed time window (e.g., 100 req/minute, reset at :00)
- ❌ **Boundary problem:** User can send 100 at 00:59 and 100 at 01:01 → 200 requests in 2 seconds

### 4. Sliding Window Log
- Track the **timestamp** of every request in a log
- On each request, count entries within the last N seconds
- ✅ Very accurate, ❌ High memory usage (stores every timestamp)

### 5. Sliding Window Counter
- Combine fixed window + weighted estimate from the previous window
- `count = current_window_count + (previous_window_count × overlap_ratio)`
- ✅ Low memory, accurate enough for most use cases

---

## Where to Implement

| Layer | Notes |
|-------|-------|
| **API Gateway** | Most common — centralized, before requests hit services |
| **Reverse Proxy (Nginx)** | `limit_req_zone` module |
| **Application layer** | Fine-grained per-endpoint control |
| **Redis** | Shared state across multiple app server instances |

> ⚠️ If you have multiple app servers, you need a **shared store (Redis)** for rate limiting — otherwise each server tracks independently and limits are multiplied.

---

## Rate Limit Identifiers

You can rate limit by:
- **IP address** — coarse, fails for shared IPs (NAT)
- **User ID / API key** — preferred for authenticated APIs
- **Endpoint** — different limits per route
- **Combination** — user + endpoint for most precision

---

## HTTP Response

When limit is exceeded, return:
```
HTTP 429 Too Many Requests

Headers:
  X-RateLimit-Limit: 100
  X-RateLimit-Remaining: 0
  X-RateLimit-Reset: 1700000060   ← Unix timestamp when limit resets
  Retry-After: 60
```

---

## Interview Tip

> "I'd implement rate limiting at the API Gateway using a token bucket algorithm, backed by Redis so the limit is shared across all app server instances. Each user's token count is stored as a Redis key with a TTL. If tokens are exhausted, we return HTTP 429 with a Retry-After header. For unauthenticated endpoints, I'd also add IP-based limiting to prevent abuse."
