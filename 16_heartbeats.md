# Heartbeats

## What Is It?
A **heartbeat** is a periodic signal sent between components to confirm they are alive and functioning. If a component stops sending heartbeats, others assume it has failed.

```
Client → HeartBeat → Server (every 5s)
Client → HeartBeat → Server (every 5s)
... (10s gap — no heartbeat) ...
Server marks Client as OFFLINE
```

---

## Why Needed?
Without heartbeats, detecting failures in a distributed system is slow — leading to:
- Delayed fault detection and slow recovery
- Increased downtime and stale routing
- Cascading failures (requests still sent to dead nodes)

---

## How It Works
1. Component A sends a "I'm alive" message to Component B every N seconds
2. Component B tracks last-received heartbeat timestamp
3. If no heartbeat in `N × threshold` seconds → Component A is presumed dead
4. Trigger failover, alert, or remove from rotation

**Common settings:** 5–30 second interval, 3 missed = declared dead

---

## Types
- **Active:** Component A pushes heartbeats to B
- **Passive:** B polls A for health (pull-based health check)
- **Gossip-based:** Heartbeat info spread via gossip across the cluster

---

## Real-World Usage
- Kubernetes **liveness probes** — restart pod if health check fails
- Load balancer **health checks** — remove unhealthy backends
- Kafka / Zookeeper **session timeouts** — leader reelection when broker dies
- Cassandra **gossip** — detect dead nodes in the ring

---

## Interview Tip
> "Each service instance sends a heartbeat to our service registry every 10 seconds. If the registry misses 3 consecutive heartbeats, it marks the instance as unhealthy and the load balancer stops routing traffic to it. This gives us automatic failure detection within ~30 seconds."
