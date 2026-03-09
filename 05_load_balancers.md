# Load Balancers

## What Is It?
A load balancer distributes incoming network traffic across multiple backend servers to ensure no single server is overwhelmed. It sits between the client and your server pool.

```
[Client] → [Load Balancer] → [Server 1]
                           → [Server 2]
                           → [Server 3]
```

---

## Benefits

- **High Availability** — route around failed servers automatically
- **Horizontal Scalability** — add/remove servers without client changes
- **Improved Performance** — spread load evenly, no single bottleneck
- **SSL Termination** — decrypt HTTPS once at the LB, plain HTTP to servers
- **Health Checks** — automatically remove unhealthy servers from rotation

---

## Load Balancing Algorithms

| Algorithm | How It Works | Best For |
|-----------|-------------|---------|
| **Round Robin** | Requests distributed evenly in circular order | Servers with equal capacity |
| **Weighted Round Robin** | Servers with higher weights receive more requests | Heterogeneous server sizes |
| **Least Connections** | Routes to server with fewest active connections | Long-lived connections (WebSockets) |
| **Least Response Time** | Routes to the fastest responding server | Latency-sensitive apps |
| **IP Hash** | Client IP hashed to determine server | Session persistence without cookies |
| **Random** | Randomly picks a server | Simple, low overhead |

---

## Layer 4 vs Layer 7 Load Balancers

| | **L4 (Transport Layer)** | **L7 (Application Layer)** |
|-|--------------------------|---------------------------|
| Operates on | IP, TCP/UDP | HTTP headers, cookies, URLs |
| Speed | Faster (less inspection) | Slower (more intelligent) |
| Routing logic | IP + Port only | Path, host, header-based |
| Use case | Raw TCP throughput | HTTP microservices, API routing |
| Examples | AWS NLB, HAProxy (TCP) | AWS ALB, Nginx, Traefik |

---

## Sticky Sessions (Session Affinity)
Forces the same client to always hit the same server (useful for stateful apps).
- Done via cookie or IP hash
- ⚠️ Reduces effectiveness of load balancing — avoid if possible; use shared session store (Redis) instead

---

## Active-Passive vs Active-Active LB

| | Active-Passive | Active-Active |
|-|----------------|---------------|
| Setup | One LB active, one on standby | Both LBs handle traffic |
| Failover | Standby takes over on failure | No failover needed |
| Throughput | Limited to one LB | Combined throughput |

---

## Health Checks
Load balancers continuously probe backend servers:
- **HTTP check:** `GET /health` → expect `200 OK`
- **TCP check:** Can open a connection?
- If a server fails N consecutive checks → removed from rotation
- When it recovers → automatically re-added

---

## Common Tools

| Tool | Type | Notes |
|------|------|-------|
| **Nginx** | L7 (also L4) | Most popular, reverse proxy + LB |
| **HAProxy** | L4 + L7 | High-performance, battle-tested |
| **AWS ALB** | L7 managed | Path/host-based routing, WAF |
| **AWS NLB** | L4 managed | Ultra-low latency, static IP |
| **Traefik** | L7 | Cloud-native, Kubernetes-friendly |
| **Cloudflare** | L7 + DDoS | Global edge load balancing |

---

## Interview Tip

> "I'd put a Layer 7 load balancer in front of the application servers to distribute HTTP traffic, perform health checks, and handle SSL termination. For the database tier, I'd use read replicas and route read queries to them — effectively load balancing at the DB layer too."
