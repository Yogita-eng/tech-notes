# SQL vs NoSQL

## Quick Decision

```
Need ACID transactions / complex JOINs?   → SQL
Massive scale / flexible schema?          → NoSQL
Both?                                     → Polyglot (use both)
```

---

## SQL (Relational Databases)

### Characteristics
- Fixed schema defined upfront
- Tables with rows and columns
- SQL query language
- ACID transactions
- Relationships via foreign keys and JOINs
- Primarily vertical scaling

### When to Use SQL
- Financial systems needing ACID (payments, inventory)
- Complex queries with multiple JOINs
- Well-defined, structured data model unlikely to change
- Reporting and analytics (OLAP)
- Strong consistency is a hard requirement

### Examples
PostgreSQL, MySQL, Oracle, SQL Server, SQLite, CockroachDB (distributed SQL)

---

## NoSQL Databases

### Characteristics
- Dynamic / flexible schema
- Various data models (document, key-value, graph, column)
- BASE properties (eventual consistency usually)
- Horizontal scaling built-in
- Optimized for specific access patterns

### When to Use NoSQL
- Unstructured or semi-structured data
- Massive scale (billions of records, high write throughput)
- Schema changes frequently (early stage product)
- Specific patterns: time series, full-text search, graphs
- High availability over strict consistency

### Types and Examples

| Type | Examples | Best For |
|------|---------|---------|
| **Document** | MongoDB, CouchDB | Flexible schemas, JSON data |
| **Key-Value** | Redis, DynamoDB | Caching, sessions, simple lookups |
| **Column-Family** | Cassandra, HBase | High write throughput, wide rows |
| **Graph** | Neo4j, Neptune | Social graphs, fraud detection |
| **Search** | Elasticsearch | Full-text search |

---

## Side-by-Side Comparison

| Feature | SQL | NoSQL |
|---------|-----|-------|
| **Schema** | Fixed, predefined | Dynamic, flexible |
| **Scaling** | Vertical (primarily) | Horizontal (built-in) |
| **Transactions** | Full ACID | Limited (mostly BASE) |
| **Queries** | Complex JOINs, SQL | Simple key-based or limited queries |
| **Consistency** | Strong | Eventual (usually) |
| **Maturity** | Decades old, battle-tested | Varies by system |
| **Best For** | Complex relationships | High volume, speed, flexibility |

---

## Polyglot Persistence (Use Both!)

Modern systems rarely use just one database. Use the right tool for each need:

**Example — Ride-sharing app:**
- **PostgreSQL** → users, trips, payments (ACID, relational)
- **Redis** → driver locations, sessions, rate limiting (fast, ephemeral)
- **Cassandra** → trip history, event logs (high write volume)
- **Elasticsearch** → search and analytics

---

## Interview Tip
> "For the core transactional data like users and orders, I'd use PostgreSQL — we need ACID guarantees and the data is relational. For the activity feed which has very high write volume and doesn't need JOINs, I'd use Cassandra. And I'd use Redis as the caching layer across both. Different problems, different tools."
