# Long Polling vs WebSockets vs SSE

## Quick Decision

```
Real-time bidirectional (chat, gaming)?         → WebSockets
Server-to-client only (notifications, feed)?    → SSE (simpler) or WebSockets
Occasional updates, simple setup?               → Long Polling
```

---

## Short Polling (The Naive Baseline)
Client sends requests at fixed intervals to check for updates.

```
Client → GET /updates → (empty) → wait 5s → repeat
```

❌ Wasteful — mostly empty responses  
❌ High server load  
❌ Not truly real-time (always lagging by poll interval)  

Only appropriate for very infrequent checks or when nothing else is available.

---

## Long Polling
Client sends a request; server **holds the connection open** until new data is available (or timeout).

```
Client → Request
Server holds open connection...
(New data arrives)
Server → Response with data
Client immediately re-requests
```

### Pros
- Works over standard HTTP — no special infrastructure
- Simpler than WebSockets
- Works through most firewalls and proxies

### Cons
- Still creates a new HTTP connection on every cycle (overhead)
- Higher latency than WebSockets
- Server holds many open connections (resource intensive)
- Not truly bidirectional

### Use Cases
- Notification systems with infrequent updates
- Status polling (order tracking, job status)
- When WebSockets aren't supported

---

## WebSockets
Establishes a **persistent, full-duplex** (bidirectional) connection. Both sides can send messages at any time after the initial handshake.

```
Client → HTTP Upgrade Request (Handshake)
Server → 101 Switching Protocols
[Persistent TCP connection established]
Client ↔ Server (send messages at will, both directions)
```

### Pros
- True real-time — lowest latency
- Full-duplex — both sides send simultaneously
- Minimal overhead after initial handshake (no repeated HTTP headers)
- Ideal for high-frequency updates

### Cons
- More complex to implement and scale
- Stateful — requires sticky sessions or shared state (harder to scale horizontally)
- Some firewalls/proxies block WebSocket connections
- Connection management overhead at large scale (millions of connections)

### Use Cases
- Chat applications
- Online multiplayer gaming
- Live collaboration (Google Docs, Figma)
- Financial tickers, live sports scores
- Real-time dashboards

---

## Server-Sent Events (SSE)
Server pushes events to client over a single, persistent HTTP connection. One-way only (server → client).

```
Client → GET /events (Accept: text/event-stream)
Server → keeps connection open, sends events as they happen
data: {"message": "New order placed"}\n\n
data: {"message": "Order shipped"}\n\n
```

### Pros
- Simple — built into browsers natively (`EventSource` API)
- Auto-reconnect on disconnect
- Works over standard HTTP/2
- No special server setup

### Cons
- One-way only (server → client)
- Client can't send messages (need separate REST call)

### Use Cases
- News/activity feeds
- Live score updates
- Progress bars for long-running jobs
- Notification streams

---

## Comparison Table

| | Short Poll | Long Poll | WebSockets | SSE |
|-|-----------|-----------|------------|-----|
| **Direction** | C→S | C→S | Bidirectional | S→C only |
| **Latency** | High | Medium | Low | Low |
| **Connection** | New each time | New each time | Persistent | Persistent |
| **Overhead** | High | Medium | Low (after handshake) | Low |
| **Complexity** | Very low | Low | Medium | Low |
| **Browser support** | Native | Native | Native | Native |
| **Firewall friendly** | ✅ | ✅ | Sometimes ❌ | ✅ |

---

## Interview Tip
> "For the chat feature, I'd use WebSockets — it needs to be bidirectional (users sending and receiving messages) and low latency. For the activity notification bell that shows 'X new notifications,' SSE would be sufficient and simpler to scale — the server just pushes counts down a persistent HTTP connection and the client shows a badge."
