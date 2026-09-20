# ⚖️ Load Balancer

![HLD](https://img.shields.io/badge/HLD-Architecture%20Fundamentals-blue)
![Load Balancer](https://img.shields.io/badge/Topic-Load%20Balancer-orange)
![L4 vs L7](https://img.shields.io/badge/Concept-L4%20vs%20L7-green)
![Algorithms](https://img.shields.io/badge/Concept-Algorithms-purple)
![Health Checks](https://img.shields.io/badge/Concept-Health%20Checks-red)

---

## **❓ Problem**

Imagine a backend API running on a single server:

```
                    ┌─────────────┐
Users ─────────────►│   App       │
                    │  Server     │
                    └─────────────┘
```

At first, everything works.

Then traffic grows:

```
1,000 requests/sec
        ↓
10,000 requests/sec
        ↓
50,000 requests/sec
```

The single server eventually becomes a bottleneck:

- CPU gets saturated
- Memory usage increases
- Connection limits are reached
- Response latency increases
- Requests start timing out
- A server failure can take the entire application down

We can add more servers:

```
              ┌── App 1
              │
Users ────────┼── App 2
              │
              └── App 3
```

But now another problem appears:

**Who decides which server should receive each request?**

That’s where the **Load Balancer (LB)** comes in.

---

# **📋 Prerequisites**

Before learning Load Balancers, understand:

- Client-Server Architecture
- Request/Response
- IP Address & Port
- TCP vs UDP
- HTTP / HTTPS
- DNS
- Basic networking concepts
- Horizontal Scaling — basic idea

---

# **🧠 Theory**

## **1. What Problem Does Load Balancing Solve?**

A **Load Balancer** distributes incoming network traffic across multiple backend instances.

Instead of:

```
Client → App Server
```

we have:

```
Client
   │
   ▼
Load Balancer
   │
   ├──► App 1
   ├──► App 2
   └──► App 3
```

The client generally interacts with the **load balancer endpoint**, rather than knowing individual backend instances.

### **Core idea**

**Load balancing separates the public entry point from the backend instances and distributes traffic among available instances.**

---

# **2. Why One Server Is Not Enough**

A single server has finite resources.

For example:

```
Server
├── CPU
├── RAM
├── Network bandwidth
└── Connection capacity
```

When traffic increases:

```
Traffic ↑
   ↓
CPU ↑
   ↓
Latency ↑
   ↓
Timeouts ↑
   ↓
Errors ↑
```

A server can also fail because of:

- Hardware problems
- Application crashes
- Memory exhaustion
- Deployment failures
- Network failures

### **Horizontal Scaling**

Instead of making one machine increasingly powerful:

```
Small Server
     ↓
Large Server
     ↓
Very Large Server
```

we can add more instances:

```
App 1
App 2
App 3
App 4
```

This is **horizontal scaling**.

---

# **3. Multiple Backend Instances**

Suppose we deploy the same application three times:

```
                 ┌── App Instance 1
                 │
Client → LB ─────┼── App Instance 2
                 │
                 └── App Instance 3
```

Each instance runs the same application.

For example:

```
App1 → 10.0.1.10:8080
App2 → 10.0.1.11:8080
App3 → 10.0.1.12:8080
```

The client doesn’t need to know these private backend addresses.

Instead:

```
api.example.com
       ↓
Load Balancer
       ↓
Backend instances
```

This abstraction becomes extremely useful when instances are dynamically created or removed.

---

# **4. Load Balancer Role**

The LB acts as an entry point between clients and backend servers.

Its responsibilities can include:

### **Traffic distribution**

```
Request → Backend instance
```

### **Health awareness**

```
Healthy instance → receive traffic
Unhealthy instance → stop receiving traffic
```

### **Routing**

Depending on the LB type:

```
TCP connection
HTTP path
HTTP host
Headers
Cookies
```

### **High availability**

The LB layer itself should not become a single point of failure.

---

# **5. Traffic Distribution**

Suppose:

```
App1
App2
App3
```

Requests:

```
R1
R2
R3
R4
R5
R6
```

The LB needs a strategy for deciding where requests/connections go.

For example, **Round Robin**:

```
R1 → App1
R2 → App2
R3 → App3
R4 → App1
R5 → App2
R6 → App3
```

The actual behavior depends on:

- Load-balancing algorithm
- L4 vs L7 operation
- Connection reuse
- Health state
- Backend capacity
- LB implementation

---

# **6. L4 vs L7**

This is one of the most important Load Balancer concepts.

A simplified networking view:

```
┌──────────────────────────────┐
│ L7 — Application             │
│ HTTP / HTTPS                 │
├──────────────────────────────┤
│ L4 — Transport               │
│ TCP / UDP                    │
├──────────────────────────────┤
│ L3 — Network                 │
│ IP                           │
└──────────────────────────────┘
```

Remember:

**L4 understands transport-level information. L7 understands application-level protocols such as HTTP.**

---

## **6.1 Layer 4 Load Balancing**

L4 operates at the transport layer.

It works with:

```
TCP
UDP
Ports
Connections / flows
```

For example:

```
Client
   │
   │ TCP :443
   ▼
L4 LB
   │
   ▼
Backend
```

The L4 LB doesn’t need to understand:

```
GET /api/orders
Host: api.example.com
Authorization: ...
```

It can forward the TCP/UDP traffic without understanding the application protocol.

### **Important**

An L4 LB **can carry HTTPS traffic**.

It doesn’t necessarily decrypt or understand the HTTPS payload.

---

# **6.2 Layer 7 Load Balancing**

L7 operates at the application layer.

For HTTP applications, it can understand:

- HTTP method
- URL/path
- Host
- Headers
- Cookies

For example:

```
GET /api/orders/123
Host: api.example.com
```

An L7 LB can route:

```
/api/orders/*  → Order Service

/api/users/*   → User Service

/api/payments/* → Payment Service
```

This is called **content-aware/request-aware routing**.

---

# **6.3 TLS Termination**

Consider:

```
Client
   │
   │ HTTPS
   ▼
L7 Load Balancer
   │
   │ HTTP
   ▼
Backend
```

The LB terminates the TLS connection.

Conceptually:

```
HTTPS
  ↓
TLS termination
  ↓
HTTP request
  ↓
Inspect /api/orders
  ↓
Route to backend
```

This allows the L7 LB to inspect the HTTP request.

### **Important distinction**

Don’t say:

“L7 is better because it decrypts HTTPS.”

Instead:

**L7 understands HTTP. If TLS is terminated at the LB, it can inspect the decrypted HTTP request and perform HTTP-aware routing.**

TLS can also be re-established between LB and backend:

```
Client
  │ HTTPS
  ▼
LB
  │ HTTPS
  ▼
Backend
```

---

# **6.4 L4 vs L7 Trade-offs**

|  | **L4** | **L7** |
| --- | --- | --- |
| Layer | Transport | Application |
| Protocols | TCP / UDP | HTTP / HTTPS |
| Understands HTTP | ❌ | ✅ |
| Path-based routing | ❌ | ✅ |
| Header-based routing | ❌ | ✅ |
| Connection/flow aware | ✅ | ✅ |
| Request/content aware | ❌ | ✅ |
| Processing | Generally simpler | More application-aware processing |
| Flexibility | Lower | Higher |

### **Mental model**

```
L4:
"Where should this network flow go?"

L7:
"Where should this HTTP request go?"
```

---

# **7. Load Balancing Algorithms**

The algorithm answers:

**How should the LB choose a backend?**

---

## **7.1 Round Robin**

The simplest approach.

```
App1 → App2 → App3 → App1 → App2 → App3
```

Example:

```
R1 → App1
R2 → App2
R3 → App3
R4 → App1
R5 → App2
R6 → App3
```

### **Mental model**

**Take turns.**

Useful when backend instances have approximately similar capacity and workload.

---

## **7.2 Weighted Round Robin**

Suppose:

```
App1 = 8 CPU
App2 = 4 CPU
App3 = 2 CPU
```

Equal distribution may not be appropriate.

We can assign:

```
App1 → weight 4
App2 → weight 2
App3 → weight 1
```

Conceptually:

```
App1 → 4 parts
App2 → 2 parts
App3 → 1 part
```

So App1 receives more traffic.

### **Mental model**

**Round Robin + different server capacities.**

---

## **7.3 Least Connections**

Instead of simply taking turns, the LB considers the number of active connections.

Example:

```
App1 → 50 active connections
App2 → 5 active connections
App3 → 20 active connections
```

A new connection may be sent to:

```
App2
```

because it currently has the fewest active connections.

### **Mental model**

**Send new connections toward the least-busy server.**

This can be useful when connection duration varies significantly.

---

## **7.4 IP Hash**

The LB calculates a hash using the client’s IP.

Conceptually:

```
hash(client IP) → backend
```

Example:

```
Client A → App1
Client B → App3
Client C → App2
```

The same client IP tends to map to the same backend while the backend set remains stable.

This can support **session affinity/sticky behavior** in some architectures.

### **Problem**

Many users may share a public IP because of NAT:

```
User A ─┐
User B ─┤
User C ─┼──► Same public IP ──► LB
User D ─┤
User E ─┘
```

That can create uneven distribution.

---

# **7.5 Consistent Hashing — Conceptual**

Suppose we have:

```
App1
App2
App3
```

and some key:

```
userId = 123
```

We can hash the key to determine where it belongs.

The problem is what happens when we add:

```
App4
```

A naive hashing strategy may cause many existing keys to move to different servers.

**Consistent hashing** is designed to minimize this remapping.

### **Mental model**

**When nodes change, keep as many existing key → node mappings as possible.**

This becomes particularly important later for:

- Distributed caches
- Sharding
- Partitioning
- Distributed systems

Don’t confuse this with ordinary Round Robin.

---

# **8. Health Checks**

Traffic should only be sent to instances that can actually serve requests.

```
             ┌── App1 ✅
Client → LB ─┼── App2 ❌
             └── App3 ✅
```

The LB needs to determine:

**Which instances are healthy and ready to receive traffic?**

---

## **8.1 Why Health Checks Are Needed**

Without health checks:

```
App2 crashes
   ↓
LB doesn't know
   ↓
Requests continue → App2
   ↓
Users receive errors
```

With health checks:

```
App2 crashes
   ↓
Health check fails
   ↓
LB marks App2 unhealthy
   ↓
Stop sending new traffic
```

---

## **8.2 Active Health Checks**

The LB can periodically send a request such as:

```
GET /health
```

Healthy response:

```
200 OK
```

Failure examples:

```
Timeout
Connection refused
5xx response
```

The LB can then update the backend’s health status.

---

# **8.3 Liveness vs Readiness**

These answer **different questions**.

### **Liveness**

**Is the application/process alive?**

```
Process running?
```

If liveness fails, an orchestrator may decide that the application needs restarting.

---

### **Readiness**

**Is the application currently ready to receive traffic?**

An application can be alive but not ready.

Example:

```
Application process → Running ✅
Application initialization → In progress
Required resources → Not ready
```

Therefore:

```
Liveness  → "Am I alive?"
Readiness → "Can I serve traffic?"
```

### **Why have both?**

Because:

```
Alive ≠ Ready
```

For example, an application may be running while:

- Starting up
- Loading configuration
- Initializing resources
- Temporarily unable to serve traffic

You may want to **stop traffic** without restarting the application.

### **Important operational principle**

Avoid making liveness depend on every external dependency.

Otherwise:

```
Database unavailable
       ↓
Liveness fails
       ↓
Restart application
       ↓
Database still unavailable
       ↓
Restart again
       ↓
Restart loop
```

Liveness and readiness should be designed according to the operational behavior you actually want.

---

# **8.4 What Happens When a Server Fails?**

Initially:

```
App1 ✅
App2 ✅
App3 ✅
```

App2 fails:

```
App1 ✅
App2 ❌
App3 ✅
```

Health checks detect the failure.

The LB marks:

```
App2 → Unhealthy
```

Traffic becomes:

```
R1 → App1
R2 → App3
R3 → App1
R4 → App3
```

App2 is removed from the traffic rotation.

---

# **8.5 Recovery**

Suppose App2 recovers:

```
App2 starting...
```

It may initially be:

```
Liveness  → ✅
Readiness → ❌
```

So the LB should not immediately send production traffic to it.

Once:

```
Readiness → ✅
```

the LB can add App2 back into the available backend pool.

Conceptually:

```
Healthy
   ↓
Failure
   ↓
Unhealthy
   ↓
Removed from traffic
   ↓
Recovery
   ↓
Health checks pass
   ↓
Added back
```

---

# **9. Load Balancer Placement**

A common architecture:

```
                         Internet
                            │
                            ▼
                           DNS
                            │
                            ▼
                    ┌──────────────┐
                    │ Load Balancer│
                    └──────┬───────┘
                           │
             ┌─────────────┼─────────────┐
             ▼             ▼             ▼
           App1          App2          App3
             │             │             │
             └─────────────┼─────────────┘
                           ▼
                         Cache
                           │
                           ▼
                           DB
```

The public entry point is generally:

```
api.example.com
```

rather than:

```
app1.internal
app2.internal
app3.internal
```

This gives us a clean separation:

```
Client
  ↓
Public entry point
  ↓
Load Balancer
  ↓
Private backend instances
```

---

# **10. Load Balancer Failure**

The LB itself can become a bottleneck or single point of failure if deployed as one instance:

```
Client
   ↓
 ONE LB ❌
   ↓
App1 App2 App3
```

If the LB fails:

```
Application becomes unreachable
```

Therefore, production architectures make the LB layer highly available.

Conceptually:

```
                  ┌── LB Instance 1
Client → Entry ───┤
                  └── LB Instance 2
                         │
                  ┌──────┼──────┐
                  ▼      ▼      ▼
                 App1   App2   App3
```

Managed cloud load-balancing services abstract much of this redundancy.

### **Principle**

**Never solve a scalability bottleneck by creating a new single point of failure.**

---

# **11. Horizontal Scaling + Load Balancer**

These two concepts work together.

### **Without horizontal scaling**

```
Client
  ↓
App1
```

### **With horizontal scaling**

```
              ┌── App1
Client → LB ──┼── App2
              └── App3
```

Traffic increases:

```
3 instances
     ↓
5 instances
     ↓
10 instances
```

The LB distributes traffic among the available healthy instances.

This is one of the foundational patterns of scalable backend systems.

---

# **12. Important: Load Balancer Does NOT Solve Every Bottleneck**

Suppose:

```
                  ┌── App1 ──┐
                  ├── App2 ──┤
Client → LB ──────┼── App3 ──┼──► DB 🔥
                  └── App4 ──┘
```

The application tier can scale horizontally.

But if:

```
DB CPU = 100%
```

adding more application servers does not necessarily help.

It may actually generate even more database traffic.

Possible solutions depend on the actual bottleneck:

- Query optimization
- Proper indexing
- Caching
- Read replicas
- Connection management
- Partitioning
- Sharding

We’ll cover these in the Database, Caching, and Distributed Systems sections.

---

# **13. Load Balancer vs Reverse Proxy vs API Gateway**

These concepts overlap in real products, but their **primary concerns differ**.

### **Load Balancer**

Main question:

**Which backend instance should receive this traffic?**

### **Reverse Proxy**

Main question:

**How should incoming traffic be proxied and handled before reaching backend services?**

Common capabilities:

- TLS termination
- Routing
- Compression
- Caching
- Connection management
- Security controls

### **API Gateway**

Main question:

**How should API traffic be managed at the service boundary?**

Common capabilities:

- Authentication/authorization integration
- Rate limiting
- API routing
- Request transformation
- API policies
- Observability

A single product can perform multiple roles, so these aren’t always physically separate components.

---

# **🗺️ Diagram**

## **Complete Load Balancer Architecture**

```
                         ┌───────────────┐
                         │   Internet    │
                         └───────┬───────┘
                                 │
                                 ▼
                         ┌───────────────┐
                         │      DNS      │
                         └───────┬───────┘
                                 │
                                 ▼
                    ┌────────────────────────┐
                    │     Load Balancer      │
                    │                        │
                    │ L4 / L7                │
                    │ Routing                │
                    │ Health Checks          │
                    │ TLS Termination        │
                    └───────────┬────────────┘
                                │
               ┌────────────────┼────────────────┐
               │                │                │
               ▼                ▼                ▼
          ┌─────────┐      ┌─────────┐      ┌─────────┐
          │  App 1  │      │  App 2  │      │  App 3  │
          │    ✅   │      │    ❌   │      │    ✅   │
          └────┬────┘      └─────────┘      └────┬────┘
               │                                  │
               └────────────────┬─────────────────┘
                                ▼
                         ┌─────────────┐
                         │    Cache    │
                         │   Redis     │
                         └──────┬──────┘
                                │
                                ▼
                         ┌─────────────┐
                         │  Database   │
                         └─────────────┘
```

---

## **L4 vs L7**

```
                 Client
                    │
              TCP / HTTPS
                    │
                    ▼
              ┌───────────┐
              │  L4 LB    │
              └─────┬─────┘
                    │
              TCP connection
                    │
                    ▼
                 Backend
```

versus:

```
                 Client
                    │
                  HTTPS
                    │
                    ▼
              ┌───────────┐
              │  L7 LB    │
              └─────┬─────┘
                    │
              TLS termination
                    │
                    ▼
            HTTP request
          /api/orders
                    │
                    ▼
              Order Service
```

---

# **💻 Code**

For the first-pass HLD understanding, **code is not necessary**.

The important thing is understanding the architecture and decision-making.

In a real system, the implementation could involve products such as:

- NGINX
- HAProxy
- AWS Application Load Balancer
- AWS Network Load Balancer
- Google Cloud Load Balancing
- Azure Load Balancer

The exact configuration depends on the infrastructure.

---

# **🏗️ Real Example**

## **AWS-style architecture**

A web application could look conceptually like:

```
                    Users
                      │
                      ▼
                    DNS
                      │
                      ▼
              AWS Load Balancer
                      │
             ┌────────┼────────┐
             ▼        ▼        ▼
           EC2      EC2      EC2
           App1     App2     App3
             │        │        │
             └────────┼────────┘
                      ▼
                    Redis
                      │
                      ▼
                  Database
```

For HTTP/HTTPS applications, an **Application Load Balancer (ALB)** provides application-layer routing capabilities.

For transport-level TCP/UDP use cases, an **AWS Network Load Balancer (NLB)** is designed for Layer 4-style load balancing.

The important architectural lesson is not the AWS product name:

**Choose the load-balancing layer and strategy based on what the application actually needs.**

---

# **🎯 Interview Questions**

### **Fundamentals**

1. What problem does a Load Balancer solve?
2. Why isn’t one powerful server always enough?
3. What is horizontal scaling?
4. How does a Load Balancer work with horizontal scaling?
5. Why shouldn’t clients directly communicate with individual backend instances?

### **L4 / L7**

6. What is L4 load balancing?
7. What is L7 load balancing?
8. What is the difference between L4 and L7?
9. Can an L4 LB handle HTTPS traffic?
10. What is TLS termination?
11. Why would you choose L7 over L4?
12. Can an L7 LB route requests based on URL path?

### **Algorithms**

13. Explain Round Robin.
14. When would Weighted Round Robin be useful?
15. What is Least Connections?
16. What is IP Hash?
17. What problem does Consistent Hashing solve?
18. Why can IP Hash create uneven distribution?

### **Health**

19. Why are health checks required?
20. What is the difference between liveness and readiness?
21. Can an application be alive but not ready?
22. What happens when a backend instance fails?
23. How does a recovered instance rejoin traffic?
24. Why shouldn’t liveness checks necessarily depend on every external dependency?

### **Architecture**

25. Where does a Load Balancer typically sit?
26. Can the Load Balancer itself become a single point of failure?
27. How do you make the LB layer highly available?
28. Does a Load Balancer solve database bottlenecks?
29. What is the difference between a Load Balancer and a Reverse Proxy?
30. What is the difference between a Load Balancer and an API Gateway?

---

# **⚠️ Mistakes / Gotchas**

### **❌ “Load Balancer = horizontal scaling”**

No.

```
Horizontal Scaling
→ Add/remove instances

Load Balancing
→ Distribute traffic among instances
```

They work together but solve different problems.

---

### **❌ “L4 cannot handle HTTPS”**

Not exactly.

An L4 LB can forward HTTPS/TLS traffic without understanding the HTTP contents.

The better statement is:

**L4 doesn’t need to understand HTTP; L7 can understand HTTP, and with TLS termination can inspect HTTPS requests.**

---

### **❌ “L7 is always better”**

No.

The requirement determines the appropriate layer.

```
Raw TCP service
    ↓
L4 may be appropriate

HTTP path-based routing
    ↓
L7 may be appropriate
```

---

### **❌ “Health check = application is alive”**

Not necessarily.

```
Alive ≠ Ready
```

An application can be running but temporarily unable to serve traffic.

---

### **❌ “More application servers always solve performance problems”**

No.

Your bottleneck might be:

```
Database
Cache
Network
External API
CPU
Disk
Lock contention
```

Always identify the bottleneck first.

---

### **❌ “The Load Balancer itself cannot fail”**

It can.

A production architecture needs redundancy/high availability at the LB layer too.

---

### **❌ “Round Robin always gives equal real-world load”**

Not necessarily.

Even if request counts are equal:

```
Request A → 2 ms
Request B → 2 seconds
```

Equal request distribution doesn’t mean equal resource utilization.

---

### **❌ “IP Hash guarantees perfect session distribution”**

No.

NAT, proxies, changing backend membership, and uneven client populations can produce skew.

---

# **🔑 Key Takeaways**

If you remember only these points:

```
1. Load Balancer
   ↓
   Distributes traffic across backend instances.

2. Horizontal Scaling
   ↓
   Adds more backend instances.

3. L4
   ↓
   TCP / UDP / connection-flow level.

4. L7
   ↓
   HTTP / HTTPS / request-aware routing.

5. Round Robin
   ↓
   Take turns.

6. Weighted Round Robin
   ↓
   Stronger instances get more traffic.

7. Least Connections
   ↓
   Prefer fewer active connections.

8. IP Hash
   ↓
   Same client IP tends toward same backend.

9. Consistent Hashing
   ↓
   Minimize remapping when nodes change.

10. Health Checks
    ↓
    Don't send traffic to unhealthy instances.

11. Liveness
    ↓
    Is it alive?

12. Readiness
    ↓
    Can it serve traffic?

13. LB + Horizontal Scaling
    ↓
    Foundational scalable architecture.

14. LB is not a solution for every bottleneck.
    ↓
    The database/cache/network can still become the bottleneck.
```

### **The one-line interview answer**

**A load balancer provides a stable entry point and distributes traffic across healthy backend instances, enabling horizontal scaling, fault isolation, and higher availability.**

---

# **📚 References**

Useful official documentation when you want to go deeper:

- AWS Elastic Load Balancing documentation
- AWS Application Load Balancer documentation
- AWS Network Load Balancer documentation
- NGINX documentation
- HAProxy documentation

---

# **🚧 Pending Topic**

Load Balancer first-pass fundamentals are **complete**.

The deeper topics can be revisited when we reach the relevant sections:

- HTTP/2 multiplexing and connection-level behavior
- HTTP/3 / QUIC
- Advanced consistent hashing
- Kubernetes readiness/liveness probes
- Multi-region load balancing
- Global traffic management
- Anycast
- Advanced failure handling
- Load-balancer observability
- Connection draining / graceful shutdown
- Sticky sessions and session affinity

### **Next HLD topic**

```
02 — Architecture Fundamentals
        │
        ├── Client-Server Architecture      ✅
        ├── Request/Response                ✅
        ├── 3-Layer Architecture            ✅
        ├── Networking Fundamentals         ✅
        ├── DNS                             ✅
        ├── Load Balancer                   ✅
        │
        └── Reverse Proxy                   🚧 NEXT
```

**Reverse Proxy** is the natural next step because it will let us clearly separate three concepts that are often confused in interviews:

```
Load Balancer
      vs
Reverse Proxy
      vs
API Gateway
```