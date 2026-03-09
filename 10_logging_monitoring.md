# 10. Design a Logging and Monitoring System

## Functional Requirements
- Collect logs from services, servers, databases
- Support structured (JSON) and unstructured log formats
- Query and search logs by time range, level, source
- Set alerts on log patterns or thresholds
- Archive old logs to cheap storage

## Non-Functional Requirements
- Handle millions of log events per second
- Near-real-time ingestion (< 5 second lag)
- High availability
- Long-term retention (years)

## Log Pipeline Architecture
```
[Services / Apps]
       ↓
[Log Agents (Fluentd/Filebeat)] — collect and ship logs
       ↓
[Message Queue (Kafka)] — buffers log bursts
       ↓
[Log Processor (Logstash/Spark Streaming)] — parse, enrich, index
       ↙                    ↘
[Hot Storage             [Cold Storage
 Elasticsearch            S3 / Glacier
 (30-day retention)]      (years, low cost)]
       ↓
[Kibana / Grafana Dashboard]
[Alert Service]
```

## The ELK Stack (Industry Standard)
- **E**lasticsearch — stores and indexes logs
- **L**ogstash — collects, parses, transforms logs
- **K**ibana — visualization and dashboards

## Metrics vs Logs vs Traces

| | Logs | Metrics | Traces |
|-|------|---------|--------|
| **What** | Discrete events with context | Aggregated numerical measurements | Request path through services |
| **Volume** | High | Medium | Medium |
| **Use for** | Debugging specific issues | Dashboards, alerts | Latency attribution |
| **Tools** | ELK, Splunk | Prometheus, Datadog | Jaeger, Zipkin, X-Ray |

## Alerting
- Define alert rules: `error_rate > 1% in last 5 minutes`
- Evaluate rules continuously against incoming log stream
- Fan-out to: PagerDuty (on-call), Slack, email
- **Alert fatigue:** Be selective — not every error needs a page

## Log Retention Policy
- Hot tier (Elasticsearch, SSD): 30 days — fast search
- Warm tier (Elasticsearch, HDD): 30-90 days — slower search
- Cold tier (S3): 1-3 years — cheap, query with Athena
- Archive tier (Glacier): 3+ years — cheapest, minutes to retrieve

---

# Practice Tips

1. **Time yourself** — aim to cover high-level design in 10-12 minutes
2. **Practice the common ones repeatedly** until the design feels natural
3. **Study real architectures** — read Netflix, Uber, Twitter engineering blogs
4. **Focus on trade-offs** — always say "I chose X because Y, but the tradeoff is Z"
5. **Know your numbers** — estimations show experience
6. **Don't memorize** — understand the reasoning behind each choice

## Further Reading
- [High Scalability Blog](http://highscalability.com)
- [AWS Architecture Center](https://aws.amazon.com/architecture/)
- [System Design Primer (GitHub)](https://github.com/donnemartin/system-design-primer)
- [Designing Data-Intensive Applications](https://dataintensive.net/) — Martin Kleppmann

---

*Last updated: 2026*
