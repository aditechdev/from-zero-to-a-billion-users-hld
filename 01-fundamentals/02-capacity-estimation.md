# 📊 Capacity Estimation

[![Capacity Estimation](https://img.shields.io/badge/-Capacity%20Estimation-6366f1)](#top)
[![Traffic Estimation](https://img.shields.io/badge/-Traffic%20Estimation-6366f1)](#traffic-estimation)
[![Storage Estimation](https://img.shields.io/badge/-Storage%20Estimation-6366f1)](#storage-estimation)
[![Bandwidth Estimation](https://img.shields.io/badge/-Bandwidth%20Estimation-6366f1)](#bandwidth-estimation)
[![Memory Estimation](https://img.shields.io/badge/-Memory%20Estimation-6366f1)](#memory-estimation)

---

## ❓ Problem
Before designing a system, we need to understand how much capacity the system needs.

Without capacity estimation, we don't know:
- How much traffic the system receives
- How much storage is required
- How much network bandwidth is required
- How much data may need to stay in memory
- Where potential bottlenecks may appear
- How many infrastructure resources may be needed

Capacity estimation gives us approximate numbers that help us reason about scalability,
bottlenecks, infrastructure, and architecture.

The goal is not to produce perfectly precise numbers. The goal is to produce reasonable
estimates quickly, state assumptions clearly, and use the estimates to drive architecture
decisions.

---

## 📋 Prerequisites
- Basic understanding of client-server architecture
- Basic understanding of databases and caching
- Basic understanding of bytes, bits, and storage units

---

## 🧠 Theory

### 1️⃣ Traffic Estimation
Traffic estimation determines how many requests a system needs to handle.

A useful flow is:
```text
Users / Events
      ↓
Requests per Day
      ↓
Average RPS
      ↓
Peak RPS
      ↓
Read / Write Workload
      ↓
Identify Bottlenecks
```

#### Requests / Day
The first step is estimating how many requests the system receives in a day.

```text
100 Million requests/day
```

This does not mean the system receives the same number of requests every second.
Traffic varies throughout the day:

```text
Morning       ████
Afternoon     ███████
Evening       ██████████
Night         ███
```

So we calculate average traffic first and then estimate peak traffic.

#### Average RPS
RPS = Requests Per Second.

```text
1 day = 24 × 60 × 60
      = 86,400 seconds
```

Therefore:
```text
Average RPS = Requests per day / 86,400
```

**Example**
```text
100,000,000 requests/day
100,000,000 / 86,400
≈ 1,157 RPS
```
So: **Average RPS ≈ 1.2K**

**⚡ Interview Shortcut** — for quick mental estimation:
```text
1 day ≈ 100,000 seconds
100,000,000 / 100,000
≈ 1,000 RPS
```
This shortcut is useful during system design interviews when exact precision is not required.

#### Peak RPS
Real-world traffic is not uniform. For estimation exercises:
```text
Peak RPS = Average RPS × Peak Factor
```
For example, with a 3× peak factor:
```text
Average RPS = 1,157
Peak RPS = 1,157 × 3
         ≈ 3,471 RPS
```
So: **Average ≈ 1.2K RPS · Peak ≈ 3.5K RPS**

⚠️ **Important:** 3× is an assumption, not a universal rule. In an interview, clearly state:
> "I'll assume peak traffic is approximately 3× average traffic."

The actual peak factor depends on the system and its traffic pattern.

#### Read vs Write Traffic
Not every request performs the same operation.

Some requests **write** data: Create, Update, Upload, Send message
Other requests **read** data: Fetch, View, Search, Get profile

We should estimate the ratio between reads and writes. For example:
```text
Read : Write = 100 : 1
```
This means the system is **read-heavy**.

```text
Read : Write = 1 : 1
```
This means the workload is approximately balanced.

**Why does this matter?**
```text
Read-heavy  → Large number of data reads   → Read scalability becomes important
Write-heavy → Large number of data writes  → Database write capacity becomes important
```

We should understand the workload before choosing solutions such as caching, read replicas,
sharding, database scaling, or other architecture components.

#### Traffic Estimation Practice — URL Shortener
Assume: **100 Million URLs created per day.** Each created URL is a write.

**Write RPS**
```text
100,000,000 / 86,400
≈ 1,157 RPS
```
Assuming a 3× peak:
```text
1,157 × 3
≈ 3,471 RPS
```
Write Traffic: **Average ≈ 1.2K RPS · Peak ≈ 3.5K RPS**

Now assume every URL receives 100 redirects/day:
```text
100 Million URLs × 100 redirects
= 10 Billion redirects/day
```
Average read RPS:
```text
10,000,000,000 / 86,400
≈ 115,741 RPS
```
So: **Average ≈ 116K RPS**

With a 3× peak assumption:
```text
115,741 × 3
≈ 347,223 RPS
```
**Peak ≈ 347K RPS**

This tells us that the URL shortener is heavily **read-dominated**.

---

### 2️⃣ Storage Estimation
Storage estimation determines how much persistent data the system needs.

A useful flow is:
```text
Records
   ↓
Record Size
   ↓
Raw Storage
   ↓
Growth Over Time
   ↓
Indexes
   ↓
Replication
   ↓
Backups / Retention
   ↓
Total Storage Requirement
```

#### Basic Storage Calculation
```text
Storage = Number of Records × Size per Record
```
**Example**
```text
10 Million users
500 Bytes/user

10,000,000 × 500
= 5,000,000,000 Bytes
≈ 5 GB
```
This is the raw data size before indexes, replication, backups, and other overhead.

#### Estimating Record Size
Record size should be estimated from the fields stored in the record.

```text
userId        = 8 Bytes
name          = 50 Bytes
email         = 50 Bytes
createdAt     = 8 Bytes
metadata      = 84 Bytes
--------------------------------
Total         = 200 Bytes
```
Record size ≈ 200 Bytes for this example. For structured metadata, this type of approximation
is useful. Large binary data such as images and videos should generally be considered separately
from structured database records because object size can dominate storage requirements.

#### Storage Growth Over Time
If data is continuously created, estimate storage over the required time horizon.

```text
10 Million records/day
500 Bytes/record

Daily:   10,000,000 × 500 = 5,000,000,000 Bytes ≈ 5 GB/day
Annual:  5 GB × 365 ≈ 1.825 TB/year
```

If growth changes each year, calculate each year separately:
```text
Year 1: 10M records/day × 500 Bytes = 5 GB/day  ≈ 1.825 TB/year
Year 2: 15M records/day × 500 Bytes = 7.5 GB/day ≈ 2.7375 TB/year
Year 3: 20M records/day × 500 Bytes = 10 GB/day  ≈ 3.65 TB/year

Total: 1.825 + 2.7375 + 3.65 ≈ 8.21 TB
```

#### Index Overhead
Indexes consume additional storage.

```text
Raw data = 50 TB
Estimated index overhead = 20%

Index storage = 50 TB × 20% = 10 TB
Total database storage before replication = 50 TB + 10 TB = 60 TB
```

Indexes also increase write work because inserts, updates, and deletes may need to maintain
the relevant indexes. Indexes improve read performance but introduce storage and write overhead.

#### Replication Overhead
Replication keeps multiple copies of data for availability and sometimes read scaling.

```text
Database storage = 60 TB
Replication factor = 3

Total replicated storage = 60 TB × 3 = 180 TB
```

A replication factor of 3 means 3 total copies, not 3 additional copies. Replication is
primarily about keeping current copies available — it is not a substitute for backups.

#### Backup / Retention
Replication and backups solve different problems.

```text
Replication → Current copies → Availability / Failover / Read scaling
Backup      → Historical copies → Recovery from accidental deletion, corruption,
                                    bad deployments, or other logical mistakes
```

A replicated delete can be propagated to replicas. A historical backup can allow recovery to
an earlier state.

**Full Backup** — contains the complete database state represented by the backup.
```text
Database = 100 GB
Daily full backup = 100 GB
7 daily backups

100 GB × 7 = 700 GB
```

**Incremental Backup** — stores changes according to the backup strategy.
```text
Full backup   = 100 GB
Incremental 1 = 5 GB
Incremental 2 = 3 GB
Incremental 3 = 7 GB

100 + 5 + 3 + 7 = 115 GB
```
Incremental backups can reduce storage and transfer compared with repeatedly taking full backups.

**Backup Frequency** — how often backups are created. More frequent backups can reduce data
potentially lost between backups, but can increase storage usage, network transfer, CPU usage,
disk I/O, and backup processing.

**Retention** — how long historical backups are kept. Examples: keep latest backup only, keep
7 daily backups, keep 30 daily backups, keep monthly backups for 1 year. Keeping only the latest
backup is still a retention policy. Longer retention increases historical storage requirements.

**Compression** — backups are often compressed to reduce storage and transfer requirements.
```text
Uncompressed backup = 100 GB
Compressed backup   = 40 GB
```
The actual compression ratio depends on the data. Compression trades storage/network savings
against CPU and processing cost.

**Separate Backup Storage** — backups should generally be stored separately from the primary
database infrastructure. Common choices: Amazon S3, Google Cloud Storage, Azure Blob Storage.
Depending on recovery requirements, backups may also be stored in another availability zone
or region.

**Replication vs Backup Calculation** — do not automatically multiply backup storage by the
database replication factor.
```text
Database copy = 20 TB
Replication factor = 3
Database storage = 20 × 3 = 60 TB

If 7 full backups of the logical 20 TB database are retained:
Backup storage = 20 × 7 = 140 TB

Total: 60 TB database + 140 TB backups = 200 TB
```
This calculation assumes the backups are taken from the logical database dataset rather than
independently backing up every replica.

---

### 3️⃣ Bandwidth Estimation
Bandwidth estimation determines how much data must travel over the network.

A useful flow is:
```text
Request Size → Request Bandwidth
Response Size → Response Bandwidth
Request + Response → External Bandwidth
                   ↓
        Add Infrastructure Traffic / Headroom
```

#### Request Bandwidth
```text
Request Bandwidth = RPS × Request Size

10K RPS × 2 KB/request
= 20,000 KB/s ≈ 20 MB/s
```

#### Response Bandwidth
```text
Response Bandwidth = RPS × Response Size

10K RPS × 20 KB/response
= 200,000 KB/s ≈ 200 MB/s
```

#### Total Bandwidth
```text
External bandwidth ≈ Request Bandwidth + Response Bandwidth
20 MB/s + 200 MB/s = 220 MB/s

Using decimal units:
220 MB/s × 8 = 1,760 Mbps ≈ 1.76 Gbps
```

#### Bits vs Bytes
```text
1 Byte = 8 bits
Bytes → bits : multiply by 8
bits → Bytes : divide by 8

1 MB = 8 Mb
```
A common mistake is confusing MB (Megabytes) with Mb (Megabits).

#### Decimal vs Binary Units
For HLD mental estimation, decimal units are commonly used:
```text
1 KB ≈ 1,000 Bytes
1 MB ≈ 1,000 KB
1 GB ≈ 1,000 MB
1 TB ≈ 1,000 GB
```
Binary units use 1024 and are formally represented as KiB, MiB, GiB, TiB.

#### External vs Internal Bandwidth
Application servers may handle more network traffic than the traffic visible from clients —
Application ↔ Database, Redis, other services, observability systems.

```text
20K RPS, 2 KB request, 8 KB response, 200 MB/s database traffic

Client-facing:
Request:  20,000 × 2 KB ≈ 40 MB/s
Response: 20,000 × 8 KB ≈ 160 MB/s
External traffic: 40 + 160 = 200 MB/s

Approximate application-server traffic including database:
40 MB/s in + 160 MB/s out + 200 MB/s DB = 400 MB/s
```
The exact infrastructure traffic depends on the architecture.

#### Protocol Overhead
Actual network traffic is greater than application payload because protocols add metadata
(IP headers, TCP headers, HTTP headers, TLS overhead, other transport/network metadata). In
HLD, this is usually handled with an approximation rather than calculating every header byte.

#### Retries
Retries can increase network traffic — original request + retry request + retry response.
At scale, uncontrolled retries can create a retry storm. Capacity estimates should consider
retry traffic when appropriate.

#### Background Traffic
Systems also generate traffic outside the primary request path: health checks, service-to-service
calls, background jobs, configuration updates, monitoring, logs, traces, database replication.

#### Headroom
Do not provision infrastructure exactly at the estimated average requirement. Headroom accounts
for traffic spikes, protocol overhead, retries, background traffic, observability traffic,
replication traffic, and future growth. There is no universal headroom percentage — state the
assumption.

#### Server Bandwidth Capacity
```text
Required Servers = Required Bandwidth / Safe Bandwidth per Server (round up)

Required bandwidth = 450 MB/s
Safe capacity/server = 100 MB/s

450 / 100 = 4.5 → 5 servers
```
This is only a bandwidth-based estimate. CPU, memory, disk I/O, database capacity, and
connection limits may require more servers.

#### CDN
A CDN places cacheable content (images, videos, static assets) closer to users.
```text
User traffic = 40 Gbps
CDN serves = 35 Gbps
Origin serves = 5 Gbps
```
The CDN reduces traffic reaching the origin for cacheable content. A CDN does not replace the
backend application, and serves a different role from an application cache such as Redis:
```text
CDN   → Edge/content delivery cache
Redis → Application-side data/cache store
```

---

### 4️⃣ Memory Estimation
Memory estimation helps determine how much RAM is needed for frequently accessed data and
application/infrastructure workloads.

A useful flow is:
```text
Total Data → Working Set → Cacheable Data → Required Cache Memory
          → Server RAM → Usable RAM → Number of Servers
```

#### Working Set
The working set is the portion of data that is actively or frequently accessed — usually much
smaller than the total dataset.
```text
Total database = 2 TB
Working set = 5%

2 TB × 5% = 0.1 TB = 100 GB
```
Working set does not automatically mean the entire working set must be cached.

#### Cache Memory
```text
Working set = 400 GB
Cache required = 25% of working set

400 GB × 25% = 100 GB
```
Cache memory also needs room for keys, metadata, internal data structures, replication buffers,
temporary memory, OS overhead, and safety headroom. So: **cache data size ≠ total RAM required.**

#### Memory Estimation (servers)
```text
Required cache = 100 GB
Server RAM = 64 GB
Reserved memory = 20%

Usable memory/server = 64 GB × (1 - 0.20) = 51.2 GB
Number of servers = 100 / 51.2 ≈ 1.95 → 2 servers
```
This is a simplified cache-memory estimate. Real systems also need to account for application
memory, OS memory, metadata, connections, replication, failover capacity, and operational
headroom.

---

## 🗺️ Diagram

**Overall Capacity Estimation**
```text
                         CAPACITY ESTIMATION
                                  │
             ┌────────────────────┼────────────────────┐
             ↓                    ↓                    ↓
          TRAFFIC              STORAGE              BANDWIDTH
             │                    │                    │
       Requests/Day          Records × Size       Request Size
             │                    │                    │
        Average RPS         Growth Over Time      Response Size
             │                    │                    │
          Peak RPS             Indexes                 │
             │                    │                    │
       Read / Write          Replication               │
             │                    │                    │
             │                 Backups                 │
             │                    │                    │
             └────────────────────┼────────────────────┘
                                  ↓
                           MEMORY ESTIMATION
                                  │
                           Working Set
                                  ↓
                             Cache Size
                                  ↓
                             Server RAM
                                  ↓
                           Number of Servers
                                  │
                                  ↓
                         Architecture Decisions
```

**Capacity Estimation → Architecture**
```text
Requirements
     ↓
Workload
     ↓
Capacity Estimation
     ├── Traffic
     ├── Storage
     ├── Bandwidth
     └── Memory
     ↓
Find Bottlenecks
     ↓
Possible Solutions
     ↓
Trade-offs
     ↓
Architecture
```

---

## 🏗️ Real Example

### URL Shortener
A URL shortener illustrates how multiple capacity dimensions interact.

```text
100M URLs created/day
100 redirects per URL/day

Writes: ≈ 1.2K average RPS · ≈ 3.5K peak RPS (3× assumption)
Reads:  ≈ 116K average RPS · ≈ 347K peak RPS (3× assumption)
```

This immediately tells us the workload is heavily read-dominated. That can influence later
architectural decisions such as caching popular URLs, read scaling, database
partitioning/sharding if required, CDN usage where content is suitable, load balancing, and
capacity planning for redirect traffic.

The important point is that we do not choose these technologies merely because they are common
— the traffic estimate tells us what problem we need to solve.

### Where Do These Numbers Come From in Production?
In a real company, capacity-estimation inputs are often derived from monitoring, analytics,
databases, load balancers, and infrastructure platforms.

| Metric | Possible Production Source |
|---|---|
| Requests / RPS | GCP Cloud Monitoring, AWS CloudWatch, load balancer metrics, API gateway metrics, APM |
| Peak traffic | Monitoring time series, load balancer metrics, APM |
| Database size | MongoDB, PostgreSQL |
| Index size | MongoDB, PostgreSQL |
| Database connections | MongoDB, PostgreSQL, cloud monitoring |
| Query latency | MongoDB monitoring, PostgreSQL statistics, APM |
| Database I/O | MongoDB monitoring, PostgreSQL/cloud infrastructure metrics |
| Cache memory | Redis / managed Redis metrics |
| Cache hit rate | Redis/application metrics |
| Network bandwidth | GCP Cloud Monitoring, AWS CloudWatch, load balancer/network metrics |
| Object storage usage | Google Cloud Storage, Amazon S3, Azure Blob Storage |
| User activity | Product analytics / event tracking |
| Logs | Centralized logging systems |
| Traces | Distributed tracing / APM |
| Replication health | Database monitoring |
| Backup size/status | Database backup service / object storage / cloud backup tooling |

The exact metric names and dashboards depend on the company's infrastructure.

### Production Numbers vs Interview Numbers
```text
HLD Interview
     ↓
Given numbers → Reasonable assumptions → Capacity calculations

Production System
     ↓
Monitoring + Analytics + Database Metrics → Observed workload
     ↓
Capacity planning → Scaling decisions
```
A senior engineer should be comfortable with both.

---

## 🎯 Interview Questions

**Q: What is capacity estimation?**
A: The process of estimating the traffic, storage, bandwidth, memory, and other infrastructure
capacity required by a system.

**Q: How do you calculate average RPS?**
A: `Average RPS = Requests per day / 86,400`

**Q: Why do we calculate peak RPS?**
A: Because real traffic is not uniform. Systems must handle periods where traffic is
significantly higher than average.

**Q: Is 3× always the correct peak factor?**
A: No. It is only an assumption. The actual peak factor depends on the workload and traffic
pattern.

**Q: Why is read/write ratio important?**
A: It describes workload characteristics and helps identify likely bottlenecks and scaling
strategies.

**Q: How do you estimate database storage?**
A: Start with Records × Record Size, then account for growth, indexes, replication, backups,
and other overhead.

**Q: Does replication replace backups?**
A: No. Replication provides current copies for availability/failover and sometimes read
scaling. Backups provide historical recovery points.

**Q: If the database has a replication factor of 3, should backups automatically be multiplied
by 3?**
A: No. Only do this if the backup architecture explicitly requires independent backups of all
replicas.

**Q: What is backup retention?**
A: Retention defines how long historical backups are preserved.

**Q: What is the difference between backup frequency and retention?**
A: Frequency is how often backups are created; retention is how long backups are kept.

**Q: What is the difference between a full and incremental backup?**
A: A full backup captures the complete database state represented by the backup. An incremental
backup captures changes according to the backup strategy rather than repeatedly storing the
full dataset.

**Q: How do you calculate request bandwidth?**
A: `Request Bandwidth = RPS × Request Size`

**Q: How do you calculate response bandwidth?**
A: `Response Bandwidth = RPS × Response Size`

**Q: How do you convert MB/s to Gbps?**
A: Approximately `MB/s × 8 = Mbps`, `Mbps / 1,000 ≈ Gbps` — so 125 MB/s ≈ 1 Gbps under decimal
units.

**Q: Is external bandwidth the same as application-server network traffic?**
A: Not necessarily — application servers can also communicate with databases, caches, other
services, and observability systems.

**Q: What is a working set?**
A: The portion of data that is frequently or actively accessed.

**Q: Is working-set size the same as cache size?**
A: No. A cache may contain only a portion of the working set.

**Q: Where would you get capacity-estimation numbers in a real company?**
A: Cloud monitoring, load balancers, API gateways, APM, database monitoring, Redis metrics,
product analytics, logs and traces, object-storage metrics.

---

## ⚠️ Mistakes / Gotchas
- Confusing users with requests
- Using requests/day directly as RPS
- Forgetting to divide by 86,400
- Treating average traffic as peak traffic
- Assuming 3× peak is a universal rule
- Ignoring read/write workload
- Choosing technologies before understanding the workload
- Treating raw database size as total infrastructure storage
- Forgetting index overhead
- Treating replication factor as extra copies instead of total copies
- Treating replication as a backup
- Assuming backup storage must automatically be multiplied by database replication factor
- Confusing backup frequency with retention
- Assuming a universal compression ratio
- Storing backups only on the same infrastructure being protected
- Confusing bits and bytes
- Remembering the conversion incorrectly: 1 Byte = 8 bits
- Confusing MB with Mb
- Confusing KB/MB decimal units with KiB/MiB binary units
- Multiplying instead of dividing when converting smaller units to larger units
- Ignoring internal application-to-database/cache traffic
- Ignoring retries and retry storms
- Ignoring protocol overhead
- Ignoring background and observability traffic
- Provisioning exactly at the calculated capacity without headroom
- Assuming bandwidth is always the bottleneck
- Assuming the working set must equal cache size
- Assuming cache data size equals total cache-server RAM
- Confusing CDN with Redis
- Treating interview assumptions as production measurements
- Giving highly precise numbers without stating assumptions

---

## 🔑 Key Takeaways
- Capacity estimation turns system requirements into approximate infrastructure numbers.
- Traffic estimation determines requests/day, average RPS, peak RPS, and read/write workload.
- Storage estimation starts with records × record size and then accounts for growth and overhead.
- Indexes improve query performance but consume storage and add write work.
- Replication and backup solve different problems.
- Backup frequency controls how often recovery points are created; retention controls how long
  they are kept.
- Bandwidth includes request and response traffic, but real infrastructure can have significant
  internal traffic too.
- 1 Byte = 8 bits.
- Working set is the frequently accessed portion of the dataset; it is not automatically equal
  to cache size.
- Cache-server RAM must account for overhead and headroom.
- Production capacity numbers come from monitoring, analytics, databases, caches, and
  infrastructure metrics.
- HLD interviews rely on given numbers and explicit assumptions.
- Capacity estimation should happen before major architecture decisions.
- The objective is not perfect precision; it is reasonable estimation + clear assumptions +
  architecture reasoning.

---

## 📚 References
- [Google Cloud Monitoring documentation](https://cloud.google.com/monitoring/docs)
- [AWS CloudWatch documentation](https://docs.aws.amazon.com/AmazonCloudWatch/latest/monitoring/WhatIsCloudWatch.html)
- [MongoDB monitoring documentation](https://www.mongodb.com/docs/manual/administration/monitoring/)
- [PostgreSQL monitoring documentation](https://www.postgresql.org/docs/current/monitoring.html)
- [Redis monitoring documentation](https://redis.io/docs/latest/operate/oss_and_stack/management/optimization/)

---

## 🚧 Pending Topic
- Architecture Fundamentals (Client-Server, Request/Response Flow, 3-Layer Architecture)

