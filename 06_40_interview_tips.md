# 40 System Design Interview Tips

Tactical advice to perform at your best in system design interviews.

---

## Requirements & Scoping

1. **Always clarify functional and non-functional requirements before designing.** Starting without this is the most common mistake.

2. **Clearly define use cases and constraints.** Ask: "Should I design for X users or Y users? Global or regional?"

3. **Check if capacity estimation is expected.** Some interviewers skip it — ask to be sure you're using time wisely.

4. **Agree on what's in scope.** If you only have 45 minutes, you can't design all of YouTube. Pick the key flows.

---

## Mindset

5. **There is no perfect solution. It's all about trade-offs.** Say this out loud. Interviewers want to see you weigh options.

6. **Keep it simple. Avoid over-engineering.** Don't start with Kubernetes and 12 microservices for a URL shortener.

7. **Assume everything can and will fail. Design for fault tolerance.** Build in redundancy, retries, and circuit breakers.

8. **Don't wait to be asked.** Proactively bring up concerns: "I'd like to talk about how this handles failures — is that okay?"

9. **Iterate your design.** Start with a simple solution, then scale it. Don't jump to the most complex answer.

---

## Scalability

10. **Design for scalability from the ground up.** Stateless services, horizontal scaling, avoid shared mutable state.

11. **Prefer horizontal scaling over vertical scaling.** Scale-out is more resilient and cost-effective at large scale.

12. **Use Load Balancers to ensure high availability and distribute traffic.** Layer 7 (ALB) for HTTP routing, Layer 4 (NLB) for pure throughput.

13. **Consider using a message queue for asynchronous communication.** Decouple services, absorb traffic spikes, enable retries.

14. **Implement autoscaling to handle traffic spikes smoothly.** Let cloud infrastructure scale up and down automatically.

15. **Use batch processing for non-urgent tasks to optimize resources.** Daily reports, ML training, ETL jobs — no need to run real-time.

16. **Implement data partitioning and sharding for large datasets.** Hash-based sharding for even distribution; range-based for locality.

---

## Database

17. **Consider using SQL Databases for structured data and ACID transactions.** Financial systems, inventory, user accounts.

18. **Opt for NoSQL Databases when dealing with unstructured data.** High write volume, flexible schema, massive scale.

19. **Consider using a graph database for highly connected data.** Social networks, fraud detection, recommendation engines.

20. **Use Database Sharding to scale SQL databases horizontally.** Partition by user_id hash, date range, or geography.

21. **Use Database Indexing to optimize read queries.** Index columns used in WHERE, JOIN, and ORDER BY.

22. **Consider denormalizing databases for read-heavy workloads.** Pre-join data to avoid expensive queries at read time.

23. **Implement Data Replication and Redundancy to avoid single points of failure.** Primary-replica replication for reads and failover.

---

## Caching

24. **Use caching to reduce load on the database and improve response times.** Target the 20% of data that serves 80% of reads.

25. **Use write-through cache for write-heavy applications.** Keeps cache and DB in sync.

26. **Use read-through cache for read-heavy applications.** Cache automatically loaded on miss.

27. **Use bloom filters to check for an item in a large dataset quickly.** Avoid expensive DB lookups for likely non-existent items.

28. **Use Time-To-Live (TTL) values to automatically expire cached data.** Reduces staleness without manual invalidation.

29. **Pre-populate critical data in the cache to avoid cold starts.** Warm the cache before traffic hits (e.g., before product launches).

---

## Network & Delivery

30. **Use CDNs to reduce latency for a global user base.** Serve static assets from edge nodes close to users.

31. **Use object storage like S3 for storing large datasets and media files.** 11 nines durability, cheap, infinitely scalable.

32. **Consider using WebSockets for real-time communication.** Chat, live collaboration, gaming, financial tickers.

---

## Reliability & Fault Tolerance

33. **Use Heartbeat Mechanisms to detect failures.** Periodic signals between components; if missed, trigger failover.

34. **Use Rate Limiting to prevent system overload and DDoS attacks.** Token bucket or sliding window at the API Gateway layer.

35. **Implement circuit breakers to prevent cascading failures.** If service B is down, don't let it take down service A.

36. **Implement retry mechanisms with exponential backoff.** 1s, 2s, 4s... with jitter to avoid thundering herd.

37. **Use failover mechanisms to automatically switch to a redundant system.** Active-passive or active-active failover.

38. **Distribute your system across different data centers to prevent localized failures.** Multi-AZ minimum; multi-region for global apps.

---

## Architecture & Design Principles

39. **Use asynchronous processing for background tasks.** Don't block the user request — queue long-running work.

40. **Design for statelessness when possible to improve scalability and simplify architecture.** Store state in Redis/DB, not on app servers.

---

## Bonus Tips

41. **Make operations idempotent to simplify retry logic.** Same request processed twice → same result. Especially important for payments.

42. **Implement comprehensive logging and monitoring.** You can't fix what you can't see. RED metrics: Rate, Errors, Duration.

43. **Implement chaos engineering practices to test system resilience.** Intentionally kill nodes, inject latency, observe behavior.

44. **Consider using a data lake or data warehouse for analytics.** S3 + Athena / Redshift / BigQuery for analytical workloads.

45. **Use CQRS when read and write patterns are very different.** Separate read models (denormalized) from write models (normalized).

---

## Communication Tips for Interviews

| Do | Don't |
|----|-------|
| Think out loud | Design in silence, then reveal |
| Ask clarifying questions upfront | Assume and design the wrong thing |
| State trade-offs explicitly | Present one option as the only option |
| Draw as you explain | Just talk without visuals |
| Invite feedback ("Does this approach work?") | Monologue for 30 minutes |
| Time-box each section | Spend 20 minutes on requirements |
| Start simple, evolve | Start with over-engineered solution |
| Own what you don't know | Pretend to know everything |

---

## How Interviewers Evaluate You

| Dimension | What They're Looking For |
|-----------|--------------------------|
| **Problem Solving** | Break down ambiguous problems into solvable pieces |
| **Technical Depth** | Understand how components actually work |
| **Trade-offs** | Can you articulate pros/cons of each choice? |
| **Scale Awareness** | Do your designs actually work at the stated scale? |
| **Communication** | Can you explain complex ideas clearly? |
| **Drive** | Do you proactively address issues without being prompted? |

---

*These tips apply to interviews at all levels — junior, senior, and staff+. At higher levels, expect deeper technical questions and more focus on trade-offs.*
