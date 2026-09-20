# Real-World Request Flows

![HLD](https://img.shields.io/badge/HLD-Architecture%20Fundamentals-blue)
![Request Flows](https://img.shields.io/badge/Topic-Real--World%20Flows-orange)
![Caching](https://img.shields.io/badge/Concept-Caching-green)
![Database](https://img.shields.io/badge/Concept-Database-purple)
![Async Processing](https://img.shields.io/badge/Concept-Async%20Processing-red)

---

## **❓ Problem**

In a real production system, a request rarely follows:

```
Client → Backend → Database → Client
```

A single user action can involve multiple components:

```
Client
  ↓
DNS
  ↓
Load Balancer
  ↓
Backend
  ↓
Cache
  ↓
Database
  ↓
External Services
```

Understanding real-world request flows helps us see:

- How different components work together
- Where caching fits
- When a database is accessed
- How multiple services interact
- Which operations are synchronous
- Which operations can be asynchronous
- How one user action can trigger many internal operations

---

## **📋 Prerequisites**

- Client-Server Architecture
- Request / Response
- Backend Request Processing Pipeline
- Complete Request Lifecycle
- Failure Points
- Latency
- Basic Cache and Database concepts
- Basic Load Balancer concepts

---

## **🧠 Theory**

### **1. Real-World Request Flow**

A real-world request is an end-to-end flow through multiple components.

For example:

```
GET /orders/123
```

A simplified production architecture might look like:

```
Client
   ↓
DNS
   ↓
Load Balancer
   ↓
Backend
   ↓
Redis
   ↓
Database
   ↓
Backend
   ↓
Client
```

The exact architecture depends on the system.

---

### **2. Simple Read Request**

Consider:

```
GET /products/123
```

The backend may first check the cache.

```
Client
   ↓
Load Balancer
   ↓
Product Service
   ↓
Redis
```

If Redis contains the product:

```
Redis HIT
   ↓
Product data
   ↓
Backend
   ↓
Client
```

The database isn’t needed.

---

### **3. Cache Miss Flow**

If Redis doesn’t contain the product:

```
Client
   ↓
Backend
   ↓
Redis
   ↓ MISS
Database
   ↓
Product data
   ↓
Backend
   ↓
Client
```

The backend may also populate the cache:

```
Database
   ↓
Product data
   ↓
Redis
   ↓
Backend
```

A common pattern is:

```
Read Cache
    ↓
 HIT ─────────→ Return data
    │
    MISS
    ↓
Read Database
    ↓
Populate Cache
    ↓
Return data
```

---

### **4. Write Request**

Now consider:

```
POST /orders
```

A write request may involve more components than a simple read.

```
Client
   ↓
Load Balancer
   ↓
Order Service
   ↓
Inventory Service
   ↓
Database
   ↓
Payment Service
   ↓
Response
```

The exact order depends on the business requirements.

For example, the system may need to verify inventory before confirming the order.

---

### **5. One Request → Multiple Services**

In a distributed system:

```
Client
   ↓
API
   ↓
Order Service
   ├── Inventory Service
   ├── Pricing Service
   └── Payment Service
```

The client doesn’t need to know about all these internal services.

From the client’s perspective:

```
POST /orders
       ↓
Response
```

Internally:

```
POST /orders
       ↓
Order Service
       ├── Check Inventory
       ├── Calculate Price
       ├── Process Payment
       └── Create Order
```

This demonstrates:

**One external request can trigger multiple internal operations and service calls.**

---

### **6. Synchronous Request Flow**

In a synchronous flow, the caller waits for the operation to complete.

Example:

```
Client
   ↓
Order Service
   ↓
Payment Service
   ↓
Payment Result
   ↓
Order Service
   ↓
Client
```

The client waits for the payment result before receiving the response.

---

### **7. Asynchronous Request Flow**

Not every operation needs to happen before the client receives a response.

Suppose an order is created and the system needs to send a notification.

Instead of:

```
Create Order
    ↓
Send Notification
    ↓
Notification Result
    ↓
Response
```

the system could use asynchronous processing:

```
Create Order
    ↓
Save Order
    ↓
Publish Event
    ↓
Response
```

Then:

```
Event
  ↓
Message Broker
  ↓
Notification Service
  ↓
Send Notification
```

The user doesn’t need to wait for the notification to be sent.

This reduces work on the synchronous request path.

---

### **8. Read Flow vs Write Flow**

A read request commonly looks like:

```
Client
   ↓
Backend
   ↓
Cache
   ↓
Database (if needed)
   ↓
Backend
   ↓
Client
```

A write request may look like:

```
Client
   ↓
Backend
   ↓
Business Logic
   ↓
Database
   ↓
Event / Message
   ↓
Response
```

The architecture depends on the requirements.

---

### **9. External Service Flow**

Backend systems frequently depend on third-party services.

For example:

```
Client
   ↓
Order Service
   ↓
Payment Provider
   ↓
Payment Result
   ↓
Order Service
   ↓
Client
```

The payment provider becomes a dependency of the order flow.

This introduces additional considerations:

- Network latency
- Timeouts
- Retries
- Failure handling
- Idempotency
- Dependency availability

These concepts become increasingly important in distributed systems.

---

### **10. Request Flow Is a Design Tool**

When designing a system, don’t only draw components.

Trace an actual request:

```
User clicks "Place Order"
          ↓
What request is sent?
          ↓
Which service receives it?
          ↓
What data is needed?
          ↓
Which dependencies are called?
          ↓
Which operations must complete?
          ↓
Which operations can happen asynchronously?
          ↓
What response is returned?
```

This helps reveal unnecessary dependencies, bottlenecks, and failure points.

---

## **🗺️ Diagram**

### **Read Request — Cache Hit**

```
        GET /products/123
                │
                ↓
             Client
                │
                ↓
         Load Balancer
                │
                ↓
         Product Service
                │
                ↓
              Redis
                │
             CACHE HIT
                │
                ↓
         Product Service
                │
                ↓
             Client
```

---

### **Read Request — Cache Miss**

```
Client
  ↓
Load Balancer
  ↓
Backend
  ↓
Redis
  ↓ MISS
Database
  ↓
Backend
  ↓
Client
```

Optional cache population:

```
Database
   ↓
Result
   ├────────→ Backend
   │
   └────────→ Redis
```

---

### **Write Request**

```
Client
   ↓
Load Balancer
   ↓
Order Service
   ↓
Business Logic
   ↓
Database
   ↓
Response
   ↓
Client
```

---

### **Distributed Write**

```
                         ┌── Inventory Service
                         │
Client → Order Service ──┼── Payment Service
                         │
                         └── Database
                                │
                                ↓
                              Event
                                │
                                ↓
                         Message Broker
                                │
                                ↓
                      Notification Service
```

---

### **Synchronous vs Asynchronous**

```
SYNCHRONOUS

Client
  ↓
Order Service
  ↓
Payment Service
  ↓
Payment Result
  ↓
Order Service
  ↓
Client
```

```
ASYNCHRONOUS

Client
  ↓
Order Service
  ↓
Database
  ↓
Event / Message
  ↓
Client

Event
  ↓
Message Broker
  ↓
Notification Service
```

---

## **💻 Code**

```
<!-- only when useful -->
```

A simplified example of a cache-aside read:

```
public Product getProduct(Long productId) {

    Product cached = redis.get(productId);

    if (cached != null) {
        return cached;
    }

    Product product = productRepository.findById(productId)
            .orElseThrow();

    redis.set(productId, product);

    return product;
}
```

Conceptually:

```
Cache
  ↓
HIT  → return
  ↓
MISS
  ↓
Database
  ↓
Populate Cache
  ↓
Return
```

---

## **🏗️ Real Example**

Consider a food-ordering application.

The user taps:

**Place Order**

The client sends:

```
POST /orders
```

with:

```
{
  "restaurantId": "R101",
  "items": [
    {
      "itemId": "I55",
      "quantity": 2
    }
  ]
}
```

A possible real-world flow:

```
Mobile App
    ↓
Load Balancer
    ↓
Order Service
    ↓
Authentication
    ↓
Validation
    ↓
Check Restaurant
    ↓
Inventory Service
    ↓
Calculate Price
    ↓
Payment Service
    ↓
Order Database
    ↓
Publish OrderCreated Event
    ↓
HTTP Response
    ↓
Mobile App
```

Then asynchronously:

```
OrderCreated Event
        ↓
Message Broker
        ↓
Notification Service
        ↓
Send Order Confirmation
```

The user receives the order confirmation without necessarily waiting for the notification system to finish.

---

## **🎯 Interview Questions**

### **Q1. Why do real-world requests involve multiple components?**

**Answer:**

Production systems separate responsibilities across components such as load balancers, caches, databases, services, and message brokers. A request may therefore cross multiple components before producing a response.

---

### **Q2. What happens during a cache hit?**

**Answer:**

The backend retrieves the required data from the cache and can return it without querying the database.

```
Backend
  ↓
Cache HIT
  ↓
Response
```

---

### **Q3. What happens during a cache miss?**

**Answer:**

The backend retrieves the data from another source, commonly the database, and may populate the cache before returning the result.

```
Cache
  ↓ MISS
Database
  ↓
Data
  ↓
Cache
  ↓
Backend
```

---

### **Q4. What is the difference between synchronous and asynchronous processing?**

**Answer:**

In synchronous processing, the caller waits for the operation to complete.

In asynchronous processing, the caller can continue after submitting the work, while another component processes it later.

---

### **Q5. Why move some work to asynchronous processing?**

**Answer:**

To remove non-critical work from the synchronous request path.

This can reduce response latency and allow background work to be processed independently.

---

### **Q6. Can one HTTP request call multiple services?**

**Answer:**

Yes.

For example:

```
POST /orders
    ↓
Order Service
    ├── Inventory Service
    ├── Payment Service
    └── Pricing Service
```

---

### **Q7. Does every read request require a database query?**

**Answer:**

No.

A cache hit or another data source may satisfy the request without accessing the database.

---

### **Q8. What happens if a dependency becomes slow?**

**Answer:**

If the dependency is on the synchronous critical path, its latency can increase the overall request latency and may eventually cause a timeout.

Appropriate timeout, retry, fallback, or asynchronous strategies may be considered depending on the dependency.

---

### **Q9. Why should we trace an actual request when designing a system?**

**Answer:**

Tracing a request reveals:

- Components involved
- Data dependencies
- Network calls
- Critical-path operations
- Potential bottlenecks
- Failure points
- Opportunities for asynchronous processing

---

### **Q10. What is the difference between an external request and internal operations?**

**Answer:**

The client may send one HTTP request, while the backend performs multiple internal operations across databases, caches, and services.

```
1 External Request
        ↓
Multiple Internal Operations
        ↓
1 External Response
```

---

## **⚠️ Mistakes / Gotchas**

### **1. Don’t assume every request follows the same architecture**

A read, write, upload, payment, and search request can have very different flows.

---

### **2. Don’t assume every request needs a database**

A cache or another service may provide the required data.

---

### **3. Don’t make everything synchronous**

If an operation does not need to finish before the response, consider whether it belongs on an asynchronous path.

---

### **4. Don’t make everything asynchronous**

Some operations are inherently required before responding.

For example, an order cannot normally be confirmed before the system has established the required payment/order state.

---

### **5. More services don’t automatically mean better architecture**

Every additional synchronous service call can introduce:

- Network overhead
- Latency
- Failure points
- Operational complexity

---

### **6. Internal architecture should not be confused with the external API**

The client might see:

```
POST /orders
```

while internally the system performs:

```
Inventory
Payment
Database
Events
Notification
```

The external API hides this internal complexity.

---

### **7. Async processing changes the reliability model**

When work is moved to a queue or event system, the client may receive success before all downstream work has completed.

The system then needs to handle:

- Retries
- Duplicate messages
- Failed consumers
- Message delivery
- Eventual consistency

These topics will be covered later under **Messaging & Queues / Distributed Systems**.

---

## **🔑 Key Takeaways**

```
Real-World Request

Client
  ↓
DNS / Network
  ↓
Load Balancer
  ↓
Backend
  ↓
Cache / Database / Services
  ↓
Backend
  ↓
Response
  ↓
Client
```

Remember:

1. Real production requests usually cross multiple components.
2. A cache hit can avoid a database query.
3. A cache miss may lead to a database query.
4. One external request can trigger multiple internal operations.
5. Synchronous operations block the request until they complete.
6. Non-critical work can sometimes be moved to an asynchronous path.
7. Every synchronous dependency can add latency and another failure point.
8. Request-flow diagrams help identify dependencies and critical paths.
9. The exact flow depends on the system and the operation being performed.
10. **When designing an HLD, trace the important user actions end-to-end rather than looking only at individual components.**