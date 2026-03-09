# Proxy Servers

## What Is It?
A proxy server is an **intermediary** between clients and servers that forwards requests on their behalf.

---

## Forward Proxy

Sits **in front of clients** — acts on behalf of the client toward the internet.

```
[Client] → [Forward Proxy] → [Internet / Server]
```

**Use cases:**
- **Anonymity:** Hide the client's real IP from servers
- **Content filtering:** Block certain websites (corporate firewalls)
- **Caching:** Cache responses for the internal network
- **Bypass restrictions:** Access geo-blocked content

**Who configures it:** The client / IT admin

**Examples:** Squid, corporate VPNs

---

## Reverse Proxy

Sits **in front of servers** — acts on behalf of the server toward clients. Clients think they're talking directly to the server.

```
[Client] → [Reverse Proxy] → [Server(s)]
```

**Use cases:**
- **Load balancing:** Distribute requests across multiple backend servers
- **SSL termination:** Decrypt HTTPS once at the proxy, plain HTTP to servers
- **Caching:** Cache responses to reduce backend load
- **DDoS protection:** Filter malicious traffic before it reaches your servers
- **API Gateway:** Auth, rate limiting, routing

**Who configures it:** The server/infrastructure owner

**Examples:** Nginx, HAProxy, AWS ALB, Cloudflare

---

## Forward vs Reverse at a Glance

| | Forward Proxy | Reverse Proxy |
|-|---------------|---------------|
| **Sits in front of** | Client | Server |
| **Protects** | Client identity | Server infrastructure |
| **Client knows about proxy?** | Yes — client configures it | No — transparent |
| **Common use** | Privacy, filtering | Load balancing, SSL, security |
| **Examples** | Squid, VPN | Nginx, HAProxy, CloudFront |

---

## Nginx as Reverse Proxy (Common Setup)

```
[Browser] → [Nginx reverse proxy :443]
                  ↓ (SSL termination, routes by path)
         /api/* → [App Server :8080]
         /static/* → [S3 / CDN]
         /ws/* → [WebSocket Server :8081]
```

---

## Interview Tip
> "I'd put Nginx as a reverse proxy in front of the application servers. It handles SSL termination — so our app servers only deal with plain HTTP internally — and provides load balancing across multiple app instances. It also serves static files directly without touching the app layer."
