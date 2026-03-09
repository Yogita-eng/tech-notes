# System Design Interview Template

A structured, step-by-step framework for approaching any system design interview question. Use this as your mental checklist.

> **Golden Rule:** Think out loud. Interviewers evaluate your thought process, not just the final design.

---

## The 7-Step Framework

| Step | Action | Time (45-min interview) |
|------|--------|------------------------|
| 1 | Clarify Requirements | 5–7 min |
| 2 | Capacity Estimation | 3–5 min |
| 3 | High-Level Design | 10–12 min |
| 4 | Database Design | 5–7 min |
| 5 | API Design | 3–5 min |
| 6 | Deep Dive | 10–15 min |
| 7 | Address Key Concerns | 5 min |

---

## Step 1: Clarify Requirements

**Never start designing without understanding what you're building.** Ask clarifying questions first.

### Functional Requirements
These define **what the system does**.

Questions to ask:
- What are the **core features** the system must support?
- Who are the **users**? (customers, internal teams, developers via API)
- How will users **interact** with the system? (web, mobile, API, CLI)
- What are the key **data types**? (text, images, video, structured data)
- Any **third-party integrations** required? (payment gateways, auth providers, maps)
- What's **out of scope** for this design?

### Non-Functional Requirements
These define **how well the system performs**.

Questions to ask:
- **Read vs Write ratio?** Is it read-heavy (10:1) or write-heavy?
- **Availability?** Can the system have downtime, or must it be 99.99% available?
- **Latency targets?** Is < 100ms response time required?
- **Consistency?** Is eventual consistency acceptable, or is strong consistency needed?
- **Scalability?** How many users? What's the expected growth?
- **Durability?** Can we lose any data, or must everything be persisted?
- **Security?** Authentication, authorization, encryption requirements?
- **Rate limiting?** Prevent API abuse?
- **Geography?** Global users? Multi-region needed?

### Example Clarifications (URL Shortener)
```
Q: Should URLs expire?
Q: Can users customize their short URLs?
Q: Do we need analytics (click tracking)?
Q: How many URLs shortened per day?
Q: Do we need to handle redirects for deleted URLs?
```

---

## Step 2: Capacity Estimation

Estimate the **scale** of the system to make informed design decisions. Check with your interviewer if they want this — some skip it.

### Key Estimates to Make

#### Traffic
```
Daily Active Users (DAU): X million
Writes per day:    X * write_ratio
Reads per day:     X * read_ratio

RPS (writes) = writes_per_day / 86,400
RPS (reads)  = reads_per_day  / 86,400
Peak RPS     = average * 2-3x (traffic spikes)
```

#### Storage
```
Storage per record: estimate fields × data types
Daily storage = writes_per_day × storage_per_record
5-year storage = daily_storage × 365 × 5
```

#### Memory (Cache)
```
# Cache 20% of data (80/20 rule — 20% of data serves 80% of reads)
Cache needed = storage × 0.20
```

#### Bandwidth
```
Ingress = RPS × average_request_size
Egress  = read_RPS × average_response_size
```

### Quick Reference Numbers
| Unit | Value |
|------|-------|
| 1 day in seconds | 86,400 (~100K) |
| 1 month in seconds | 2.6 million |
| 1 year in seconds | 31.5 million |
| 1 KB | 10³ bytes |
| 1 MB | 10⁶ bytes |
| 1 GB | 10⁹ bytes |
| 1 TB | 10¹² bytes |
| 1 PB | 10¹⁵ bytes |

### Example (Twitter-like system)
```
DAU: 300 million
Tweets/user/day: 2
Total tweets/day: 600 million
RPS (write): ~7,000
RPS (read): ~350,000 (assuming 50:1 read:write ratio)

Tweet size: ~300 bytes
Storage/day: 600M × 300B = 180 GB/day
Storage/5yr: 180GB × 365 × 5 = ~330 TB
```

---

## Step 3: High-Level Design

Draw a **block diagram** showing the major components and how they connect.

### Core Components Checklist

```
                        ┌─────────────────────────────────────────┐
                        │          High-Level Architecture         │
                        └─────────────────────────────────────────┘

[Clients: Web/Mobile] 
        ↓
[CDN] (static assets)
        ↓
[API Gateway / Load Balancer]
        ↓
[Application Servers]
    ↙         ↘
[Cache]    [Message Queue]
    ↓            ↓
[Database]  [Worker Services]
    ↓
[Storage: S3/Blob]
```

### Components to Consider

| Component | When to Include |
|-----------|-----------------|
| **CDN** | Static assets, global users, media files |
| **Load Balancer** | Multiple servers, high availability |
| **API Gateway** | Auth, rate limiting, routing, SSL termination |
| **Application Servers** | Business logic processing |
| **Cache (Redis)** | Frequent reads, session storage, rate limiting |
| **Database (SQL/NoSQL)** | Persistent data storage |
| **Message Queue (Kafka/SQS)** | Async processing, service decoupling |
| **Object Storage (S3)** | Files, images, videos, large blobs |
| **Search (Elasticsearch)** | Full-text search |
| **Worker Services** | Background jobs, consumers |

### Draw While Explaining
- Start with **user → system entry point**
- Add components for each major functional requirement
- Show data flow with arrows
- Label technologies (e.g., "Redis for cache", "PostgreSQL for DB")

---

## Step 4: Database Design

### Part A: Data Modeling

Identify key **entities**, their **attributes**, and **relationships**.

1. List core entities (nouns in the requirements)
2. Define attributes for each entity
3. Identify relationships (one-to-many, many-to-many)
4. Identify primary keys and foreign keys
5. Consider normalization vs denormalization

**Example (Instagram):**
```
Users:    user_id, username, email, created_at
Posts:    post_id, user_id, image_url, caption, created_at
Follows:  follower_id, followee_id, created_at
Likes:    like_id, user_id, post_id, created_at
Comments: comment_id, user_id, post_id, content, created_at
```

### Part B: Choose the Right Database

Ask yourself:

| Question | If Yes → |
|----------|---------|
| Need ACID transactions? | SQL (PostgreSQL, MySQL) |
| Complex joins and relations? | SQL |
| Flexible/variable schema? | NoSQL Document (MongoDB) |
| Simple key-value lookups? | Redis, DynamoDB |
| Massive scale, wide rows? | Cassandra |
| Full-text search? | Elasticsearch |
| Graphs / relationships? | Neo4j |
| Time-stamped metrics? | InfluxDB, TimescaleDB |
| Cache layer? | Redis, Memcached |

### Part C: Schema Design Tips
- Use **UUIDs** or **distributed IDs** (Snowflake IDs) instead of auto-increment for distributed systems
- Add **created_at / updated_at** timestamps to all tables
- Consider **soft deletes** (`deleted_at` column) instead of hard deletes
- Index columns used in **WHERE**, **JOIN**, **ORDER BY** clauses
- Consider **denormalization** for read-heavy tables

---

## Step 5: API Design

Define the interfaces clients use to interact with the system.

### Choose API Style
- **REST:** Most common, resource-based, HTTP methods
- **GraphQL:** When clients need flexible queries
- **gRPC:** Internal service-to-service, high performance
- **WebSockets:** Real-time bidirectional (chat, live updates)

### API Design Checklist
- [ ] List the core endpoints based on functional requirements
- [ ] Define request/response formats
- [ ] Consider authentication (API key, JWT, OAuth)
- [ ] Consider versioning (`/v1/users`)
- [ ] Define error responses (HTTP status codes)
- [ ] Consider pagination for list endpoints

### Example API (URL Shortener)

```
POST /api/v1/urls
Request:  { "long_url": "https://example.com/very/long/path", "expiry": "2025-01-01" }
Response: { "short_url": "https://tny.io/abc123", "created_at": "2024-01-01" }

GET /api/v1/urls/{short_code}
Response: 301 Redirect to long_url

GET /api/v1/urls/{short_code}/analytics
Response: { "clicks": 1543, "unique_visitors": 820, "countries": [...] }

DELETE /api/v1/urls/{short_code}
Response: 204 No Content
```

### Authentication Patterns
- **API Key:** Simple, for server-to-server
- **JWT (JSON Web Token):** Stateless, for user auth in REST APIs
- **OAuth 2.0:** Delegated authorization (login with Google)
- **Session Cookies:** Traditional server-side sessions

---

## Step 6: Deep Dive into Key Components

The interviewer will guide this. Pick 1-2 areas to go deep on based on what's most interesting/complex.

### Common Deep Dive Areas

#### Database Scaling
- **Replication:** Primary/Replica setup — reads from replicas, writes to primary
- **Sharding:** Horizontal partitioning — hash-based or range-based
- **Connection Pooling:** PgBouncer, HikariCP
- **Query Optimization:** Indexes, EXPLAIN ANALYZE, query rewrites

#### Caching Strategy
- Where to cache: CDN, reverse proxy, application, database query cache
- What to cache: Hot data (top 20% serving 80% of reads)
- Cache invalidation strategy: TTL, event-driven invalidation, write-through
- Cache eviction: LRU, LFU

#### Real-Time Features
- WebSockets for bidirectional real-time communication
- Kafka/Redis Pub/Sub for server-side fan-out
- Connection management at scale (1M+ concurrent WebSocket connections)

#### File Upload/Storage
- Client uploads to S3 directly via **pre-signed URLs** (bypass your servers)
- CDN in front of S3 for fast global delivery
- Generate thumbnails/transcode async via Lambda or worker queue

#### Search
- Elasticsearch for full-text search
- Index denormalized documents
- Sync from primary DB via CDC (Change Data Capture) or events

#### Feed Generation
- **Pull model:** User requests feed, compute on-the-fly (fresh, expensive)
- **Push model:** Pre-compute and push to user's feed cache (fast, expensive storage)
- **Hybrid:** Push for regular users, pull for celebrities with millions of followers

---

## Step 7: Address Key Concerns

Proactively bring up and address potential issues.

### Scalability
- [ ] Stateless application servers (horizontal scaling)
- [ ] Database read replicas for read-heavy load
- [ ] Caching to reduce DB load
- [ ] Database sharding for write scalability
- [ ] CDN for global static content
- [ ] Autoscaling groups for traffic spikes

### High Availability
- [ ] Eliminate single points of failure
- [ ] Deploy across multiple Availability Zones
- [ ] Health checks + automatic failover
- [ ] Database replication (primary + standby)
- [ ] Load balancer redundancy

### Fault Tolerance
- [ ] Circuit breakers to prevent cascading failures
- [ ] Retry logic with exponential backoff
- [ ] Dead letter queues for failed messages
- [ ] Graceful degradation (serve reduced functionality)
- [ ] Bulkhead pattern (isolate failures)

### Data Consistency
- [ ] Define consistency requirements (strong vs eventual)
- [ ] Handle distributed transactions (Saga pattern if needed)
- [ ] Idempotent operations (safe to retry)
- [ ] Conflict resolution for concurrent writes

### Security
- [ ] Authentication (who are you?)
- [ ] Authorization (what can you do?)
- [ ] Input validation (prevent injection attacks)
- [ ] Rate limiting (prevent abuse)
- [ ] Data encryption at rest and in transit (TLS/HTTPS)
- [ ] Secrets management (don't hardcode credentials)

### Monitoring & Observability
- [ ] Metrics: latency, error rate, throughput (RED metrics)
- [ ] Logging: structured logs, correlation IDs for distributed tracing
- [ ] Tracing: Jaeger, AWS X-Ray for request paths through services
- [ ] Alerting: PagerDuty, OpsGenie on SLO violations

---

## Quick Reference Card

### Numbers Every Engineer Should Know

| Metric | Value |
|--------|-------|
| Availability 99.9% downtime/year | 8.76 hours |
| Availability 99.99% downtime/year | 52 minutes |
| Seconds in a day | 86,400 |
| Average tweet size | ~300 bytes |
| Average photo (compressed) | ~300 KB |
| Average web page | ~2 MB |
| RAM read | ~100 nanoseconds |
| SSD random read | ~100 microseconds |
| Network within same DC | ~0.5 milliseconds |
| Network US → Europe | ~150 milliseconds |

### Latency Targets by Use Case
| Use case | Target |
|----------|--------|
| Search results | < 100ms |
| Social feed load | < 500ms |
| Video start time | < 2s |
| Payment processing | < 3s |
| Background job | Minutes acceptable |

---

## Interview Communication Tips

1. **Restate the problem** in your own words before diving in
2. **Think out loud** — explain your reasoning for every choice
3. **Draw diagrams** on the whiteboard as you talk
4. **Ask before estimating** — "Should I do a rough capacity estimate?"
5. **Acknowledge trade-offs** — "I chose X because Y, but the downside is Z"
6. **Drive the interview** — don't wait to be asked, proactively bring up concerns
7. **Timeboxing** — don't spend 20 min on requirements, move forward
8. **Be honest** — "I'm not 100% sure about X, but I'd approach it by..."
9. **Iterate** — your first design doesn't need to be perfect, refine it
10. **Know your audience** — depth of answer should match seniority level

---

*Use this template for every system design practice session. Internalize the steps until they feel natural.*
