# Bloom Filters

## What Is It?
A Bloom filter is a **space-efficient probabilistic data structure** that answers one question:

> "Is this element in the set?"

Possible answers:
- **"Definitely NOT in the set"** — guaranteed correct (no false negatives)
- **"Possibly in the set"** — may be wrong (false positives possible)

---

## Key Properties

| Property | Value |
|----------|-------|
| **False negatives** | Impossible — if it says "not present," it's not |
| **False positives** | Possible — may say "present" when it's not |
| **Space** | Much smaller than storing actual elements |
| **Deletion** | Not supported in standard Bloom filter |

---

## How It Works

### Setup
1. Initialize a **bit array** of `m` bits, all set to `0`
2. Choose `k` independent hash functions

### Inserting an Element
1. Run the element through all `k` hash functions → get `k` positions
2. Set the bits at those `k` positions to `1`

### Querying an Element
1. Run the element through all `k` hash functions → get `k` positions
2. Check those `k` bit positions:
   - Any bit is `0` → element is **definitely NOT in the set** ✅
   - All bits are `1` → element **probably IS in the set** (might be false positive)

```
Insert "apple":
  hash1("apple") = 2 → bit[2] = 1
  hash2("apple") = 5 → bit[5] = 1
  hash3("apple") = 9 → bit[9] = 1

Query "apple":  bits 2, 5, 9 all = 1 → probably present ✅
Query "mango":  hash1 = 2 (1), hash2 = 7 (0) → bit 7 is 0 → definitely NOT present ✅
Query "grape":  hash1 = 2 (1), hash2 = 5 (1), hash3 = 4 (1) → false positive ⚠️
```

---

## Tuning Parameters

- **More bits (m)** → lower false positive rate, more memory
- **More hash functions (k)** → up to a point, lower FPR, then increases
- **More elements (n)** → higher false positive rate
- Typical false positive rates: 1–5% with reasonable memory usage

---

## When to Use

✅ **Check before expensive DB lookup** — "Does user X exist?" — if Bloom says no, skip the DB  
✅ **Web crawlers** — "Have I already crawled this URL?"  
✅ **Spam filters** — "Is this email address in the blocked list?"  
✅ **Duplicate detection** in stream processing  
✅ **Password breach detection** — "Is this password in the list of breached passwords?"  

---

## Real-World Usage

| System | How Bloom Filters Are Used |
|--------|--------------------------|
| **Cassandra** | Each SSTable has a Bloom filter — avoids disk reads for keys not in that file |
| **HBase / BigTable** | Same as Cassandra — reduces unnecessary disk lookups |
| **Google Chrome** | Safe Browsing feature uses Bloom filters to quickly check malicious URLs |
| **Redis** | Has a built-in Bloom filter module (RedisBloom) |
| **CDNs** | Check if content is cached without querying cache storage |
| **Bitcoin** | SPV clients use Bloom filters to ask nodes for relevant transactions |

---

## Interview Tip
> "Before querying the database to check if a username already exists, I'd run it through a Bloom filter. If the filter says 'definitely not present,' we skip the DB query entirely — saving latency and DB load. If it says 'possibly present,' we do the DB lookup to confirm. False positives just mean an extra DB read occasionally, which is acceptable."
