# Batch vs Stream Processing

## Quick Decision

```
Data doesn't need to be fresh (daily reports, ETL)?  → Batch
Need real-time insights / instant reactions?          → Stream
Need both accuracy and recency?                       → Lambda Architecture
```

---

## Batch Processing
Collect data over time, then process the entire dataset at once in a scheduled job.

```
Data accumulates → [Batch Job at 2AM] → Process all data → Output results (delayed)
```

### Pros
- Simple to implement and reason about
- Efficient for large data volumes (optimized sequential I/O)
- Lower cost — run during off-peak hours
- Easy to reprocess if something fails (just re-run the job)
- Naturally handles historical data

### Cons
- **High latency** — results only available after the full batch runs
- Resource spikes when the batch job runs
- Not suitable for anything requiring real-time action

### Use Cases
- Daily/weekly reports and dashboards
- ETL pipelines (Extract → Transform → Load into warehouse)
- Payroll processing
- Overnight recommendation model training
- Backup and archival jobs

### Tools
Apache Hadoop MapReduce, Apache Spark (batch), AWS Glue, dbt

---

## Stream Processing
Process data **in real-time** as it arrives — record by record or in micro-batches.

```
Data arrives → [Stream Processor] → Process immediately → Instant output
```

### Pros
- **Low latency** (milliseconds to seconds)
- Real-time insights and immediate action
- Continuous, always-on processing
- Can trigger immediate alerts or actions

### Cons
- More complex architecture
- Harder to handle out-of-order or late-arriving events
- More expensive (always-on compute)
- Exactly-once semantics can be tricky to implement

### Use Cases
- Real-time fraud detection (act before transaction completes)
- Live dashboards and monitoring
- IoT sensor data processing
- Clickstream analytics
- Real-time recommendations
- Log anomaly detection and alerting

### Tools
Apache Kafka + Kafka Streams, Apache Flink, Apache Spark Streaming, AWS Kinesis, Google Dataflow

---

## Comparison

| | Batch | Stream |
|-|-------|--------|
| **Latency** | High (hours/days) | Low (milliseconds/seconds) |
| **Throughput** | Very high (optimized) | High |
| **Complexity** | Low | Higher |
| **Cost** | Lower (scheduled) | Higher (always-on) |
| **Data freshness** | Stale | Fresh |
| **Reprocessing** | Easy (re-run job) | Harder |
| **Best for** | Historical analysis, ETL | Real-time decisions, alerts |

---

## Lambda Architecture (Hybrid)
Combines both to get accuracy and speed:

```
Data → [Batch Layer]   → slow, accurate historical computation
     → [Speed Layer]   → fast, approximate real-time computation
                            ↓
                      [Serving Layer] → merges both for queries
```

Used by: LinkedIn, Netflix, Twitter for analytics at scale

---

## Interview Tip
> "For the analytics dashboard, most metrics don't need to be real-time — daily active users, revenue summaries, and cohort analysis can be batch processed nightly using Spark and loaded into a data warehouse. But for fraud detection, I need to evaluate each transaction as it happens — that requires a stream processor like Flink consuming from Kafka with sub-second latency."
