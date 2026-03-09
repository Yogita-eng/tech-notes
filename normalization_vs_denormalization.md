# Normalization vs Denormalization

## Quick Decision

```
Write-heavy, data integrity critical (OLTP)?  → Normalize
Read-heavy, analytics, reporting (OLAP)?      → Denormalize
NoSQL at scale?                               → Denormalize (by default)
```

---

## Normalization
Structuring a database to **minimize redundancy** by splitting data into related tables.

Each fact is stored in exactly one place.

### Example — Normalized
```
CUSTOMER:  customer_id | name | email | phone
ORDER:     order_id | customer_id | date | quantity | price
```
To get a customer's order details → JOIN the two tables.

### Pros
- Less storage (no duplicated data)
- Update in one place — no inconsistency risk
- Better data integrity
- Fewer anomalies (insert/update/delete anomalies avoided)

### Cons
- Complex queries requiring many JOINs
- Slower reads (multiple table lookups)
- JOINs are expensive at scale

### Best For
- OLTP (frequent writes, transactions)
- Financial systems, inventory
- When data changes frequently

---

## Denormalization
Intentionally **introducing redundancy** to improve read performance by pre-joining data.

### Example — Denormalized
```
CUSTOMER_ORDER: id | customer_name | email | date | quantity | price
```
Customer info repeated in every order row — but reads are a single table scan.

### Pros
- Faster reads — fewer or no JOINs
- Simpler queries
- Great for read-heavy workloads

### Cons
- Data redundancy (wastes storage)
- Update anomalies — changing a customer's email requires updating all their order rows
- More complex writes

### Best For
- OLAP (heavy reads, analytics)
- NoSQL databases (designed around denormalization)
- Microservices (each service maintains its own denormalized view)
- Pre-computed query results (materialized views)

---

## CQRS Pattern (Best of Both Worlds)
- **Write model:** Normalized — enforces consistency and integrity
- **Read model:** Denormalized — optimized for specific query patterns
- Sync them via events or CDC (Change Data Capture)

---

## Interview Tip
> "The core user and order tables would be normalized for data integrity and ACID compliance on writes. But for the order history page that users open frequently, I'd maintain a denormalized read model in Redis — a pre-joined view of order + product + customer data — so it's a single fast lookup instead of 3 JOINs."
