# System Design (Basics) — 30 Questions

⭐ = very frequently asked. Freshers get the *conceptual* version of these — you don't need to design Netflix, just understand the building blocks.

---

## A. Core scaling concepts

**1. ⭐ What is scalability?**
A system's ability to handle increased load (more users/data/requests) by adding resources, without performance collapsing. A scalable system grows gracefully.

**2. ⭐ Vertical vs Horizontal scaling?**
**Vertical (scale up)**: add more power to one machine (CPU, RAM) — simple, but has a ceiling and is a single point of failure. **Horizontal (scale out)**: add more machines and distribute load — virtually unlimited, fault-tolerant, but adds complexity (coordination, consistency). Modern large systems scale horizontally.

**3. ⭐ What is a load balancer?**
A component that distributes incoming requests across multiple servers so no single one is overwhelmed — improving availability, throughput, and fault tolerance. Strategies: round-robin, least-connections, IP-hash. If one server dies, the balancer routes around it.

**4. ⭐ What is caching? Why use it?**
Storing frequently-accessed data in a fast layer (memory) so repeat requests don't hit the slow source (database/disk/network). Dramatically reduces latency and load. Examples: Redis, Memcached, CDN, browser cache. The trade-off is **staleness** (cache may be out of date).

**5. ⭐ Cache invalidation — why is it hard?**
Keeping the cache consistent with the source of truth. Strategies: **TTL** (expire after a time), **write-through** (update cache + DB together), **write-back** (update cache, DB later), **cache-aside** (app loads into cache on miss). Famous quote: "There are only two hard things in CS: cache invalidation and naming things."

**6. What is a CDN?**
Content Delivery Network — geographically distributed servers caching static content (images, video, JS/CSS) close to users, cutting latency and offloading the origin server. (Same as Networks Q62.)

**7. ⭐ What is database replication?**
Keeping copies of the database on multiple servers. **Master-slave**: writes to master, reads from replicas (scales reads, adds redundancy). Trade-off: **replication lag** → replicas may be slightly stale (eventual consistency).

**8. ⭐ What is sharding (database partitioning)?**
Splitting a database **horizontally** across servers, each holding a subset of rows (e.g. users A-M on shard 1, N-Z on shard 2). Lets data exceed one machine. Challenges: choosing a good **shard key**, cross-shard queries, rebalancing.

**9. Replication vs Sharding?**
**Replication**: *copies* of the same data on multiple servers (for availability + read scaling). **Sharding**: *different* subsets of data on different servers (for write/storage scaling). Often used together: shard the data, replicate each shard.

**10. ⭐ What is the CAP theorem (system design context)?**
In a distributed system you can guarantee at most 2 of: **Consistency** (all nodes see the same data), **Availability** (every request gets a response), **Partition tolerance** (works despite network failures). Since partitions are inevitable, you choose **CP** (consistency over availability) or **AP** (availability over consistency).

---

## B. Architecture patterns

**11. ⭐ Monolith vs Microservices?**
**Monolith**: the whole app is one deployable unit — simple to build/deploy initially, but hard to scale parts independently and risky to change. **Microservices**: the app is split into small, independent services communicating over the network — independently scalable/deployable, but adds operational complexity (networking, monitoring, data consistency). Start monolith, split when needed.

**12. What is an API gateway?**
A single entry point in front of microservices that routes requests, handles auth, rate limiting, and aggregation — so clients don't talk to each service directly. Simplifies the client and centralizes cross-cutting concerns.

**13. ⭐ What is a message queue? Why use one?**
A component (RabbitMQ, Kafka, SQS) that lets services communicate **asynchronously** by passing messages through a buffer. Producers add messages; consumers process them later. Benefits: **decoupling** (services don't wait on each other), **load smoothing** (handle spikes), **reliability** (messages persist if a consumer is down). e.g. sending emails in the background.

**14. Synchronous vs Asynchronous communication?**
**Synchronous**: the caller waits for a response (HTTP request-response) — simple but couples services and blocks. **Asynchronous**: the caller sends a message and continues; processing happens later (message queues) — decoupled, resilient, but harder to track. *(This async idea links directly to the event-loop concepts behind my OSS work.)*

**15. What is rate limiting?**
Restricting how many requests a client can make in a time window (e.g. 100/minute) to prevent abuse, ensure fair use, and protect the system from overload (and DDoS). Algorithms: token bucket, leaky bucket, fixed/sliding window. (HTTP 429 = rate limited.)

**16. ⭐ What is a single point of failure (SPOF)?**
A component whose failure brings down the whole system. Eliminated by **redundancy** — replicate components, use load balancers, multiple servers/regions, so no single failure is fatal. High availability = no SPOFs.

**17. What is high availability? What is fault tolerance?**
**High availability**: the system stays up almost all the time (measured in "nines" — 99.99% = ~52 min downtime/year), via redundancy + failover. **Fault tolerance**: the system keeps working *correctly* even when components fail. Achieved by replication, redundancy, graceful degradation.

**18. What is eventual consistency?**
A consistency model where, after an update, replicas may temporarily disagree but will *eventually* converge to the same value (if no new updates). Trades immediate consistency for availability/performance. Common in distributed NoSQL systems (AP in CAP). e.g. a "like" count that's briefly off.

---

## C. Data & storage

**19. ⭐ SQL vs NoSQL — how to choose?**
**SQL**: structured data, complex relationships/queries, strong consistency (ACID), moderate scale → e.g. banking, e-commerce orders. **NoSQL**: massive scale, flexible/unstructured data, high write throughput, can tolerate eventual consistency → e.g. user sessions, logs, social feeds. Pick based on data structure, scale, and consistency needs. (See DBMS Q62.)

**20. What are the NoSQL database types?**
**Key-value** (Redis, DynamoDB — simple, fast lookups), **Document** (MongoDB — JSON-like, flexible), **Column-family** (Cassandra — huge writes, time-series), **Graph** (Neo4j — relationships/networks). Choose by access pattern.

**21. What is a primary-replica (read replica) setup?**
Writes go to the primary database; reads are spread across replicas. Scales read-heavy workloads (most apps read far more than write). Replicas lag slightly behind (eventual consistency for reads).

**22. What is database indexing's role in system design?**
Indexes make queries fast at scale (O(log n) vs O(n) scans), but slow writes and use storage. Critical for read performance; over-indexing hurts write-heavy systems. (See DBMS Q39.)

**23. What is a write-heavy vs read-heavy system? How do they differ in design?**
**Read-heavy** (social feed): add caching, read replicas, CDNs. **Write-heavy** (logging, IoT): use sharding, write-optimized stores (Cassandra), message queues to buffer writes, minimal indexing. Identify the ratio first — it drives the architecture.

---

## D. Reliability & operations

**24. What is idempotency in system design? Why important?**
An operation that, repeated, has the same effect as doing it once. Crucial for **safe retries** in distributed systems — if a network hiccup makes a client retry a payment, idempotency (via an idempotency key) ensures it's not charged twice. (See Networks Q38.)

**25. What is graceful degradation?**
When part of a system fails, it degrades functionality instead of crashing entirely — e.g. if the recommendation service is down, show generic content rather than an error page. Keeps the core experience working.

**26. What is a heartbeat / health check?**
A periodic signal a service sends (or an endpoint a load balancer polls) to confirm it's alive. If heartbeats stop, the system marks it unhealthy and stops routing traffic to it (failover). Basis of monitoring and auto-recovery.

**27. What is back-of-the-envelope estimation? Why do it?**
Quick rough calculations of scale during design: requests/second, storage needed, bandwidth. e.g. "1M users × 10 requests/day ÷ 86400s ≈ 116 req/s." It validates whether a design is feasible and where bottlenecks are *before* building.

**28. What is a bottleneck? How do you find one?**
The component limiting overall system throughput (the slowest part). Found via **monitoring/profiling** (metrics, logs, traces). You scale or optimize the bottleneck first — improving anything else won't help until it's fixed.

---

## E. Putting it together

**29. ⭐ How would you design a URL shortener (TinyURL)? (a classic fresher Q)**
Core: map a long URL ↔ a short code. **Write**: generate a unique short code (base-62 of an auto-increment ID, or a hash), store the mapping in a DB. **Read**: look up the code → redirect (HTTP 301/302). **Scale**: it's read-heavy → add caching (Redis) for popular links, read replicas, a CDN. Handle collisions (if hashing) and consider the key space (6 base-62 chars ≈ 56 billion URLs). Mention DB choice (key-value fits perfectly).

**30. ⭐ Walk through designing any system (the framework).**
1. **Clarify requirements** (functional + non-functional: scale, latency, consistency). 2. **Estimate** scale (users, QPS, storage). 3. **Define the API** (endpoints). 4. **Design the data model** (SQL/NoSQL, schema). 5. **High-level architecture** (clients → load balancer → servers → cache → DB). 6. **Deep-dive** the tricky part (sharding, caching, the bottleneck). 7. **Address bottlenecks & failures** (SPOFs, scaling, consistency). Saying this framework out loud structures any design answer.

---

## ⭐ The 10 System Design must-knows:
Vertical vs horizontal scaling (2) · Load balancer (3) · Caching + invalidation (4,5) · Replication vs sharding (9) · CAP theorem (10) · Monolith vs microservices (11) · Message queue / async (13,14) · SQL vs NoSQL choice (19) · SPOF + high availability (16,17) · the design framework (30).
