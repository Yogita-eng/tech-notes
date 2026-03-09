# REST vs RPC

## Quick Decision

```
Public API consumed by many clients?         → REST
Internal service-to-service, high perf?     → gRPC (RPC)
Complex, flexible data requirements?        → GraphQL
```

---

## REST (Representational State Transfer)

### Principles
- **Resource-based** — URLs represent things (nouns): `/users`, `/orders/123`
- **Standard HTTP methods** — GET, POST, PUT, PATCH, DELETE
- **Stateless** — each request is self-contained
- Returns **JSON or XML**
- **Uniform interface** — consistent, discoverable

### HTTP Methods

| Method | Action | Idempotent? | Safe? |
|--------|--------|-------------|-------|
| GET | Read | ✅ | ✅ |
| POST | Create | ❌ | ❌ |
| PUT | Replace (full update) | ✅ | ❌ |
| PATCH | Partial update | ❌ | ❌ |
| DELETE | Delete | ✅ | ❌ |

### Example
```
GET    /users/123        → fetch user
POST   /users            → create user
PUT    /users/123        → replace user
PATCH  /users/123        → update user fields
DELETE /users/123        → delete user
```

### Best For
- Public APIs consumed by browsers, mobile apps, third parties
- Simple CRUD operations
- When discoverability and documentation matter
- Large developer ecosystem

---

## RPC (Remote Procedure Call)

### Principles
- **Action-based** — calls remote functions (verbs): `getUser()`, `placeOrder()`
- Treats the remote service like a local function call
- Can use various protocols (HTTP, TCP, binary)

### gRPC (Modern RPC — Google)
- Uses **Protocol Buffers** (binary serialization — smaller and faster than JSON)
- Runs over **HTTP/2** — multiplexing, streaming, header compression
- Strongly-typed contracts via `.proto` files
- Auto-generates client/server code in multiple languages

```protobuf
// orders.proto
service OrderService {
  rpc PlaceOrder(OrderRequest) returns (OrderResponse);
  rpc GetOrderStatus(OrderId) returns (OrderStatus);
  rpc StreamUpdates(OrderId) returns (stream OrderUpdate);
}
```

### Best For
- **Internal microservice-to-microservice** communication
- High-performance, low-latency service calls
- Bidirectional streaming
- When binary serialization overhead matters

---

## Comparison

| | REST | gRPC / RPC |
|-|------|------------|
| **Protocol** | HTTP/1.1 | HTTP/2 |
| **Format** | JSON (human readable) | Binary Protobuf (compact, fast) |
| **Performance** | Moderate | High |
| **Coupling** | Loose (URL-based) | Tighter (schema contract) |
| **Browser support** | Native | Needs gRPC-Web |
| **Streaming** | Limited | Native bidirectional |
| **Code gen** | Optional (OpenAPI) | Required (.proto) |
| **Best for** | Public APIs | Internal services |

---

## GraphQL (Third Option)

- Single endpoint (`POST /graphql`)
- Client specifies **exactly what data** it needs in the query
- Eliminates over-fetching and under-fetching
- Ideal for: complex frontend data needs, mobile apps (minimize bandwidth), BFF (Backend for Frontend) pattern

```graphql
query {
  user(id: "123") {
    name
    email
    orders(last: 5) {
      id
      total
      status
    }
  }
}
```

---

## Interview Tip
> "For the public-facing API consumed by mobile and web clients, I'd use REST — it's well understood, easy to document with OpenAPI, and works natively with browsers. For internal service-to-service communication between microservices, I'd use gRPC — the binary protocol and HTTP/2 give us significantly lower latency and smaller payloads, which matters when services are calling each other hundreds of times per second."
