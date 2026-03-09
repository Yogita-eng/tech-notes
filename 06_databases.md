# Databases

## What Is It?
A database is an organized collection of data that can be efficiently stored, accessed, managed, and updated.

Choosing the right database type is one of the most important decisions in system design.

---

## Types of Databases

| Type | Data Model | Examples | Best For |
|------|------------|---------|---------|
| **Relational (RDBMS)** | Tables, rows, columns | PostgreSQL, MySQL, Oracle | Structured data, complex queries, ACID transactions |
| **Key-Value** | Key → Value pairs | Redis, DynamoDB, Memcached | Caching, sessions, simple lookups |
| **Document** | JSON/BSON documents | MongoDB, CouchDB | Flexible schema, content management |
| **Column-Family** | Wide columns per row | Cassandra, HBase | High-write throughput, time-series, analytics |
| **Graph** | Nodes + Edges | Neo4j, Amazon Neptune | Social networks, fraud detection, recommendations |
| **In-Memory** | Data stored in RAM | Redis, Memcached | Caching, real-time leaderboards |
| **Time-Series** | Time-stamped data | InfluxDB, TimescaleDB | Metrics, IoT, monitoring |
| **Search** | Inverted index | Elasticsearch, Solr | Full-text search, log analysis |
| **Spatial** | Geographic data | PostGIS, MongoDB | Maps, location services |
| **Vector** | High-dimensional vectors | Pinecone, pgvector | AI embeddings, semantic search |

---

## ACID Properties (Relational DBs)

| Property | Meaning |
|----------|---------|
| **Atomicity** | Transaction is all-or-nothing |
| **Consistency** | Data always moves from one valid state to another |
| **Isolation** | Concurrent transactions don't interfere with each other |
| **Durability** | Committed data persists even after a crash |

---

## BASE Properties (NoSQL)

| Property | Meaning |
|----------|---------|
| **Basically Available** | System remains available |
| **Soft State** | State may change over time even without input |
| **Eventual Consistency** | System will become consistent eventually |

---

## Replication

### Primary-Replica (Master-Slave)
```
[Primary] ──writes──→ [Replica 1]
                    → [Replica 2]
```
- All writes go to primary
- Reads served from replicas (scales read throughput)
- If primary fails → promote a replica

### Primary-Primary (Multi-Master)
- Both nodes accept reads and writes
- More complex conflict resolution
- Used for geographic distribution (active-active regions)

---

## Indexes
- Speed up reads by creating lookup structures on columns
- **B-Tree:** Most common, supports range queries
- **Hash:** Fast exact lookups only
- **Full-text:** Tokenizes and indexes text content
- ⚠️ Every index slows down writes — index only what you query

---

## Database Selection Guide

```
Need ACID transactions?              → PostgreSQL / MySQL
High write throughput, wide rows?   → Cassandra
Simple key lookups, caching?        → Redis
Flexible schema, document storage?  → MongoDB
Full-text search?                    → Elasticsearch
Highly connected data (graphs)?     → Neo4j
Time-series metrics?                 → InfluxDB / TimescaleDB
AI/vector similarity search?        → Pinecone / pgvector
```

---

## Polyglot Persistence
Modern systems often use **multiple databases** — each chosen for a specific use case.

**Example (E-commerce):**
- PostgreSQL → orders, payments, users (ACID required)
- Redis → cart, sessions, rate limiting (fast, ephemeral)
- Elasticsearch → product search (full-text)
- Cassandra → activity logs (high write volume)

---

## Interview Tip

> "For this system, I'd use PostgreSQL for the core transactional data since we need ACID guarantees, and Redis as a cache layer for frequently accessed data to reduce DB load. If we need full-text search later, I'd add Elasticsearch synced via change data capture."
