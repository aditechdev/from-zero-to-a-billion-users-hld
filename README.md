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

---
## 🗺️ Roadmap

```text
01 · HLD FUNDAMENTALS (~8–11 hrs)                  ✅
│
├── HLD Fundamentals (~2–3 hrs)                    ✅
│   ├── What is HLD? (~20 min)                     ✅
│   ├── Why HLD? (~15 min)                         ✅
│   ├── Functional Requirements (~20 min)          ✅
│   ├── Non-Functional Requirements (~25 min)      ✅
│   ├── Functional vs Non-Functional (~15 min)     ✅
│   └── Basic HLD Thinking (~30–40 min)            ✅
│
└── Capacity Estimation (~6–8 hrs)                 ✅
    │
    ├── Traffic Estimation (~2 hrs)                ✅
    │   ├── Requests / Day (~20 min)               ✅
    │   ├── Average RPS (~20 min)                  ✅
    │   ├── Peak RPS (~20 min)                     ✅
    │   ├── Read vs Write Traffic (~20 min)        ✅
    │   └── Traffic Estimation Practice (~40 min)  ✅
    │
    ├── Storage Estimation (~2 hrs)                ✅
    │   ├── Basic Storage Calculation (~20 min)    ✅
    │   ├── Estimating Record Size (~30 min)       ✅
    │   ├── Storage Growth Over Time (~25 min)     ✅
    │   ├── Index Overhead (~20 min)               ✅
    │   ├── Replication Overhead (~15 min)         ✅
    │   └── Backup / Retention (~15 min)           ✅
    │
    ├── Bandwidth Estimation (~1–1.5 hrs)          ✅
    │   ├── Request Bandwidth (~20 min)            ✅
    │   ├── Response Bandwidth (~25 min)           ✅
    │   └── Total Bandwidth (~30 min)              ✅
    │
    └── Memory Estimation (~1–1.5 hrs)             ✅
        ├── Working Set (~25 min)                  ✅
        ├── Cache Memory (~25 min)                 ✅
        └── Memory Estimation (~30 min)            ✅
```
```
02 · ARCHITECTURE FUNDAMENTALS (~10–12 hrs)        🟡 IN PROGRESS
│
├── Client-Server Architecture (~45 min)            ✅
│   ├── What Problem Does Client-Server Solve?      ✅
│   ├── First Principles                            ✅
│   ├── What is a Client?                           ✅
│   ├── What is a Server?                           ✅
│   ├── Client-Server Relationship                  ✅
│   ├── Client Responsibilities                     ✅
│   ├── Server Responsibilities                     ✅
│   ├── Shared State                                ✅
│   ├── Source of Truth                             ✅
│   ├── Trust Boundary                              ✅
│   ├── Why We Cannot Trust the Client              ✅
│   ├── Client-Side vs Server-Side Validation       ✅
│   ├── Client and Server Don't Need Same UI        ✅
│   ├── Logical Server vs Physical Server           ✅
│   ├── Multiple Clients → One Backend              ✅
│   ├── Real-World Examples                         ✅
│   │   ├── Banking Application                     ✅
│   │   ├── Coupon Validation                       ✅
│   │   └── Last Available Product                  ✅
│   └── Common Mistakes & Mental Model              ✅
│
├── Request / Response Flow (~45 min)               🟡
│   ├── What Problem Does Request / Response Solve? ⏳
│   ├── Request                                     ✅
│   ├── Response                                    ✅
│   ├── Request → Processing → Response             ✅
│   ├── Client → Backend → Client                   ✅
│   ├── Backend → Database                          ✅
│   ├── Client / Server Roles at Each Boundary      ✅
│   ├── Request / Response Interaction Boundaries   ✅
│   ├── One External Request → Multiple Operations  ✅
│   ├── Request ≠ Operation                         ✅
│   ├── Operation ≠ Response                        ⏳
│   ├── Anatomy of a Request                        ⬅️ NEXT
│   │   ├── Destination                             ⏳
│   │   ├── Intent / Operation                      ⏳
│   │   ├── Parameters                              ⏳
│   │   ├── Metadata                                ⏳
│   │   └── Body                                    ⏳
│   ├── Anatomy of a Response                       ⏳
│   │   ├── Result / Data                           ⏳
│   │   ├── Success / Failure                       ⏳
│   │   ├── Status Information                      ⏳
│   │   ├── Metadata                                ⏳
│   │   └── Body                                    ⏳
│   ├── HTTP Request / Response                     ⏳
│   │   ├── HTTP Request                            ⏳
│   │   ├── HTTP Response                           ⏳
│   │   ├── URL / Endpoint                          ⏳
│   │   ├── HTTP Methods                            ⏳
│   │   ├── Headers                                 ⏳
│   │   ├── Body                                    ⏳
│   │   └── Status Codes                            ⏳
│   ├── Complete Request Lifecycle                  ⏳
│   ├── Failure Points                              ⏳
│   ├── Latency                                     ⏳
│   └── Real-World Request Flows                    ⏳
│
├── 3-Tier Architecture (~45 min)                   ⏳
│   ├── What Problem Does 3-Tier Solve?             ⏳
│   ├── First Principles                            ⏳
│   ├── Presentation Tier                           ⏳
│   ├── Application / Business Tier                 ⏳
│   ├── Data Tier                                   ⏳
│   ├── Responsibilities of Each Tier               ⏳
│   ├── Request Flow Through 3 Tiers                ⏳
│   ├── Separation of Concerns                      ⏳
│   ├── Presentation vs Business Logic              ⏳
│   ├── Business Logic vs Data Access               ⏳
│   ├── Why Separate the Tiers?                     ⏳
│   ├── 3-Tier vs Client-Server                     ⏳
│   ├── 3-Tier vs 3-Layer                           ⏳
│   ├── Physical Deployment vs Logical Tiers        ⏳
│   ├── Scaling Individual Tiers                    ⏳
│   ├── Real-World Example                          ⏳
│   └── Common Mistakes & Mental Model              ⏳
│   
├──HLD Networking Fundamentals (~1 hr)
│   ├── IP Address & Port
│   ├── TCP vs UDP
│   ├── TCP Connection — Conceptual
│   ├── HTTP over TCP
│   ├── HTTPS / TLS — Conceptual
│   ├── Connection Reuse / Keep-Alive
│   └── Network Latency
│
├── DNS (~40 min)                                   ⏳
│   ├── What Problem Does DNS Solve?                ⏳
│   ├── Domain Name vs IP Address                   ⏳
│   ├── DNS Resolution                              ⏳
│   ├── Recursive Resolver                          ⏳
│   ├── Root DNS Server                             ⏳
│   ├── TLD DNS Server                              ⏳
│   ├── Authoritative DNS Server                    ⏳
│   ├── DNS Caching                                 ⏳
│   ├── TTL                                         ⏳
│   ├── DNS Record Types                            ⏳
│   │   ├── A                                       ⏳
│   │   ├── AAAA                                    ⏳
│   │   ├── CNAME                                   ⏳
│   │   └── NS                                      ⏳
│   ├── Recursive vs Iterative Resolution           ⏳
│   ├── DNS in a Real Request Flow                  ⏳
│   └── Common Mistakes & Mental Model              ⏳
│
├── Load Balancer (~1.5 hrs)                        ⏳
│   ├── What Problem Does Load Balancing Solve?     ⏳
│   ├── Why One Server Is Not Enough                ⏳
│   ├── Multiple Backend Instances                  ⏳
│   ├── Load Balancer Role                          ⏳
│   ├── Traffic Distribution                        ⏳
│   ├── L4 vs L7                                    ⏳
│   │   ├── Layer 4 Load Balancing                  ⏳
│   │   ├── Layer 7 Load Balancing                  ⏳
│   │   └── L4 vs L7 Trade-offs                     ⏳
│   ├── Load Balancing Algorithms (~30 min)         ⏳
│   │   ├── Round Robin                             ⏳
│   │   ├── Weighted Round Robin                    ⏳
│   │   ├── Least Connections                       ⏳
│   │   ├── IP Hash                                 ⏳
│   │   └── Consistent Hashing — Conceptual         ⏳
│   ├── Health Checks (~20 min)                     ⏳
│   │   ├── Why Health Checks Are Needed            ⏳
│   │   ├── Liveness vs Readiness — Conceptual      ⏳
│   │   └── Removing Unhealthy Instances            ⏳
│   ├── Load Balancer Placement                     ⏳
│   ├── Load Balancer Failure                       ⏳
│   ├── Horizontal Scaling + Load Balancer          ⏳
│   └── Common Mistakes & Mental Model              ⏳
│
├── Reverse Proxy (~30 min)                         ⏳
│   ├── What Problem Does a Reverse Proxy Solve?    ⏳
│   ├── Forward Proxy vs Reverse Proxy              ⏳
│   ├── Client → Reverse Proxy → Backend            ⏳
│   ├── Backend Protection                          ⏳
│   ├── TLS Termination — Conceptual                ⏳
│   ├── Routing                                     ⏳
│   ├── Caching — Conceptual                        ⏳
│   ├── Compression — Conceptual                    ⏳
│   ├── Reverse Proxy vs Load Balancer              ⏳
│   └── Common Deployment Example                   ⏳
│
├── API Gateway (~40 min)                           ⏳
│   ├── What Problem Does API Gateway Solve?        ⏳
│   ├── Client → Gateway → Services                 ⏳
│   ├── Single Entry Point                          ⏳
│   ├── Routing                                     ⏳
│   ├── Authentication / Authorization              ⏳
│   ├── Rate Limiting — Conceptual                  ⏳
│   ├── Request / Response Transformation           ⏳
│   ├── Aggregation — Conceptual                    ⏳
│   ├── API Gateway vs Reverse Proxy                ⏳
│   ├── API Gateway vs Load Balancer                ⏳
│   └── Common Mistakes & Trade-offs                ⏳
│
├── CDN
│    ├── What Problem Does CDN Solve?
│    ├── Edge Servers
│    ├── Origin Server
│    ├── Cache Hit / Cache Miss
│    ├── TTL
│    ├── Cache Invalidation
│    ├── Static vs Dynamic Content
│    ├── CDN + Object Storage
│    ├── CDN + Backend
│    └── CDN vs Application Cache                                       ⏳
│
├── Horizontal Scaling (~30 min)                    ⏳
│   ├── What Problem Does Scaling Solve?            ⏳
│   ├── Vertical vs Horizontal Scaling              ⏳
│   ├── Scale Out                                   ⏳
│   ├── Multiple Server Instances                   ⏳
│   ├── Load Balancer + Horizontal Scaling          ⏳
│   ├── Stateless Requirement — Introduction        ⏳
│   ├── Shared State Problem                        ⏳
│   ├── Scaling Limits                              ⏳
│   └── Real-World Example                          ⏳
│
├── Vertical Scaling (~20 min)                      ⏳
│   ├── What is Scale Up?                           ⏳
│   ├── CPU / Memory Scaling                        ⏳
│   ├── Advantages                                  ⏳
│   ├── Limitations                                 ⏳
│   ├── Vertical vs Horizontal Scaling              ⏳
│   └── When to Use Which?                          ⏳
│
├── Stateless vs Stateful (~40 min)                 ⏳
│   ├── What Problem Does Statelessness Solve?      ⏳
│   ├── What is State?                              ⏳
│   ├── Stateful Server                             ⏳
│   ├── Stateless Server                            ⏳
│   ├── Why Stateless Helps Scaling                 ⏳
│   ├── Where Does State Go?                        ⏳
│   │   ├── Database                                ⏳
│   │   ├── Cache                                   ⏳
│   │   └── External State Store                    ⏳
│   ├── Sticky Sessions — Conceptual                ⏳
│   ├── Stateless vs Stateful Trade-offs            ⏳
│   └── Common Mistakes & Mental Model              ⏳
│
├── High Availability (~45 min)
│   ├── What is Availability?
│   ├── Single Point of Failure
│   ├── Redundancy
│   ├── Failover
│   ├── Active-Passive
│   ├── Active-Active
│   ├── Multi-AZ — Conceptual
│   ├── Multi-Region — Conceptual
│   └── Availability Trade-offs
│   
├── Monolith (~25 min)                              ⏳
│   ├── What Problem Does a Monolith Solve?         ⏳
│   ├── Monolithic Architecture                     ⏳
│   ├── Components Inside a Monolith                ⏳
│   ├── Deployment Unit                             ⏳
│   ├── Advantages                                  ⏳
│   ├── Limitations                                 ⏳
│   ├── Scaling a Monolith                          ⏳
│   ├── Modular Monolith — Conceptual               ⏳
│   └── When Is a Monolith a Good Choice?           ⏳
│
├── Microservices (~45 min)                         ⏳
│   ├── What Problem Do Microservices Solve?        ⏳
│   ├── From Monolith → Microservices               ⏳
│   ├── Service Boundaries                          ⏳
│   ├── Independent Deployment                      ⏳
│   ├── Independent Scaling                         ⏳
│   ├── Service-to-Service Communication            ⏳
│   ├── Database per Service — Conceptual           ⏳
│   ├── Advantages                                  ⏳
│   ├── Distributed System Complexity               ⏳
│   ├── Microservices Trade-offs                    ⏳
│   ├── Monolith vs Microservices                   ⏳
│   └── When Should We Use Microservices?           ⏳
│
└── Service Discovery (~30 min)                     ⏳
    ├── What Problem Does Service Discovery Solve?  ⏳
    ├── Why Service IPs Cannot Be Hardcoded         ⏳
    ├── Dynamic Service Instances                   ⏳
    ├── Service Registry                            ⏳
    ├── Service Registration                        ⏳
    ├── Service Lookup                              ⏳
    ├── Client-Side Discovery                       ⏳
    ├── Server-Side Discovery                       ⏳
    ├── Health / Availability Awareness             ⏳
    ├── Service Discovery + Load Balancing          ⏳
    └── Common Deployment Example                   ⏳
```
```
03 · API & SERVICE COMMUNICATION
│
├── API Fundamentals
│   ├── What is an API?
│   ├── API Contract
│   ├── Request / Response Schema
│   └── API Boundaries
│
├── REST
│   ├── Resources
│   ├── HTTP Methods
│   ├── HTTP Status Codes
│   └── REST Constraints — Conceptual
│
├── API Design
│   ├── Resource Modeling
│   ├── Pagination
│   │   ├── Offset Pagination
│   │   └── Cursor Pagination
│   ├── Filtering
│   ├── Sorting
│   ├── Idempotency — Introduction
│   ├── Error Handling
│   ├── API Versioning
│   └── Backward Compatibility
│
├── Serialization
│   ├── What is Serialization?
│   ├── JSON
│   ├── Protobuf (Protocol Buffers)
│   ├── Binary Formats
│   ├── Binary vs Text Formats
│   ├── JSON vs Protobuf
│   └── Serialization Overhead
│
├── Service Communication
│   ├── Synchronous Communication
│   ├── Asynchronous Communication
│   ├── REST                                        ⏳
│   ├── gRPC                                        ⏳
│   ├── Message Queue                               ⏳
│   ├── Event Streaming    
│   ├── Request / Response
│   ├── Pub/Sub
│   └── Event Communication
│
└── REST vs gRPC
```
```
04 · DATABASES (~10–14 hrs)                        ⏳
├── Database Fundamentals (~45 min)                ⏳
├── SQL vs NoSQL (~1 hr)                           ⏳
├── Data Modeling
│   ├── Schema
│   ├── Relationships
│   ├── Normalization
│   └── Denormalization                         ⏳
│
├── Transactions
│   ├── What is a Transaction?
│   ├── ACID
│   │  ├── Atomicity
│   │  ├── Consistency
│   │  ├── Isolation
│   │  └── Durability
│   ├── Commit / Rollback
│   ├── Concurrent Transactions
│   ├── Isolation Levels
│   │   ├── Read Uncommitted
│   │   ├── Read Committed
│   │   ├── Repeatable Read
│   │   └── Serializable
│   ├── Race Conditions — Database Context
│   ├── Optimistic vs Pessimistic Locking — Conceptual
│   └── Locking — Conceptual
│
├── Indexes (~2 hrs)                               ⏳
│   ├── Why Indexes? (~20 min)                     ⏳
│   ├── B-Tree Index (~30 min)                     ⏳
│   ├── Composite Index (~25 min)                  ⏳
│   ├── Index Selectivity (~20 min)                ⏳
│   └── Index Trade-offs (~20 min)                 ⏳
├── Query Patterns (~45 min)                       ⏳
│
├── Database Bottlenecks (~1 hr)                   ⏳
│   ├── CPU (~10 min)                              ⏳
│   ├── Memory (~10 min)                           ⏳
│   ├── Disk I/O (~15 min)                         ⏳
│   ├── Connections (~10 min)                      ⏳
│   └── Slow Queries (~15 min)                     ⏳
│
├── Replication (~1.5 hrs)                         ⏳
│   ├── Primary / Replica (~25 min)                ⏳
│   ├── Read Replicas (~25 min)                    ⏳
│   └── Replication Lag (~30 min)                  ⏳
│
├── Partitioning (~45 min)                         ⏳
│    ├── Partitioning
│    ├── Hash Partitioning
│    └── Range Partitioning
│
├── Sharding (~2 hrs)                              ⏳
│   ├── Shard Key (~25 min)                        ⏳
│   ├── Hash Sharding (~20 min)                    ⏳
│   ├── Range Sharding (~20 min)                   ⏳
│   ├── Hot Partitions (~25 min)                   ⏳
│   └── Rebalancing (~30 min)                      ⏳
│
└── Database Scaling (~1 hr)                       ⏳
    ├── Read Scaling (~20 min)                     ⏳
    ├── Write Scaling (~20 min)                    ⏳
    └── Connection Pooling (~20 min)               ⏳
```
```
05 · CACHING (~5–7 hrs)
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
├── Distributed Cache (~40 min)
└── Cache Consistency
```
```
06 · STORAGE
│
├── Storage Fundamentals
│   ├── Object Storage
│   ├── File Storage
│   └── Block Storage
│
├── Object Storage
│   ├── Bucket
│   ├── Object
│   ├── Metadata
│   └── Durability — Conceptual
│
├── Large File Storage
│
├── File Upload Architecture
│   ├── Backend Upload
│   ├── Direct Upload
│   ├── Pre-Signed URLs
│   └── Multipart Upload
│
├── File Download Architecture
├── Object Storage + CDN
└── Database vs Object Storage
```
```
07 · MESSAGING & QUEUES (~8–10 hrs)
│
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
```
```
08 · KAFKA (~8–12 hrs)
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
```
```
09 · REAL-TIME SYSTEMS
│
├── What is Real-Time Communication?
├── What Problem Does Real-Time Communication Solve?
├── Polling
├── Long Polling
├── Server-Sent Events
├── WebSockets
│   ├── Connection Lifecycle
│   ├── Persistent Connections
│   ├── WebSocket Scaling
│   ├── WebSockets vs HTTP
│   └── WebSockets vs SSE
├── Scaling Real-Time Systems — Introduction
├── Presence
├── Real-Time Notifications
└── Real-Time Messaging
```
```
10 · RELIABILITY & HIGH AVAILABILITY(~8–12 hrs)
│
├── Availability
├── Single Point of Failure
├── Redundancy
├── Failover
├── Active-Passive
├── Active-Active
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
└── Failure Scenarios (~2 hrs)
    ├── Server Failure (~20 min)
    ├── Database Failure (~20 min)
    ├── Cache Failure (~15 min)
    ├── Queue Failure (~15 min)
    ├── Network Failure (~25 min)
    └── Region Failure (~25 min)
```
```
11 · DISTRIBUTED SYSTEMS (~15–20 hrs)
│
├── Why Distributed Systems? (~40 min)
├── Network Failures (~45 min)
├── Partial Failures (~45 min)
├── Latency (~45 min)
├── Network Partitions (~45 min)
├── Replication (~1 hr)
├── Partitioning (~1 hr)
├── Consistent Hashing
│   ├── Hash Ring
│   ├── Virtual Nodes
│   └── Rebalancing
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
```
```
12 · SEARCH SYSTEMS
│
├── Database Search vs Search Engine
├── Full-Text Search
├── Inverted Index
├── Search Index
├── Indexing Pipeline
├── Query Flow
├── Search Ranking — Conceptual
├── Elasticsearch / OpenSearch — Conceptual
└── Search Scaling
```
```
13 · DISASTER RECOVERY
│
├── What Problem Does DR Solve?
├── Backup vs Replication
├── Recovery
├── Failover
├── RPO
├── RTO
├── Multi-AZ
├── Multi-Region
├── Active-Passive
├── Active-Active
├──Recovery Strategies
└── Disaster Recovery Trade-offs
```
```
14 · SECURITY IN HLD (~4–6 hrs)
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
├── Data Protection (~30 min)
└── Principle of Least Privilege
```
```
15 · OBSERVABILITY (~4–6 hrs)
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
```
```
16 · ADVANCED ARCHITECTURE PATTERNS (~8–12 hrs)
│
├── Event-Driven Architecture (~1.5 hrs)
├── CQRS (~1 hr)
├── Event Sourcing (~1.5 hrs)
├── Saga Pattern (~1.5 hrs)
├── Outbox Pattern (~1 hr)
├── Transactional Messaging (~1 hr)
└── When NOT to Use These Patterns (~1 hr)
```
```
17 · ARCHITECTURE TRADE-OFFS
│
├── Performance vs Cost
├── Latency vs Throughput
├── Consistency vs Availability
├── Simplicity vs Scalability
├── SQL vs NoSQL
├── Cache vs Database
├── Synchronous vs Asynchronous
├── Monolith vs Microservices
├── Strong vs Eventual Consistency
├── Build vs Buy
└── Reliability vs Complexity
```
```
18 · SYSTEM DESIGN CASE STUDIES (~25–40+ hrs)
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
    ├── Ride Matching (~2–3 hrs)
    └──Distributed Job Scheduler

```
