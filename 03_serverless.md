# 3. Serverless Architecture

## Overview
Abstracts away all infrastructure. Developers write functions; the cloud provider manages servers, scaling, and availability automatically.

```
Event → [Cloud Function (Lambda/Cloud Run)] → Response
```

## How It Works
- Deploy **individual functions** (not applications)
- Functions are triggered by **events** (HTTP request, file upload, timer, queue message)
- Provider automatically scales to zero (no traffic = no cost) or to thousands of instances
- Pay only for compute time used (per-invocation billing)

## Key Platforms
| Platform | Provider |
|----------|---------|
| AWS Lambda | Amazon |
| Azure Functions | Microsoft |
| Google Cloud Functions / Cloud Run | Google |
| Cloudflare Workers | Cloudflare |
| Vercel / Netlify Functions | Edge-focused |

## Benefits
- **No server management** — zero ops overhead
- **Automatic scaling** — handles spikes without configuration
- **Cost efficient** — pay only when code runs (great for sporadic workloads)
- **Faster time to market** — focus purely on code

## Challenges
- **Cold Starts:** First invocation after idle period has extra latency (100ms–2s)
- **Execution limits:** Max duration (AWS Lambda: 15 minutes)
- **Vendor lock-in:** Hard to move between providers
- **State management:** Functions are stateless — need external state (DynamoDB, Redis)
- **Debugging complexity:** Harder to reproduce issues locally
- **Cost at scale:** Can become expensive under constant high load

## Best Use Cases
- Event-driven tasks (image processing on upload, email triggers)
- Webhooks and API backends with sporadic traffic
- Scheduled jobs (cron replacements)
- Real-time data transformations
- Glue code between services

## NOT great for
- Long-running tasks (> 15 minutes)
- High-throughput constant load (EC2 might be cheaper)
- Applications needing local state
- Latency-sensitive applications (cold starts)

---
