# CDN (Content Delivery Network)

## What Is It?
A CDN is a **geographically distributed network of servers** (called edge nodes or Points of Presence — PoPs) that delivers content to users from the nearest server, reducing latency.

```
[User in India] → [CDN Edge Node, Mumbai] → fast response
[User in US]    → [CDN Edge Node, Virginia] → fast response
(vs. both going to origin server in one location)
```

---

## How It Works

1. User requests a resource (image, video, JS file, API response)
2. DNS routes request to the nearest CDN edge server
3. **Cache hit** → edge server responds immediately (no origin needed)
4. **Cache miss** → edge fetches from origin, caches it, responds to user
5. Subsequent users get it from the edge cache

---

## What CDNs Can Cache

| Content Type | Examples |
|-------------|---------|
| **Static assets** | Images, CSS, JS, fonts, HTML |
| **Video** | HLS/DASH segments for streaming |
| **API responses** | With short TTLs for semi-dynamic content |
| **Downloads** | PDFs, installers, large files |

---

## Benefits

| Benefit | How |
|---------|-----|
| **Reduced Latency** | Content served from servers geographically closer to user |
| **Reduced Origin Load** | Edge servers absorb most requests, origin handles misses only |
| **High Availability** | Content stays available even if origin is temporarily down |
| **DDoS Protection** | CDN absorbs and filters large traffic attacks at the edge |
| **Cost Savings** | Less bandwidth consumed at origin |
| **Global Scale** | Serve users worldwide without deploying global infrastructure |

---

## Push vs Pull CDN

| | Pull CDN | Push CDN |
|-|----------|----------|
| **How** | CDN fetches from origin on first request | You upload content to CDN proactively |
| **Best for** | Frequently accessed content that can be lazily cached | Large files, predictable content (videos, software releases) |
| **Effort** | Low — just configure origin | Higher — must push updates to CDN |
| **Example** | CloudFront (default), Cloudflare | Manual S3 + CloudFront push |

---

## Cache Invalidation
When origin content changes, CDN cache must be refreshed:
- **TTL expiry** — content expires after a set time (simplest)
- **Purge/Invalidation API** — explicitly tell CDN to clear specific paths
- **Cache-busting** — change the URL when content changes (e.g., `app.v2.js`)

---

## CDN + S3 Pattern (Very Common)
```
[Upload] → [S3 (origin)]
[User request] → [CloudFront CDN] → (cache miss) → [S3] → cache → return
```
S3 for durable storage, CDN for fast global delivery. Most media-heavy apps use this.

---

## Popular CDN Providers

| Provider | Known For |
|----------|----------|
| **AWS CloudFront** | Deep AWS integration |
| **Cloudflare** | Security + CDN + DNS, free tier |
| **Akamai** | Enterprise, largest edge network |
| **Fastly** | Instant cache purge, developer-friendly |
| **Azure CDN** | Microsoft cloud integration |

---

## Interview Tip

> "I'd put a CDN in front of all static assets and media files. Users in different regions get content from the nearest edge node, cutting latency from ~150ms to ~10ms. This also significantly reduces load on our origin servers. I'd use CloudFront backed by S3 for image/video storage."
