# Consistent Hashing

## The Problem It Solves

With **regular (modular) hashing**: `node = hash(key) % N`

When N changes (add or remove a node), **almost every key gets remapped** to a different node — causing massive data migration or cache misses.

**Consistent hashing** ensures that when N changes, only `~1/N` of keys need to be remapped.

---

## How It Works

### Step 1: Create a Hash Ring
Imagine a circular space (ring) of values from `0` to `2^32 - 1`.

### Step 2: Map Servers to the Ring
Hash each server's name/IP → place it at a position on the ring.

```
           Server A (hash: 100)
               ●
          ╱         ╲
        ╱               ╲
Server D ●               ● Server B
(hash:300)               (hash: 150)
        ╲               ╱
          ╲           ╱
               ●
           Server C (hash: 250)
```

### Step 3: Map Keys to the Ring
Hash each data key → find its position on the ring.

### Step 4: Assign Key to Server
Walk **clockwise** from the key's position on the ring → the first server you hit owns that key.

```
Key "user:123" hashes to position 130
→ walk clockwise → hits Server B at 150
→ Key stored on Server B
```

---

## Adding/Removing Nodes

**Adding a node (Server E at position 200):**
- Only keys between Server B (150) and Server E (200) need to move from Server C to Server E
- All other keys unaffected ✅

**Removing a node (Server B at 150):**
- Only Server B's keys (100–150) move to the next server (Server C)
- All other keys unaffected ✅

---

## Virtual Nodes (vnodes)

**Problem:** With few servers, distribution may be uneven (one server gets disproportionate keys).

**Solution:** Each physical server maps to **multiple virtual nodes** on the ring.

```
Server A → positions: 10, 105, 230, 310...
Server B → positions: 50, 160, 270, 380...
```

Benefits:
- More uniform distribution of keys
- Compensates for servers with different capacities (bigger server = more vnodes)
- Smoother rebalancing when nodes are added/removed

---

## Real-World Usage

| System | How Used |
|--------|---------|
| **Cassandra** | Distributes data across nodes in the ring |
| **DynamoDB** | Uses consistent hashing internally for partitioning |
| **Memcached** | Client-side consistent hashing to pick cache server |
| **Redis Cluster** | Hash slots (16384 slots) distributed across nodes |
| **Chord** | P2P DHT protocol based on consistent hashing |
| **Nginx** | Consistent hash upstream module for backend selection |

---

## Summary

| | Regular Hashing | Consistent Hashing |
|-|-----------------|-------------------|
| Keys remapped on node change | ~All keys | ~1/N keys |
| Uniform distribution | ✅ | Needs vnodes |
| Complexity | Low | Medium |
| Best for | Fixed set of nodes | Dynamic cluster (nodes join/leave) |

---

## Interview Tip

> "For distributing data across our cache cluster, I'd use consistent hashing so that adding or removing cache nodes only remaps a small fraction of keys instead of causing a full cache miss storm. Each physical node would have multiple virtual nodes on the ring to ensure even distribution."
