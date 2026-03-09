# Consensus Algorithms

## What Is It?
Consensus algorithms allow nodes in a distributed system to **agree on a single value or state**, even in the presence of failures, network delays, or malicious nodes.

---

## Why Needed?
In distributed systems, nodes can disagree on:
- Who is the current **leader**?
- What is the **latest committed value**?
- Is a node **dead or just slow**?

Consensus algorithms resolve these disagreements safely.

---

## Key Properties

| Property | Meaning |
|----------|---------|
| **Safety** | All nodes agree on the same value (no split decisions) |
| **Liveness** | Nodes eventually reach a decision (don't get stuck) |
| **Fault Tolerance** | Works even if some nodes fail |

> **FLP Impossibility:** In an asynchronous system, you cannot guarantee all three simultaneously. Real systems relax liveness slightly (e.g., require eventual progress).

---

## Paxos
- Roles: **Proposer**, **Acceptor**, **Learner**
- Proposer sends a proposal → if a **majority (quorum) of acceptors** agree → value is committed
- Complex to understand and implement correctly
- Variants: Multi-Paxos, Fast Paxos

---

## Raft (Simpler Alternative to Paxos)
- Roles: **Leader**, **Follower**, **Candidate**
- One **Leader** manages all writes and replicates the log to followers
- Followers **vote** to elect a leader via randomized timeouts
- Write is committed only after **majority of nodes** acknowledge it

```
Leader elected → Client writes go to leader → Leader replicates to followers
→ Majority ACK → Committed → Followers apply to state machine
```

Used by: **etcd** (Kubernetes), **CockroachDB**, **Consul**, **TiKV**

---

## Byzantine Fault Tolerance (BFT)
- Handles **malicious/corrupted** nodes (not just crashed ones)
- More expensive (requires `3f + 1` nodes to tolerate `f` Byzantine nodes)
- Used in blockchain systems: PBFT, Tendermint

---

## Real-World Usage

| System | Algorithm |
|--------|-----------|
| etcd / Kubernetes | Raft |
| Zookeeper | ZAB (Zookeeper Atomic Broadcast) |
| Consul | Raft |
| CockroachDB | Raft per range |
| Bitcoin | Proof of Work |

---

## Interview Tip
> "For leader election in our distributed service, I'd use etcd backed by Raft consensus. When our primary node goes down, etcd handles the election automatically — the remaining nodes vote, a new leader is chosen, and our services discover the new leader via etcd's watch API."
