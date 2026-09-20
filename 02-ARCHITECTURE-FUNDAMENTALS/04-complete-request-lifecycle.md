# Complete Request Lifecycle

![HLD](https://img.shields.io/badge/HLD-Architecture%20Fundamentals-blue)
![Topic](https://img.shields.io/badge/Topic-Request%20Lifecycle-orange)
![End-to-End](https://img.shields.io/badge/Concept-End--to--End-green)
![DNS](https://img.shields.io/badge/Component-DNS-purple)
![Load Balancer](https://img.shields.io/badge/Component-Load%20Balancer-red)
![Database](https://img.shields.io/badge/Component-Database-yellow)

---

## **❓ Problem**

An HTTP request is not simply:

```
Client → Backend → Response
```

In a real production system, the request may pass through several components before reaching the backend and may interact with multiple internal systems before the response reaches the client.

Understanding the **complete request lifecycle** helps us reason about:

- Where a request travels
- Which components participate
- Where processing happens
- How dependencies are involved
- Where failures can occur
- Where latency can be introduced

The goal is to understand the **end-to-end journey of a request**.

---

## **📋 Prerequisites**

- Client-Server Architecture
- Request / Response
- HTTP Request / Response
- Backend Request Processing Pipeline
- DNS basics
- Load Balancer basics
- Cache and Database basics

---

## **🧠 Theory**

### **1. What is the Complete Request Lifecycle?**

The complete request lifecycle is the journey of a request:

**from the moment a client initiates it until the client receives and processes the response.**

A simplified view:

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

A production system can be more like:

```
Client
  ↓
DNS
  ↓
Network
  ↓
Load Balancer
  ↓
Backend
  ↓
Cache / Database / Services
  ↓
Backend
  ↓
Load Balancer
  ↓
Network
  ↓
Client
```

This is a conceptual model. The exact path depends on the system architecture.

---

### **2. Step 1 — Client Creates the Request**

Suppose a user opens an order:

```
GET /orders/123
```

The client creates an HTTP request containing information such as:

```
Method
Path
Headers
Query Parameters
Body (if applicable)
```

For example:

```
GET /orders/123
Authorization: Bearer <token>
Accept: application/json
```

The client then needs to send this request to the server.

---

### **3. Step 2 — DNS Resolution**

The client needs to determine where the domain should be reached.

For example:

```
api.example.com
      ↓
DNS Resolution
      ↓
IP Address
```

The client can then use the resolved destination to establish communication.

DNS results can be cached, so DNS resolution does **not necessarily happen as a fresh lookup for every request**.

---

### **4. Step 3 — Network Communication**

The request travels through the network toward the destination.

Conceptually:

```
Client
  ↓
Local Network / ISP
  ↓
Internet
  ↓
Destination
```

The exact network path can vary.

At this level, the important idea is:

The request must travel from the client to the system hosting the application.

Detailed TCP, TLS, routing, and packet-level behavior belongs to Networking Fundamentals.

---

### **5. Step 4 — Load Balancer**

In a production system, the request may first reach a load balancer.

```
                 ┌── Backend A
Client → LB ─────┼── Backend B
                 └── Backend C
```

The load balancer selects an appropriate backend instance.

For example:

```
Backend A → Unhealthy
Backend B → Healthy
Backend C → Healthy
```

The request may be routed to B or C according to the load-balancing strategy.

The important purpose is:

**Distribute incoming traffic across available backend instances.**

---

### **6. Step 5 — Backend Receives the Request**

The selected backend receives the HTTP request.

For example:

```
GET /orders/123
```

The backend processes it through its request-processing pipeline:

```
Request Reception
       ↓
Validation
       ↓
Authentication
       ↓
Authorization
       ↓
Business Logic
       ↓
Data / External Services
       ↓
Build Response
```

This is where our **Backend Request Processing Pipeline** fits into the larger lifecycle.

---

### **7. Step 6 — Backend Accesses Dependencies**

The backend may need information from other systems.

For example:

```
Backend
   ├── Redis
   ├── Database
   ├── Payment Service
   ├── Inventory Service
   └── Other Services
```

For:

```
GET /orders/123
```

the backend might first check Redis:

```
Backend
   ↓
Redis
   ↓
Cache HIT
   ↓
Return order
```

If Redis returns a miss:

```
Backend
   ↓
Redis
   ↓ MISS
Database
   ↓
Order data
   ↓
Backend
```

The client still sees this as **one HTTP request**.

Internally, the backend may have performed several operations.

---

### **8. Step 7 — Backend Builds the Response**

After completing the required processing, the backend creates an HTTP response.

For example:

```
HTTP/1.1 200 OK
Content-Type: application/json
```

```
{
  "orderId": "123",
  "status": "SHIPPED"
}
```

Conceptually:

```
Internal Result
      ↓
HTTP Response
      ↓
Status + Headers + Body
```

---

### **9. Step 8 — Response Travels Back**

The response travels back toward the client.

With a proxy/load-balancer architecture, a simplified model is:

```
Backend
   ↓
Load Balancer
   ↓
Network
   ↓
Client
```

The load balancer is **not selecting another backend for the response**.

It is forwarding the response associated with the request/connection.

---

### **10. Step 9 — Client Receives and Processes the Response**

The client receives:

```
200 OK
```

```
{
  "orderId": "123",
  "status": "SHIPPED"
}
```

The application can then:

- Parse the response
- Update application state
- Update the UI
- Display the result
- Handle errors

The request lifecycle is complete.

---

## **🗺️ Diagram**

### **Complete Lifecycle**

```
┌──────────────┐
│    Client    │
└──────┬───────┘
       │
       │ HTTP Request
       ↓
┌──────────────┐
│     DNS      │
└──────┬───────┘
       │
       ↓
┌──────────────┐
│    Network   │
└──────┬───────┘
       │
       ↓
┌──────────────┐
│Load Balancer │
└──────┬───────┘
       │
       ↓
┌─────────────────────┐
│       Backend       │
│                     │
│ Reception           │
│ Validation          │
│ Authentication      │
│ Authorization       │
│ Business Logic      │
└──────────┬──────────┘
           │
           ↓
      ┌────┴─────┐
      │          │
      ↓          ↓
   Redis      Database
      │          │
      └────┬─────┘
           ↓
┌─────────────────────┐
│       Backend       │
│   Build Response    │
└──────────┬──────────┘
           │
           ↓
┌──────────────┐
│Load Balancer │
└──────┬───────┘
       │
       ↓
┌──────────────┐
│    Network   │
└──────┬───────┘
       │
       ↓
┌──────────────┐
│    Client    │
└──────────────┘
```

### **Request and Response Direction**

```
REQUEST
Client
  ↓
DNS
  ↓
Network
  ↓
Load Balancer
  ↓
Backend
  ↓
Cache / Database
  ↓
Backend

RESPONSE
Backend
  ↓
Load Balancer
  ↓
Network
  ↓
Client
```

---

## **💻 Code**

```
<!-- only when useful -->
```

A simplified Spring Boot request:

```
@GetMapping("/orders/{id}")
public ResponseEntity<OrderResponse> getOrder(
        @PathVariable Long id) {

    Order order = orderService.getOrder(id);

    return ResponseEntity.ok(
            new OrderResponse(
                    order.getId(),
                    order.getStatus()
            )
    );
}
```

The controller represents only a small part of the lifecycle.

Conceptually:

```
Client
  ↓
HTTP
  ↓
Load Balancer
  ↓
Spring Boot
  ↓
Controller
  ↓
Service
  ↓
Cache / Repository
  ↓
Database
  ↓
Service
  ↓
Controller
  ↓
HTTP Response
  ↓
Client
```

---

## **🏗️ Real Example**

Consider a food-ordering application.

The user opens:

```
GET /orders/123
```

### **End-to-end flow**

```
1. Mobile App
      ↓
2. DNS resolves API domain
      ↓
3. Request travels through network
      ↓
4. Load Balancer receives request
      ↓
5. LB selects healthy Backend B
      ↓
6. Backend authenticates user
      ↓
7. Backend checks authorization
      ↓
8. Backend checks Redis
      ↓
9. Redis MISS
      ↓
10. Backend queries Database
      ↓
11. Database returns order
      ↓
12. Backend builds HTTP response
      ↓
13. Response returns through LB/network
      ↓
14. Mobile App receives response
      ↓
15. UI displays order
```

The important observation is:

```
One External Request
        ↓
Multiple Internal Operations
        ↓
One External Response
```

---

## **🎯 Interview Questions**

### **Q1. What is the complete request lifecycle?**

**Answer:**

It is the end-to-end journey of a request from client initiation through network and backend processing, including dependency interactions, until the response reaches the client.

---

### **Q2. Does DNS necessarily perform a fresh lookup for every request?**

**Answer:**

No.

DNS results can be cached at different levels. A client may already have a valid cached DNS result.

---

### **Q3. What is the role of a load balancer in the request lifecycle?**

**Answer:**

A load balancer distributes incoming requests among available backend instances according to its routing strategy and health information.

---

### **Q4. If the load balancer sends a request to Backend B, does it choose another backend for the response?**

**Answer:**

No.

The load balancer does not perform a new backend selection for the response. In a proxy-based architecture, it forwards the response associated with that request back toward the client.

---

### **Q5. Can one client request cause multiple backend operations?**

**Answer:**

Yes.

For example:

```
GET /orders/123
      ↓
Check Redis
      ↓
Query Database
      ↓
Fetch related data
      ↓
Build Response
```

The client still sees one request and one response.

---

### **Q6. Does every request have to go through a database?**

**Answer:**

No.

A request can potentially be served from a cache or another source.

```
Backend
   ↓
Redis HIT
   ↓
Response
```

The database isn’t necessarily accessed.

---

### **Q7. Where does the Backend Request Processing Pipeline fit into the complete lifecycle?**

**Answer:**

It is the **backend-side portion** of the larger end-to-end lifecycle.

```
Client
  ↓
DNS / Network / LB
  ↓
┌─────────────────────────┐
│ Backend Processing      │
│                         │
│ Reception               │
│ Validation              │
│ Authentication          │
│ Authorization           │
│ Business Logic          │
│ Data Interaction        │
└─────────────────────────┘
  ↓
Response
  ↓
Client
```

---

### **Q8. Is the lifecycle diagram a strict representation of every production system?**

**Answer:**

No.

It is a conceptual model. Real systems may:

- Cache DNS results
- Reuse network connections
- Skip the load balancer
- Use multiple proxies
- Use CDNs
- Call multiple services
- Execute operations in parallel
- Process some work asynchronously

The purpose of the model is to understand the major stages and boundaries.

---

### **Q9. Why is understanding the complete lifecycle important in HLD?**

**Answer:**

Because system-level problems often occur at boundaries between components.

Understanding the lifecycle helps identify:

- Dependencies
- Failure points
- Latency sources
- Scaling requirements
- Bottlenecks
- Reliability concerns

It provides the foundation for reasoning about larger distributed systems.

---

## **⚠️ Mistakes / Gotchas**

### **1. DNS is not necessarily a network hop**

DNS resolution happens before communication with the resolved destination, but cached DNS results can eliminate a fresh lookup.

---

### **2. The diagram is not packet-level networking**

The lifecycle is a **high-level architectural model**.

Don’t interpret:

```
DNS → Network → LB
```

as a literal sequence of physical network hops.

---

### **3. Load Balancer ≠ Backend**

The load balancer decides where incoming traffic should go.

The backend performs application processing.

```
Load Balancer → Traffic distribution

Backend → Application processing
```

---

### **4. One request can involve many internal operations**

```
One HTTP Request
        ↓
Redis
Database
Payment Service
Inventory Service
        ↓
One HTTP Response
```

Never assume:

```
1 Request = 1 Database Query
```

---

### **5. The database isn’t always the first dependency**

A backend may check a cache before accessing the database.

```
Backend
   ↓
Cache
   ├── HIT → Response
   │
   └── MISS → Database
```

---

### **6. Response does not mean “backend starts again”**

The backend processes the request, produces a response, and that response is returned through the relevant network/proxy architecture.

---

## **🔑 Key Takeaways**

```
Client
  ↓
DNS
  ↓
Network
  ↓
Load Balancer
  ↓
Backend
  ↓
Cache / Database / Services
  ↓
Backend
  ↓
Load Balancer
  ↓
Network
  ↓
Client
```

Remember:

1. **Complete Request Lifecycle = end-to-end journey of a request.**
2. DNS helps resolve the destination.
3. The network carries the communication.
4. A load balancer distributes incoming traffic.
5. The backend performs application processing.
6. The backend may communicate with multiple dependencies.
7. One external request can trigger many internal operations.
8. The backend builds one HTTP response for the client.
9. The response travels back through the relevant architecture.
10. The lifecycle is a **conceptual model** , not a rigid packet-by-packet sequence.
