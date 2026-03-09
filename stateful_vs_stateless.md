# Stateful vs Stateless Design

## Quick Decision

```
Long-lived connections (gaming, WebSockets)?    → Stateful
REST APIs, microservices, cloud services?       → Stateless
Need to scale horizontally?                     → Stateless (or stateless + external state store)
```

---

## Stateful Design
The server **remembers client data** between requests — it stores session/context in local memory.

```
Request 1: User logs in → Server A stores session in memory
Request 2: User makes request → Must go to Server A (has the session)
```

### Pros
- Simpler logic per request — context is already there
- Fast for session-heavy operations (no external lookup)
- Natural fit for long-lived connections (gaming, live sessions)

### Cons
- Hard to scale horizontally — client must always hit the same server (sticky sessions)
- Server failure loses all session state
- Uneven load — "hot" servers get stuck with active sessions
- Complex deployments and rolling updates

### Use Cases
- Traditional web apps with server-side sessions
- Game servers (player state maintained in memory)
- Live collaboration sessions

---

## Stateless Design
Every request contains **all information needed** to process it. Server stores nothing between requests.

```
Request 1: { "user_id": 123, "token": "jwt..." } → any Server can handle it
Request 2: { "user_id": 123, "token": "jwt..." } → any Server can handle it
```

### Pros
- **Easy horizontal scaling** — any server handles any request
- No sticky sessions needed — load balancer can freely distribute
- Simple failure recovery — restart any server, nothing is lost
- Easy rolling deployments

### Cons
- Larger request payloads — must carry context (JWT, parameters)
- More external lookups — session/user data fetched from DB/Redis per request
- More complex for multi-step workflows (need to track state externally)

### Use Cases
- REST APIs (the "S" in REST means stateless)
- Microservices
- Serverless functions (Lambda)
- Anything behind a load balancer at scale

---

## Hybrid: Stateless App + External State Store

The best of both worlds — stateless application servers with state stored in a shared external store:

```
[Stateless App Server 1] ─┐
[Stateless App Server 2] ─┼─→ [Redis (session/state)] → [Database]
[Stateless App Server 3] ─┘
```

- App servers are fully stateless → scale freely
- State lives in Redis (fast) or the DB (durable)
- Any server can handle any request by reading state from Redis

---

## JWT — Enabling Stateless Auth

Instead of server-side sessions, encode user info into a **signed token** sent with every request:

```
JWT payload: { user_id: 123, role: "admin", exp: 1700000000 }
Signed with server secret → tamper-proof

Client sends: Authorization: Bearer <jwt_token>
Server verifies signature → no DB lookup needed → stateless ✅
```

---

## Interview Tip
> "I'd design the application servers as stateless — each request carries a JWT token with user identity. Any server can verify it without a session store lookup. User preferences and state are loaded from Redis on demand. This means I can freely autoscale the app tier — add 10 new servers during peak and they immediately take traffic with no warm-up."
