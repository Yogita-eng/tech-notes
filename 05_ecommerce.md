# 5. Design an E-Commerce Platform (Amazon)

## Functional Requirements
- Product listings with search
- Shopping cart
- Order placement and management
- Payments
- Product reviews

## Non-Functional Requirements
- High availability, especially during flash sales
- Low latency for product search
- Strong consistency for inventory and payments
- Scalable to Black Friday traffic levels

## Key Services (Microservices)
```
[API Gateway]
      ↓
[User Service] [Product Service] [Order Service] [Payment Service]
                      ↓                ↓               ↓
                 [Search (ES)]    [Inventory DB]   [Payment DB]
```

## Inventory Management — The Hard Problem
**Challenge:** Two users shouldn't be able to buy the last item simultaneously.

**Solution:** Optimistic Locking
```sql
UPDATE inventory SET quantity = quantity - 1, version = version + 1
WHERE product_id = X AND version = known_version AND quantity > 0;
-- If 0 rows affected → someone else bought it → retry/fail
```

## Payment Processing
1. Validate order
2. Reserve inventory (optimistic lock)
3. Initiate payment (external payment gateway: Stripe, PayPal)
4. If payment succeeds → confirm order, deduct inventory
5. If payment fails → release inventory reservation

Use **Saga Pattern** for distributed transaction:
- Each step publishes event
- Compensating transactions roll back on failure

## Product Search
- Elasticsearch for full-text search
- Faceted search (filter by price, brand, rating)
- Sync product catalog to ES via change data capture (CDC) from primary DB

## Shopping Cart
- **Redis** for cart storage (fast, temporary)
- Cart data: `{user_id: {product_id: quantity, ...}}`
- Persist to DB when order placed

## Flash Sale Architecture
- **Pre-warm inventory in Redis** (avoid DB hit during sale)
- **Queue system** — place orders in queue, process serially
- **Oversell protection** — Redis `DECRBY` with Lua script (atomic)
- **CDN** for product pages (avoid serving same static page from app servers)

---
