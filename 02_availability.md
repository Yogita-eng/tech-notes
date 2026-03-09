# Availability

## What Is It?
Availability is the proportion of time a system is operational and accessible when required.

```
Availability = Uptime / (Uptime + Downtime)
```

- **Uptime:** Period during which the system is functional and accessible
- **Downtime:** Period during which the system is unavailable (failures, maintenance, etc.)

---

## Availability Tiers ("The Nines")

| Availability | Downtime / Year | Downtime / Month | Common Name |
|-------------|-----------------|------------------|-------------|
| 99% | 3.65 days | ~7.2 hours | Two nines |
| 99.9% | 8.76 hours | ~43.8 minutes | Three nines |
| 99.99% | 52.56 minutes | ~4.38 minutes | Four nines |
| 99.999% | 5.26 minutes | ~26 seconds | Five nines |
| 99.9999% | 31.5 seconds | ~2.6 seconds | Six nines |

Most production systems target **99.9% to 99.99%**. Five nines is extremely hard and expensive to achieve.

---

## How to Improve Availability

| Technique | Description |
|-----------|-------------|
| **Redundancy** | Multiple instances of every component |
| **Failover** | Automatically switch to backup on failure |
| **Health Checks / Heartbeats** | Detect failing components quickly |
| **Geographic Distribution** | Multi-AZ or multi-region deployments |
| **Circuit Breakers** | Prevent cascading failures across services |
| **Graceful Degradation** | Serve reduced functionality during partial failure |

---

## SLA vs SLO vs SLI

| Term | Meaning | Example |
|------|---------|---------|
| **SLA** (Service Level Agreement) | Contract with users | "We guarantee 99.9% uptime" |
| **SLO** (Service Level Objective) | Internal target | "We aim for 99.95% uptime" |
| **SLI** (Service Level Indicator) | Actual measured metric | "Current success rate: 99.97%" |

---

## Availability vs Consistency (CAP Tradeoff)

During a network partition, you must choose:
- **Availability:** System returns a response (possibly stale) — e.g., Cassandra, DynamoDB
- **Consistency:** System returns an error rather than stale data — e.g., HBase, Zookeeper

---

## Interview Tip

> "To achieve high availability, I'd eliminate single points of failure by running multiple instances across availability zones, using a load balancer with health checks to route away from unhealthy nodes, and setting up a standby database replica for automatic failover."
