# Database Indexes

## What Is It?
A database index is a **data structure that speeds up data retrieval** on a table at the cost of additional storage and slower writes.

Without an index → **Full table scan** (O(n)) — reads every row  
With an index → **Targeted lookup** (O(log n) for B-Tree) — jumps straight to the data

An index stores the indexed column values along with **pointers** to the actual rows in the table.

---

## Types of Indexes

| Type | Description | Use Case |
|------|-------------|---------|
| **B-Tree** | Balanced tree; default in most DBs | Range queries, equality, sorting |
| **Hash** | Hash map; equality lookups only | `WHERE id = 5` exact match |
| **Composite** | Index on multiple columns | Multi-column `WHERE` clauses |
| **Covering** | Includes all columns a query needs | Avoids table row lookup entirely |
| **Full-Text** | Tokenizes and indexes text | `LIKE` searches, keyword search |
| **Partial** | Index on a subset of rows | `WHERE status = 'active'` only |
| **Unique** | Enforces uniqueness + speeds lookup | Email, username columns |
| **Bitmap** | Bit arrays for low-cardinality columns | Data warehousing, analytics |

---

## How B-Tree Index Works

```
Index on "last_name":

        [M]
       /   \
    [G-L]  [N-Z]
    /   \    /  \
  [G] [H-L] [N] [O-Z]

Query: WHERE last_name = 'Kumar'
→ traverse tree → O(log n) lookups → get row pointer → fetch row
```

---

## The Tradeoff

| | Without Index | With Index |
|-|---------------|------------|
| **Read speed** | Slow (full table scan) | Fast (targeted lookup) |
| **Write speed** | Fast | Slower (index must be updated) |
| **Storage** | Less | More |

> **Rule:** Index for reads, but every index has a write cost. Don't over-index.

---

## What to Index

✅ Columns in `WHERE` clauses  
✅ Columns in `JOIN` conditions  
✅ Columns in `ORDER BY` and `GROUP BY`  
✅ Foreign key columns  
✅ Columns with high cardinality (many unique values)  

❌ Columns rarely queried  
❌ Very low cardinality columns (e.g., `is_active` boolean — bitmap index instead)  
❌ Tables with very high write rates and infrequent reads  

---

## Composite Index Column Order

For composite index `(a, b, c)`:
- Query on `a` → uses index ✅
- Query on `a, b` → uses index ✅
- Query on `a, b, c` → uses index ✅
- Query on `b` alone → **does NOT use index** ❌
- Query on `b, c` alone → **does NOT use index** ❌

> **Rule:** Queries must match the **leftmost prefix** of the composite index.

---

## How to Check Index Usage

```sql
-- PostgreSQL
EXPLAIN ANALYZE SELECT * FROM users WHERE email = 'test@example.com';

-- Look for "Index Scan" (good) vs "Seq Scan" (full table scan, bad)
```

---

## Common Index Strategies

### For a users table:
```sql
CREATE INDEX idx_users_email ON users(email);          -- login lookups
CREATE INDEX idx_users_created ON users(created_at);   -- recent user queries
CREATE UNIQUE INDEX idx_users_username ON users(username);  -- uniqueness + speed
```

### For a posts table (social media feed):
```sql
CREATE INDEX idx_posts_user_created ON posts(user_id, created_at DESC);
-- covers: WHERE user_id = X ORDER BY created_at DESC LIMIT 20
```

---

## Interview Tip

> "To optimize read performance on the messages table, I'd add a composite index on `(conversation_id, created_at DESC)` — since the most common query pattern is fetching the latest N messages for a given conversation. I'd avoid indexing every column since each index adds overhead on the write path, which is frequent in a chat app."
