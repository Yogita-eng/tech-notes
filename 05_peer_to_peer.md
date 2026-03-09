# 5. Peer-to-Peer (P2P) Architecture

## Overview
Decentralized architecture where each **peer** acts as both client and server, sharing resources directly with other peers. No central coordinator.

```
[Peer A] ←→ [Peer B] ←→ [Peer C]
    ↕                        ↕
[Peer D] ←────────────→ [Peer E]
```

## Characteristics
- No central server (fully decentralized)
- Each peer has equal status and responsibilities
- Peers can join/leave freely
- Resources shared directly between peers

## Benefits
- **No single point of failure** — highly resilient
- **Scales naturally** — more peers = more resources
- **Cost-efficient** — no central infrastructure
- **Privacy** — no central authority storing data

## Challenges
- **Inconsistency** — hard to maintain consistent state
- **Security** — peers can be malicious
- **Discovery** — hard to find peers without a central registry (usually uses bootstrap nodes)
- **NAT traversal** — firewalls block direct peer connections

## Types of P2P
- **Pure P2P:** No central component at all (BitTorrent DHT)
- **Hybrid P2P:** Central index server for discovery, then direct peer communication (original Napster)

## Real-World Applications
| Application | P2P Usage |
|-------------|-----------|
| **BitTorrent** | File distribution |
| **Bitcoin/Ethereum** | Distributed ledger, transaction propagation |
| **WebRTC** | Browser-to-browser video/audio (Zoom, Google Meet) |
| **Skype (original)** | VoIP calls |
| **IPFS** | Distributed file system |
| **Gnutella** | File sharing |

---
