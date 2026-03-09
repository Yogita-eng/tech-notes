# 1. Client-Server Architecture

## Overview
The most fundamental architecture: a **client** makes requests and a **server** processes them and returns responses.

```
[Client] ←→ [Network/Internet] ←→ [Server]
```

## Components
- **Client:** User-facing interface (browser, mobile app, desktop app). Sends requests, displays results.
- **Server:** Processes requests, manages resources (DB, files, business logic), sends responses.

## Common Variants

### 2-Tier
```
[Client] ←→ [Server + Database]
```
- Simple, fast for small apps
- Tight coupling between server and DB

### 3-Tier (Most Common)
```
[Client / Presentation] ←→ [Application Server / Logic] ←→ [Database / Data]
```
- Better separation of concerns
- Scales tiers independently

### N-Tier
Multiple layers with caching, message queues, CDN, etc. added between.

## Pros & Cons
| Pros | Cons |
|------|------|
| Simple mental model | Server is single point of failure |
| Centralized control | Can bottleneck at server |
| Easy to secure | Scalability requires extra effort |
| Clear separation | |

## Best For
Web apps, mobile backends, enterprise applications — essentially everything starts here.

---
