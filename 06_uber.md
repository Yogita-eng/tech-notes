# 6. Design a Ride-Sharing Service (Uber)

## Functional Requirements
- See nearby available drivers
- Request a ride (pickup + dropoff)
- Match rider with nearby driver
- Real-time GPS tracking during ride
- Fare estimation

## Non-Functional Requirements
- Low latency matching (< 5 seconds)
- Real-time location updates
- High consistency: no driver matched to two riders simultaneously
- Highly available

## Location Service
**Challenge:** Efficiently find drivers within X km of a rider.

**Solution: Geohash or S2 Cells**
- Divide the world into a grid of cells (geohash string: `9q8yy`)
- Each cell has a unique string ID
- Drivers in the same or adjacent cells are nearby

```
Driver updates location every 5 seconds
→ [WebSocket / HTTPS] → Location Service → Redis GeoSet
                                             (GEOADD driver_locations lon lat driver_id)

Rider requests ride:
→ GEORADIUS driver_locations lon lat 5 km → list of nearby drivers
```

## Matching Algorithm
1. Find all available drivers within 5km via Geohash + Redis GeoSearch
2. Sort by distance, estimated time to pickup
3. Offer ride to closest driver
4. If driver declines/no response (10s timeout) → offer to next
5. Lock driver as "matched" atomically (Redis atomic operation) to prevent double-booking

## Trip Tracking
- Driver app sends GPS location every 3-5 seconds via WebSocket
- Location Service updates Redis
- Rider app subscribes to driver's location updates (WebSocket push)

## Surge Pricing
- Monitor supply (available drivers) and demand (ride requests) per geohash cell
- If demand >> supply → increase multiplier
- Feed data to pricing service via Kafka stream

## Database
```
users:     user_id, name, phone, rating
drivers:   driver_id, user_id, car_info, rating, status
trips:     trip_id, rider_id, driver_id, pickup, dropoff, status, fare, created_at
locations: (ephemeral in Redis, persist to DB for analytics)
```

---
