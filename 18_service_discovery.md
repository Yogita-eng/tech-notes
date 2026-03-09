# Service Discovery

## What Is It?
Service discovery is a mechanism that allows services in a distributed system to **dynamically find and communicate with each other** without hardcoded addresses.

In microservices, instances start/stop, scale up/down, and change IPs constantly. Hardcoding `192.168.1.5:8080` breaks immediately.

---

## How It Works

1. **Register:** When a service starts, it registers itself (IP, port, health endpoint) in a **Service Registry**
2. **Lookup:** When Service A wants to call Service B, it queries the registry for B's current address
3. **Call:** Service A calls Service B directly using the retrieved address
4. **Deregister:** When a service stops (or fails health checks), it's removed from the registry

```
[Service B starts]
    → registers with Service Registry

[Service A wants to call B]
    → asks Registry: "Where is Service B?"
    → Registry returns: "192.168.1.22:8080"
    → Service A calls Service B directly
```

---

## Two Discovery Patterns

### Client-Side Discovery
- Client queries the registry and picks an instance itself
- Client must implement load balancing logic
- **Example:** Netflix Eureka + Ribbon

```
Client → Service Registry → gets list of instances → client picks one → calls it
```

### Server-Side Discovery
- Client calls a load balancer/router
- The LB queries the registry and forwards the request
- Client needs no discovery logic — simpler
- **Example:** AWS ALB + ECS, Kubernetes Services

```
Client → Load Balancer → queries Registry → forwards to instance
```

---

## Service Registry Tools

| Tool | Best For |
|------|---------|
| **Consul** | Service discovery + health checking + KV store; widely used |
| **etcd** | Used by Kubernetes internally; key-value + watch |
| **Zookeeper** | Coordination service; used by Kafka/Hadoop |
| **Eureka** | Netflix OSS; Java-centric |
| **Kubernetes DNS** | Automatic in K8s — `service-name.namespace.svc.cluster.local` |

---

## Health Checks
Service registries continuously health-check registered services:
- **Active health check:** Registry pings the service's `/health` endpoint
- **TTL-based:** Service must renew its registration periodically
- Failed health check → removed from registry → no more traffic

---

## Kubernetes Service Discovery (Most Common Today)
In Kubernetes, service discovery is automatic:
```yaml
# Service "payment-service" is reachable at:
http://payment-service.default.svc.cluster.local:8080
```
- Kubernetes DNS resolves the service name to the current pod IPs
- No external registry needed

---

## Interview Tip
> "In our microservices setup, each service registers with Consul on startup. When the Order Service needs to call the Payment Service, it queries Consul for available Payment Service instances and uses client-side load balancing to pick one. Consul's health checks automatically remove crashed instances from the registry, so we never route to a dead service."
