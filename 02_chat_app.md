# 2. Design a Chat Application (WhatsApp)

## Functional Requirements
- One-on-one and group messaging
- Online/offline status
- Message delivery receipts (sent, delivered, read)
- Multimedia messages (images, video, audio)

## Non-Functional Requirements
- Real-time message delivery (< 100ms latency)
- High availability (users expect chat to always work)
- Durability (messages must not be lost)
- Support millions of concurrent connections

## High-Level Design
```
[Client] ←WebSocket→ [Chat Server / Load Balancer]
                              ↓             ↓
                        [Message DB]   [Presence Service]
                              ↓
                       [Notification Service]
                       [Media Service → S3]
```

## WebSocket Connection Management
- Each client maintains a **persistent WebSocket connection** to a chat server
- When user A sends message to user B:
  1. Message arrives at user A's chat server
  2. Chat server looks up which server user B is connected to (via **Session Service / Redis**)
  3. Routes message to user B's chat server
  4. User B's server pushes message over WebSocket

## Message Storage (Database Design)

### For 1:1 Messages
```
messages:
  message_id    BIGINT (Snowflake ID)
  conversation_id BIGINT (INDEX)
  sender_id     BIGINT
  content       TEXT
  content_type  ENUM (text, image, video, audio)
  created_at    TIMESTAMP
  delivered_at  TIMESTAMP
  read_at       TIMESTAMP
```

### Database Choice: Why Cassandra?
- Write-heavy (every message is a write)
- Time-ordered queries (`SELECT * WHERE conversation_id = X ORDER BY time DESC LIMIT 20`)
- Horizontal scaling built-in
- WhatsApp actually uses Mnesia/Erlang, Messenger uses HBase, Instagram uses Cassandra

## Offline Message Delivery
- If recipient offline → message stored in DB
- When they reconnect → server pushes all missed messages
- **Push notifications** (APNS/FCM) to wake up mobile app

## Message Ordering
- Use **Snowflake IDs** for message IDs — they're time-ordered globally
- Clients display messages sorted by message_id

## Group Chat
- Each group has a `group_id`
- Fanout: when message sent to group, server fans out to all members
- For large groups: fan out to individual message queues per user

## Media Handling
1. Client uploads to S3 directly via **pre-signed URL**
2. S3 URL stored in message content
3. CDN sits in front of S3 for fast retrieval

---
