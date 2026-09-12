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

## 🧭 How to Use This Repo

Each topic follows a standard note format:

**Problem → Prerequisites → Theory → Diagram → Code (when relevant) → Real Example → Interview Questions → Mistakes / Gotchas → Key Takeaways → References (when useful).**

Start with `01-fundamentals/` and work through the topics in order.

The keywords listed below each topic make it easy to search for specific concepts using `Ctrl+F` or GitHub search.
