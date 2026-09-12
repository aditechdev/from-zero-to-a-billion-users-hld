# 🚀 From Zero to a Billion Users — HLD Prep

System design case studies and fundamentals, worked through from scratch —
how a system scales from a handful of users to a billion.

---

## 📖 Table of Contents

### 01 · 🧱 Fundamentals

**[What is HLD?](01-fundamentals/01-hld-fundamentals.md)**  
`What is HLD` · `Functional Requirements` · `Non-Functional Requirements` · `Architecture Thinking`

**[Capacity Estimation](01-fundamentals/02-capacity-estimation.md)**  
`Capacity Estimation` · `Traffic Estimation` · `RPS` · `Read vs Write Traffic` · `Peak RPS`

---
<!-- 
### 02 · 🏗️ Architecture

| Topic |
|---|
| [Client-Server](02-architecture/client-server.md) |
| [Load Balancer](02-architecture/load-balancer.md) |
| [Reverse Proxy](02-architecture/reverse-proxy.md) |
| [API Gateway](02-architecture/api-gateway.md) |

---

### 03 · 🗄️ Databases

| Topic |
|---|
| [SQL vs NoSQL](03-databases/sql-vs-nosql.md) |
| [Replication](03-databases/replication.md) |
| [Partitioning](03-databases/partitioning.md) |
| [Sharding](03-databases/sharding.md) |

---

### 04 · ⚡ Caching

| Topic |
|---|
| [Caching Fundamentals](04-caching/caching-fundamentals.md) |
| [Cache Strategies](04-caching/cache-strategies.md) |
| [Cache Invalidation](04-caching/cache-invalidation.md) |

---

### 05 · 📨 Messaging

| Topic |
|---|
| [Queues](05-messaging/queues.md) |
| [Workers](05-messaging/workers.md) |
| [Acknowledgements](05-messaging/acknowledgements.md) |
| [Dead Letter Queue](05-messaging/dead-letter-queue.md) |

---

### 06 · 🔀 Kafka

| Topic |
|---|
| [Kafka Basics](06-kafka/kafka-basics.md) |
| [Partitions](06-kafka/partitions.md) |
| [Consumer Groups](06-kafka/consumer-groups.md) |
| [Delivery Semantics](06-kafka/delivery-semantics.md) |

---

### 07 · 🛡️ Reliability

| Topic |
|---|
| [Timeouts](07-reliability/timeouts.md) |
| [Retries](07-reliability/retries.md) |
| [Circuit Breaker](07-reliability/circuit-breaker.md) |
| [Idempotency](07-reliability/idempotency.md) |

---

### 08 · 🌐 Distributed Systems

| Topic |
|---|
| [CAP Theorem](08-distributed-systems/cap-theorem.md) |
| [Consistency](08-distributed-systems/consistency.md) |
| [Consensus (Raft/Paxos)](08-distributed-systems/consensus.md) |
| [Distributed Locks](08-distributed-systems/distributed-locks.md) |

---

### 09 · 🧩 Advanced Patterns

| Topic |
|---|
| [CQRS](09-advanced/cqrs.md) |
| [Event Sourcing](09-advanced/event-sourcing.md) |
| [Saga Pattern](09-advanced/saga.md) |
| [Outbox Pattern](09-advanced/outbox-pattern.md) |

---

### 10 · 🏢 Case Studies

| System |
|---|
| [URL Shortener](10-case-studies/url-shortener.md) |
| [Instagram](10-case-studies/instagram.md) |
| [WhatsApp](10-case-studies/whatsapp.md) |
| [Notification System](10-case-studies/notification-system.md) |
| [Payment System](10-case-studies/payment-system.md) |

---
-->
## 🧭 How to Use This Repo

Each topic follows a standard note format:

**Problem → Prerequisites → Theory → Diagram → Code (when relevant) → Real Example → Interview Questions → Mistakes / Gotchas → Key Takeaways → References (when useful).**

Start with `01-fundamentals/` and work through the topics in order.

The keywords listed below each topic make it easy to search for specific concepts using `Ctrl+F` or GitHub search.

## RoadMap

## 🗺️ Roadmap

```text
01 · HLD FUNDAMENTALS (~8–11 hrs)
│
├── HLD Fundamentals (~2–3 hrs)
│   ├── What is HLD? (~20 min)                 ✅
│   ├── Why HLD? (~15 min)                     ✅
│   ├── Functional Requirements (~20 min)     ✅
│   ├── Non-Functional Requirements (~25 min) ✅
│   ├── Functional vs Non-Functional (~15 min)✅
│   └── Basic HLD Thinking (~30–40 min)       ✅
│
└── Capacity Estimation (~6–8 hrs)
    │
    ├── Traffic Estimation (~2 hrs)
    │   ├── Requests / Day (~20 min)           ✅
    │   ├── Average RPS (~20 min)              ✅
    │   ├── Peak RPS (~20 min)                 ✅
    │   ├── Read vs Write Traffic (~20 min)    ✅
    │   └── Traffic Estimation Practice (~40 min) ✅
    │
    ├── Storage Estimation (~2 hrs)
    │   ├── Basic Storage Calculation (~20 min)   ✅
    │   ├── Estimating Record Size (~30 min)      ✅
    │   ├── Storage Growth Over Time (~25 min)    ⬅️ NEXT
    │   ├── Index Overhead (~20 min)
    │   ├── Replication Overhead (~15 min)
    │   └── Backup / Retention (~15 min)
    │
    ├── Bandwidth Estimation (~1–1.5 hrs)
    │   ├── Request Bandwidth (~20 min)
    │   ├── Response Bandwidth (~25 min)
    │   └── Total Bandwidth (~30 min)
    │
    └── Memory Estimation (~1–1.5 hrs)
        ├── Working Set (~25 min)
        ├── Cache Memory (~25 min)
        └── Memory Estimation (~30 min)


02 · ARCHITECTURE FUNDAMENTALS (~6–8 hrs)
│
├── Client-Server Architecture (~30 min)
├── Request / Response Flow (~25 min)
├── DNS (~40 min)
├── Load Balancer (~1.5 hrs)
│   ├── L4 vs L7 (~30 min)
│   ├── Load Balancing Algorithms (~30 min)
│   └── Health Checks (~20 min)
├── Reverse Proxy (~30 min)
├── API Gateway (~40 min)
├── Horizontal Scaling (~30 min)
├── Vertical Scaling (~20 min)
├── Stateless vs Stateful (~40 min)
├── Monolith (~25 min)
├── Microservices (~40 min)
└── Service Discovery (~30 min)


03 · DATABASES (~10–14 hrs)
│
├── Database Fundamentals (~45 min)
├── SQL vs NoSQL (~1 hr)
├── Data Modeling (~1 hr)
├── Indexes (~2 hrs)
│   ├── Why Indexes? (~20 min)
│   ├── B-Tree Index (~30 min)
│   ├── Composite Index (~25 min)
│   ├── Index Selectivity (~20 min)
│   └── Index Trade-offs (~20 min)
├── Query Patterns (~45 min)
├── Database Bottlenecks (~1 hr)
│   ├── CPU (~10 min)
│   ├── Memory (~10 min)
│   ├── Disk I/O (~15 min)
│   ├── Connections (~10 min)
│   └── Slow Queries (~15 min)
├── Replication (~1.5 hrs)
│   ├── Primary / Replica (~25 min)
│   ├── Read Replicas (~25 min)
│   └── Replication Lag (~30 min)
├── Partitioning (~45 min)
├── Sharding (~2 hrs)
│   ├── Shard Key (~25 min)
│   ├── Hash Sharding (~20 min)
│   ├── Range Sharding (~20 min)
│   ├── Hot Partitions (~25 min)
│   └── Rebalancing (~30 min)
└── Database Scaling (~1 hr)
    ├── Read Scaling (~20 min)
    ├── Write Scaling (~20 min)
    └── Connection Pooling (~20 min)


04 · CACHING (~5–7 hrs)
│
├── Why Caching? (~25 min)
├── Cache Fundamentals (~30 min)
├── Cache-Aside (~30 min)
├── Read-Through (~25 min)
├── Write-Through (~25 min)
├── Write-Back (~25 min)
├── TTL (~20 min)
├── Cache Eviction (~45 min)
│   ├── LRU (~20 min)
│   ├── LFU (~15 min)
│   └── FIFO (~10 min)
├── Cache Invalidation (~40 min)
├── Cache Stampede (~30 min)
├── Cache Penetration (~20 min)
├── Cache Avalanche (~20 min)
├── Hot Keys (~25 min)
└── Distributed Cache (~40 min)


05 · MESSAGING & QUEUES (~8–10 hrs)
│
├── Synchronous vs Asynchronous (~30 min)
├── Queue Fundamentals (~30 min)
├── Producer (~15 min)
├── Consumer (~15 min)
├── Worker (~15 min)
├── Worker Pool (~25 min)
├── Acknowledgements (~30 min)
├── Retry (~25 min)
├── Exponential Backoff (~25 min)
├── Jitter (~20 min)
├── Dead Letter Queue (~30 min)
├── Poison Messages (~20 min)
├── Message Ordering (~30 min)
├── Duplicate Messages (~30 min)
├── Idempotent Consumers (~40 min)
├── Backpressure (~30 min)
└── Delivery Semantics (~1 hr)
    ├── At-most-once (~15 min)
    ├── At-least-once (~20 min)
    └── Exactly-once (~25 min)


06 · KAFKA (~8–12 hrs)
│
├── Why Kafka? (~30 min)
├── Kafka Fundamentals (~1 hr)
├── Topics (~25 min)
├── Partitions (~1 hr)
├── Producers (~40 min)
├── Consumers (~40 min)
├── Consumer Groups (~45 min)
├── Offsets (~40 min)
├── Ordering (~30 min)
├── Replication (~45 min)
├── Leader / Follower (~40 min)
├── Rebalancing (~45 min)
├── Consumer Lag (~30 min)
└── Delivery Semantics (~45 min)


07 · RELIABILITY (~8–12 hrs)
│
├── Timeouts (~30 min)
├── Retries (~30 min)
├── Exponential Backoff (~25 min)
├── Jitter (~20 min)
├── Circuit Breaker (~45 min)
├── Bulkhead (~30 min)
├── Rate Limiting (~45 min)
├── Load Shedding (~30 min)
├── Graceful Degradation (~30 min)
├── Backpressure (~30 min)
├── Health Checks (~25 min)
├── Liveness / Readiness (~30 min)
├── SLA (~25 min)
├── SLI (~25 min)
├── SLO (~30 min)
├── Error Budget (~30 min)
├── RPO (~20 min)
├── RTO (~20 min)
└── Failure Scenarios (~2 hrs)
    ├── Server Failure (~20 min)
    ├── Database Failure (~20 min)
    ├── Cache Failure (~15 min)
    ├── Queue Failure (~15 min)
    ├── Network Failure (~25 min)
    └── Region Failure (~25 min)


08 · DISTRIBUTED SYSTEMS (~15–20 hrs)
│
├── Why Distributed Systems? (~40 min)
├── Network Failures (~45 min)
├── Partial Failures (~45 min)
├── Latency (~45 min)
├── Network Partitions (~45 min)
├── Replication (~1 hr)
├── Partitioning (~1 hr)
├── Consistency (~2 hrs)
│   ├── Strong Consistency (~30 min)
│   ├── Eventual Consistency (~40 min)
│   └── Causal Consistency (~40 min)
├── Availability (~30 min)
├── CAP Theorem (~1 hr)
├── Quorum (~45 min)
├── Leader / Follower (~30 min)
├── Leader Election (~45 min)
├── Distributed Locks (~1 hr)
├── Ordering (~45 min)
├── Idempotency (~45 min)
├── Consensus (~3 hrs)
│   ├── Raft (~1.5 hrs)
│   └── Paxos (~1.5 hrs)
└── Distributed Transactions (~1 hr)


09 · ADVANCED ARCHITECTURE PATTERNS (~8–12 hrs)
│
├── Event-Driven Architecture (~1.5 hrs)
├── CQRS (~1 hr)
├── Event Sourcing (~1.5 hrs)
├── Saga Pattern (~1.5 hrs)
├── Outbox Pattern (~1 hr)
├── Transactional Messaging (~1 hr)
└── When NOT to Use These Patterns (~1 hr)


10 · SECURITY IN HLD (~4–6 hrs)
│
├── Authentication (~30 min)
├── Authorization (~30 min)
├── Sessions (~30 min)
├── JWT (~40 min)
├── OAuth (~45 min)
├── API Security (~30 min)
├── Encryption (~45 min)
│   ├── Encryption at Rest (~20 min)
│   └── Encryption in Transit (~20 min)
├── Secrets Management (~25 min)
├── Rate Limiting (~25 min)
├── Abuse Prevention (~30 min)
└── Data Protection (~30 min)


11 · OBSERVABILITY (~4–6 hrs)
│
├── Logs (~30 min)
├── Metrics (~30 min)
├── Traces (~45 min)
├── Monitoring (~30 min)
├── Alerting (~30 min)
├── Latency (~30 min)
├── Error Rate (~20 min)
├── Throughput (~20 min)
└── Debugging Distributed Systems (~1 hr)


12 · SYSTEM DESIGN CASE STUDIES (~25–40+ hrs)
│
├── Beginner (~5–7 hrs)
│   ├── URL Shortener (~2 hrs)
│   ├── Pastebin (~1.5–2 hrs)
│   └── Rate Limiter (~1.5–2 hrs)
│
├── Intermediate (~7–10 hrs)
│   ├── File Storage / Upload (~2 hrs)
│   ├── Notification System (~2 hrs)
│   ├── Chat System (~2–3 hrs)
│   └── WhatsApp (~2–3 hrs)
│
├── Advanced (~9–13 hrs)
│   ├── Instagram (~2–3 hrs)
│   ├── News Feed (~2 hrs)
│   ├── YouTube (~2–3 hrs)
│   ├── Uber (~2–3 hrs)
│   └── Food Delivery (~2 hrs)
│
└── High-Scale / Interview (~6–10+ hrs)
    ├── Payment System (~2–3 hrs)
    ├── Ticket Booking (~2 hrs)
    └── Ride Matching (~2–3 hrs)

```
