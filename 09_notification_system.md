# 9. Design a Notification System

## Functional Requirements
- Send notifications via email, SMS, push (mobile/web), in-app
- Support immediate and scheduled delivery
- Bulk notifications for campaigns
- Track delivery status and retries

## Non-Functional Requirements
- High throughput (millions of notifications/minute)
- High availability (99.99%)
- Low latency for real-time notifications
- At-least-once delivery

## High-Level Design
```
[API / Event Trigger] → [Notification Service] → [Kafka]
                                                     ↓
                              ┌──────────────────────┼─────────────────┐
                        [Email Worker]        [SMS Worker]    [Push Worker]
                              ↓                    ↓                ↓
                         [SendGrid]           [Twilio]          [APNS/FCM]
```

## Retry Mechanism
- Failed notifications stored in **Dead Letter Queue (DLQ)**
- Retry with **exponential backoff**: 1s, 2s, 4s, 8s...
- After max retries → alert engineering team

## Priority Queues
- **High priority queue:** OTP codes, payment alerts, security notifications
- **Normal queue:** Marketing emails, recommendations
- **Low priority queue:** Newsletters, weekly digests

## Template Service
- Store notification templates with variable placeholders
- `"Hello {{user.name}}, your order {{order.id}} has shipped!"`
- Render template with user data at send time

## Delivery Tracking
```sql
notification_log:
  id             UUID
  user_id        BIGINT
  channel        ENUM (email, sms, push, in_app)
  template_id    INT
  status         ENUM (queued, sent, delivered, failed)
  sent_at        TIMESTAMP
  delivered_at   TIMESTAMP
```

---
