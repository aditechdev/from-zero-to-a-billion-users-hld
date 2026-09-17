# **Latency**

🏷️ Tags

---

## **❓ Problem**

A system can be functionally correct and still feel slow.

For example:

```
Client
  ↓
Backend
  ↓
Database
  ↓
Backend
  ↓
Client
```

If the database takes 2 seconds to respond, the user may experience a slow application even though nothing has technically failed.

In HLD, we need to understand:

- How long a request takes
- Where time is being spent
- Which operations contribute to the response time
- Which operations can run in parallel
- Which operations can be removed or optimized
- How latency changes as dependencies increase

---

## **📋 Prerequisites**

- Complete Request Lifecycle
- Backend Request Processing Pipeline
- Client-Server Architecture
- Cache and Database basics
- Basic understanding of synchronous and asynchronous operations

---

## **🧠 Theory**

### **1. What Is Latency?**

**Latency** is the time taken for an operation to complete or for a response to travel between two points.

For an API request, we commonly care about:

**How much time passes between the client sending a request and receiving the response?**

Example:

```
Request sent
     ↓
   120 ms
     ↓
Response received
```

The observed request latency is approximately:

```
120 ms
```

---

### **2. Latency vs Throughput**

These are different concepts.

**Latency:**

How long does one operation take?

**Throughput:**

How many operations can the system handle per unit of time?

Example:

```
API
├── Latency    → 100 ms/request
└── Throughput  → 10,000 requests/second
```

A system can have:

- Low latency but low throughput
- High latency but high throughput
- Both high
- Both low

They measure different characteristics.

---

### **3. End-to-End Latency**

A request can accumulate latency across multiple components.

```
Client
   ↓ 20ms
Network
   ↓ 5ms
Load Balancer
   ↓ 10ms
Backend
   ↓ 50ms
Database
   ↓ 10ms
Backend
   ↓ 20ms
Network
   ↓
Client
```

A simplified calculation:

```
20 + 5 + 10 + 50 + 10 + 20
= 115 ms
```

So:

**End-to-end latency is influenced by the latency of the components and operations on the request’s critical path.**

---

### **4. Sequential Operations**

Suppose the backend performs:

```
Backend
   ↓
Service A → 100ms
   ↓
Service B → 200ms
   ↓
Database → 50ms
```

Because they execute sequentially:

```
100 + 200 + 50
= 350ms
```

Each operation adds to the critical path.

---

### **5. Parallel Operations**

Suppose Service A and Service B are independent:

```
             ┌── Service A → 100ms
Backend ─────┤
             └── Service B → 200ms
```

If they execute in parallel:

```
max(100, 200)
= 200ms
```

The backend has to wait for the slower operation.

This gives us an important rule:

```
Sequential:
A + B

Parallel:
max(A, B)
```

This is a simplified model and assumes the operations can actually execute independently.

---

### **6. Critical Path**

The **critical path** is the sequence of operations that determines how long the request must wait before a response can be produced.

Example:

```
Backend
   ↓
Authentication → 10ms
   ↓
Database → 200ms
   ↓
Response → 10ms
```

The request cannot complete until these required operations complete.

So the critical path is approximately:

```
10 + 200 + 10
= 220ms
```

Now consider an independent logging operation:

```
Backend
   ├── Database → 200ms
   │
   └── Logging → 100ms
```

If logging does not block the response, its 100ms does not necessarily add to the user-visible request latency.

---

### **7. Dependency Latency**

Every synchronous dependency can potentially add latency.

```
Order Service
      ↓
Payment Service
      ↓
Inventory Service
      ↓
Restaurant Service
      ↓
Database
```

If all calls are sequential:

```
Payment       100ms
Inventory     150ms
Restaurant    100ms
Database      200ms
              ─────
Total         550ms
```

This is why adding more synchronous dependencies to a request path can make the system slower.

---

### **8. Network Latency**

Communication between services takes time.

For example:

```
Service A
    ↓
Network
    ↓
Service B
```

Even if Service B performs its operation in 5ms, the overall interaction may take longer because of:

- Network transmission
- Connection handling
- Processing
- Queuing
- Response transmission

In distributed systems:

**A remote call is not free.**

This becomes increasingly important in microservice architectures.

---

### **9. Database Latency**

Database operations can become a major source of latency.

For example:

```
Backend
   ↓
Database Query
   ↓
200ms
```

Possible causes include:

- Poor query
- Missing index
- Large dataset
- Lock contention
- High database load
- Network delay
- Connection pool exhaustion

The solution depends on the actual bottleneck.

---

### **10. Cache and Latency**

A cache can reduce latency by avoiding an expensive database operation.

Without cache:

```
Backend
   ↓
Database
   ↓
100ms
```

With cache:

```
Backend
   ↓
Redis
   ↓
5ms
```

If the data is already cached:

```
Backend
   ↓
Cache HIT
   ↓
Response
```

The request may avoid the database completely.

However, cache access itself still has some latency.

---

### **11. Latency vs Timeout**

These are related but different.

**Latency:**

How long an operation actually takes.

**Timeout:**

The maximum amount of time the caller is willing to wait.

Example:

```
Database latency = 3 seconds

Backend timeout = 2 seconds
```

The backend may stop waiting after 2 seconds and treat the operation as failed.

```
Request
   ↓
Database
   ↓
2 sec
   ↓
TIMEOUT
```

The underlying database operation may still have taken longer or continued depending on the system.

---

### **12. Tail Latency**

Average latency does not tell the whole story.

Suppose 100 requests have:

```
Most requests → 50ms
A few requests → 2 seconds
```

The average might still look reasonable while some users experience very slow responses.

This is why distributed systems often look at **percentiles**.

For example:

```
p50 → 50ms
p95 → 100ms
p99 → 500ms
```

Meaning:

- **p50:** 50% of requests are at or below 50ms
- **p95:** 95% are at or below 100ms
- **p99:** 99% are at or below 500ms

The remaining 1% can be slower than 500ms.

**p99 is an example of tail latency measurement.**

---

### **13. Why Tail Latency Matters**

Imagine an API calls three services:

```
Backend
   ├── Service A
   ├── Service B
   └── Service C
```

Even if each service is usually fast, occasional slow responses can make the overall request slow.

As the number of dependencies increases, the probability that **at least one dependency is slow** can increase.

This is one reason large distributed systems pay close attention to tail latency.

---

## **🗺️ Diagram**

### **End-to-End Latency**

```
Client
   │
   │ 20ms
   ↓
Network
   │
   │ 5ms
   ↓
Load Balancer
   │
   │ 10ms
   ↓
Backend
   │
   │ 15ms
   ↓
Redis
   │
   │ 5ms
   ↓
Backend
   │
   │ 100ms
   ↓
Database
   │
   │
   ↓
Backend
   │
   ↓
Client
```

---

### **Sequential vs Parallel**

```
SEQUENTIAL

Backend
   ↓
Service A ── 100ms
   ↓
Service B ── 200ms
   ↓
Response

Total ≈ 300ms
```

```
PARALLEL

             ┌── Service A ── 100ms
Backend ─────┤
             └── Service B ── 200ms
                       ↓
                    Response

Total ≈ 200ms
```

---

### **Critical Path**

```
                    ┌── Logging ── 100ms
                    │
Backend ────────────┼── Database ── 200ms
                    │
                    └── Cache ───── 20ms
                             
Required operations
        ↓
Determine response time
```

---

## **💻 Code**

```
<!-- only when useful -->
```

A simple way to measure an operation:

```
long start = System.currentTimeMillis();

Order order = orderRepository.findById(orderId)
        .orElseThrow();

long latency = System.currentTimeMillis() - start;

System.out.println("DB latency: " + latency + " ms");
```

In production systems, latency is normally measured using proper metrics, tracing, and monitoring rather than `System.out.println()`.

---

## **🏗️ Real Example**

Consider:

```
GET /orders/123
```

Suppose the backend performs:

```
Authentication       → 10ms
Redis lookup         → 5ms
Database query       → 100ms
Response processing  → 15ms
```

If executed sequentially:

```
10 + 5 + 100 + 15
= 130ms
```

Now suppose the cache contains the order:

```
Authentication       → 10ms
Redis lookup         → 5ms
Database             → skipped
Response processing  → 15ms
```

Approximate latency:

```
10 + 5 + 15
= 30ms
```

The cache reduced latency by avoiding the database operation.

---

### **Another Example — Parallel Calls**

Suppose an order-details page needs:

```
Order Service      → 100ms
Payment Service    → 150ms
Restaurant Service → 80ms
```

If sequential:

```
100 + 150 + 80
= 330ms
```

If they can safely execute in parallel:

```
max(100, 150, 80)
= 150ms
```

This is why parallelizing independent work can significantly reduce request latency.

---

## **🎯 Interview Questions**

### **Q1. What is latency?**

**Answer:**

Latency is the time taken for an operation or request to complete.

For an API, it is commonly measured from when the request is sent until the response is received.

---

### **Q2. What is the difference between latency and throughput?**

**Answer:**

Latency measures the time taken by an operation.

Throughput measures how many operations a system can process per unit of time.

---

### **Q3. If Service A takes 100ms and Service B takes 200ms sequentially, what is the approximate latency contribution?**

**Answer:**

```
100 + 200 = 300ms
```

---

### **Q4. If those same services execute independently in parallel?**

**Answer:**

Approximately:

```
max(100, 200) = 200ms
```

The backend must wait for the slower operation.

---

### **Q5. What is a critical path?**

**Answer:**

The critical path is the sequence of required operations that determines when the response can be completed.

Operations outside the critical path don’t necessarily add to user-visible latency if they don’t block the response.

---

### **Q6. Does adding another microservice always add latency?**

**Answer:**

A synchronous remote call generally introduces additional communication and processing overhead, so it can add latency.

However, the actual impact depends on whether the call is on the critical path, whether it is parallelized, and the communication architecture.

---

### **Q7. How can caching reduce latency?**

**Answer:**

Caching can avoid expensive operations such as database queries.

```
Cache HIT
   ↓
Return data

instead of:

Cache MISS
   ↓
Database
   ↓
Return data
```

---

### **Q8. What is the difference between timeout and latency?**

**Answer:**

Latency is the time an operation takes.

A timeout is the maximum time a caller is willing to wait before treating the operation as unsuccessful.

---

### **Q9. Why is p99 latency useful?**

**Answer:**

p99 shows the latency experienced by the slowest approximately 1% of requests and helps expose tail-latency problems that averages can hide.

---

### **Q10. If an API has 50ms average latency, does that mean every request takes 50ms?**

**Answer:**

No.

Some requests may be much faster and others much slower. Percentiles such as p50, p95, and p99 provide a better picture of the latency distribution.

---

### **Q11. How can you reduce request latency?**

**Answer:**

Depending on the bottleneck:

- Cache frequently accessed data
- Optimize database queries
- Add appropriate indexes
- Reduce unnecessary network calls
- Parallelize independent operations
- Reduce payload size
- Use connection reuse/pooling
- Move non-critical work off the synchronous path
- Scale overloaded components

The correct optimization should be based on measurement rather than assumptions.

---

## **⚠️ Mistakes / Gotchas**

### **1. Low latency does not mean high throughput**

They measure different properties.

```
Latency   → How long?
Throughput → How many?
```

---

### **2. Don’t simply add every operation’s latency**

Only operations on the **critical path** necessarily contribute directly to synchronous response latency.

Parallel operations behave differently from sequential operations.

---

### **3. Average latency can hide slow requests**

Always consider latency distribution and tail latency.

```
Average → useful
p95/p99 → also important
```

---

### **4. More servers don’t automatically reduce latency**

Adding backend instances primarily helps with capacity and load distribution.

If every request still waits 2 seconds for the same slow database query, adding more backend servers may not solve the database latency problem.

---

### **5. Caching isn’t free**

A cache itself has:

- Network cost
- Processing cost
- Memory cost
- Cache-miss behavior

It reduces latency when it successfully avoids a more expensive operation.

---

### **6. Parallelism has requirements**

Two operations can only safely run in parallel when their dependencies and correctness requirements allow it.

If:

```
B depends on A
```

they cannot simply be executed simultaneously.

---

### **7. Timeout is not a latency optimization**

A shorter timeout makes the system stop waiting sooner; it doesn’t make the dependency faster.

---

## **🔑 Key Takeaways**

```
Latency
   ↓
How long does the operation take?
```

Remember:

1. **Latency measures time.**
2. **Throughput measures processing capacity over time.**
3. Sequential operations add to the critical path.
4. Independent parallel operations are dominated by the slowest one.
5. Remote dependencies introduce communication overhead.
6. Database operations can become major latency bottlenecks.
7. Caching can reduce latency by avoiding expensive operations.
8. Timeouts limit how long a caller waits.
9. Average latency can hide tail latency.
10. p95/p99 help understand slow requests.
11. Only operations that block the response necessarily contribute directly to user-visible synchronous latency.
12. **Measure first, then optimize the actual bottleneck.**