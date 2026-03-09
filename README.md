# 🎯 System Design Interview Prep

A topic-by-topic reference — every concept has its own dedicated file for quick, focused revision.

---

## 📁 Folder Structure

```
interview-prep/
├── fundamentals/     ← 20 core building blocks
├── tradeoffs/        ← 11 key design decisions
├── patterns/         ← 10 architectural patterns
├── questions/        ← 10 full interview walkthroughs
├── 04_system_design_interview_template.md
└── 06_40_interview_tips.md
```

---

## 📘 Fundamentals (20 topics)

| # | File | One-liner |
|---|------|-----------|
| 01 | [Scalability](fundamentals/01_scalability.md) | Handle growing load by adding resources |
| 02 | [Availability](fundamentals/02_availability.md) | The nines, redundancy, SLA/SLO/SLI |
| 03 | [Latency vs Throughput](fundamentals/03_latency_vs_throughput.md) | Speed vs volume, Little's Law |
| 04 | [CAP Theorem](fundamentals/04_cap_theorem.md) | CP vs AP, partition tolerance |
| 05 | [Load Balancers](fundamentals/05_load_balancers.md) | Algorithms, L4 vs L7, health checks |
| 06 | [Databases](fundamentals/06_databases.md) | SQL, NoSQL types, ACID vs BASE |
| 07 | [CDN](fundamentals/07_cdn.md) | Edge nodes, push vs pull |
| 08 | [Message Queues](fundamentals/08_message_queues.md) | Kafka, pub/sub, delivery guarantees |
| 09 | [Rate Limiting](fundamentals/09_rate_limiting.md) | Token bucket, sliding window, Redis |
| 10 | [Database Indexes](fundamentals/10_database_indexes.md) | B-Tree, composite, covering |
| 11 | [Caching](fundamentals/11_caching.md) | Strategies, eviction, Redis vs Memcached |
| 12 | [Consistent Hashing](fundamentals/12_consistent_hashing.md) | Hash ring, virtual nodes |
| 13 | [Database Sharding](fundamentals/13_database_sharding.md) | Hash/range/geo sharding |
| 14 | [Consensus Algorithms](fundamentals/14_consensus_algorithms.md) | Paxos, Raft, leader election |
| 15 | [Proxy Servers](fundamentals/15_proxy_servers.md) | Forward vs reverse proxy |
| 16 | [Heartbeats](fundamentals/16_heartbeats.md) | Failure detection, health probes |
| 17 | [Checksums](fundamentals/17_checksums.md) | Data integrity, CRC32, SHA-256 |
| 18 | [Service Discovery](fundamentals/18_service_discovery.md) | Consul, etcd, client vs server-side |
| 19 | [Bloom Filters](fundamentals/19_bloom_filters.md) | Probabilistic, false positives |
| 20 | [Gossip Protocol](fundamentals/20_gossip_protocol.md) | Decentralized cluster membership |

---

## ⚖️ Trade-offs (11 topics)

| File | The Choice |
|------|------------|
| [Vertical vs Horizontal Scaling](tradeoffs/vertical_vs_horizontal_scaling.md) | Bigger machine vs more machines |
| [Strong vs Eventual Consistency](tradeoffs/strong_vs_eventual_consistency.md) | Correctness vs availability |
| [Stateful vs Stateless](tradeoffs/stateful_vs_stateless.md) | Session in server vs token/store |
| [Read vs Write Through Cache](tradeoffs/read_vs_write_through_cache.md) | Cache sync strategy |
| [SQL vs NoSQL](tradeoffs/sql_vs_nosql.md) | ACID vs BASE |
| [REST vs RPC](tradeoffs/rest_vs_rpc.md) | Public API vs internal services |
| [Sync vs Async](tradeoffs/sync_vs_async.md) | Wait for result vs fire-and-forget |
| [Batch vs Stream Processing](tradeoffs/batch_vs_stream_processing.md) | Scheduled jobs vs real-time |
| [Long Polling vs WebSockets](tradeoffs/long_polling_vs_websockets.md) | Pull vs persistent connection |
| [Normalization vs Denormalization](tradeoffs/normalization_vs_denormalization.md) | Data integrity vs read speed |
| [TCP vs UDP](tradeoffs/tcp_vs_udp.md) | Reliable vs fast transport |

---

## 🏛️ Architectural Patterns (10 topics)

| File | Pattern |
|------|---------|
| [01 Client-Server](patterns/01_client_server.md) | The foundational model |
| [02 Microservices](patterns/02_microservices.md) | Independent, deployable services |
| [03 Serverless](patterns/03_serverless.md) | Function-as-a-Service, no infra |
| [04 Event-Driven](patterns/04_event_driven.md) | Decoupled via events/messages |
| [05 Peer-to-Peer](patterns/05_peer_to_peer.md) | No central server |
| [06 Monolithic](patterns/06_monolithic.md) | Single deployable unit |
| [07 SOA](patterns/07_soa.md) | Services via enterprise bus |
| [08 CQRS](patterns/08_cqrs.md) | Separate read and write models |
| [09 Event Sourcing](patterns/09_event_sourcing.md) | Store events, not state |
| [10 Layered / N-Tier](patterns/10_layered_n_tier.md) | Presentation → Logic → Data |

---

## ❓ Interview Questions (10 full walkthroughs)

Each includes: requirements → capacity → architecture → DB schema → deep dive

| File | System |
|------|--------|
| [01 URL Shortener](questions/01_url_shortener.md) | TinyURL |
| [02 Chat App](questions/02_chat_app.md) | WhatsApp |
| [03 Social Media](questions/03_instagram.md) | Instagram |
| [04 Video Streaming](questions/04_youtube.md) | YouTube |
| [05 E-Commerce](questions/05_ecommerce.md) | Amazon |
| [06 Ride-Sharing](questions/06_uber.md) | Uber |
| [07 File Storage](questions/07_google_drive.md) | Google Drive |
| [08 Web Crawler](questions/08_web_crawler.md) | Googlebot |
| [09 Notification System](questions/09_notification_system.md) | Push/Email/SMS |
| [10 Logging & Monitoring](questions/10_logging_monitoring.md) | ELK, metrics, traces |

---

## 📋 Interview Resources

- [Interview Template](04_system_design_interview_template.md) — 7-step framework with time allocation
- [40 Interview Tips](06_40_interview_tips.md) — Communication, scalability, DB, caching tips

---

## 🚀 Suggested Study Order

**Week 1 — Fundamentals:** Read 3 topics/day from the fundamentals folder  
**Week 2 — Trade-offs + Patterns:** 2 trade-offs + 1 pattern per day  
**Week 3 — Practice:** 1-2 questions/day with a 45-min timer, whiteboard style

> *"There is no perfect design. It's all about trade-offs."*
