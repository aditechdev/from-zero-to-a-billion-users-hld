# **Failure Points**

🏷️ Tags

---

## **❓ Problem**

A backend system is not just one component.

A typical request may travel through:

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
Cache
  ↓
Database
  ↓
External Services
```

Every component can fail.

If one component fails, the impact can range from:

- A single failed request
- Slower responses
- Partial functionality
- Complete service outage
- Cascading failures across the system

In HLD, we therefore need to ask:

**What happens if any component or dependency fails?**

---

## **📋 Prerequisites**

- Client-Server Architecture
- Request / Response
- Complete Request Lifecycle
- Backend Request Processing Pipeline
- Load Balancer basics
- Cache basics
- Database basics

---

## **🧠 Theory**

### **1. What Is a Failure Point?**

A **failure point** is a component, dependency, or boundary where something can go wrong and affect request processing.

For example:

```
Backend
   ↓
Database
   X
Connection timeout
```

The database is a failure point for this request.

---

### **2. Failure Can Occur at Every Layer**

Consider:

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
Cache
   ↓
Database
```

Possible failures:

```
Client
   ↓
   ❌ Network unavailable

DNS
   ↓
   ❌ DNS resolution failure

Load Balancer
   ↓
   ❌ LB unavailable

Backend
   ↓
   ❌ Server crash

Cache
   ↓
   ❌ Cache unavailable

Database
   ↓
   ❌ Database unavailable
```

The failure doesn’t always originate inside the application code.

---

### **3. Component Failure vs Dependency Failure**

These are useful distinctions.

#### **Component failure**

The application server itself fails.

```
Backend B
   ↓
   ❌ Crash
```

#### **Dependency failure**

The backend is healthy, but something it depends on fails.

```
Backend
   ↓
Database
   X
```

The backend may still be running, but requests requiring the database can fail.

---

### **4. Single Instance Failure**

Suppose we have three backend instances:

```
              ┌── Backend A
              │
Load Balancer ┼── Backend B
              │
              └── Backend C
```

Backend B crashes:

```
              ┌── Backend A ✅
              │
Load Balancer ┼── Backend B ❌
              │
              └── Backend C ✅
```

If the load balancer detects B as unhealthy, it can stop sending new requests to B.

This is an example of **fault tolerance through redundancy**.

The system doesn’t require every individual instance to be healthy.

---

### **5. Single Database Failure**

Now consider:

```
Backend A ──┐
Backend B ──┼──→ Database
Backend C ──┘
                 ❌
```

If the database is unavailable, multiple healthy backend instances may still be unable to process requests requiring that database.

This demonstrates an important HLD concept:

**Adding redundancy to one layer does not automatically eliminate failure at another layer.**

---

### **6. Failure Handling**

When a dependency fails, the application needs an appropriate strategy.

Possible approaches include:

#### **Retry**

Try the operation again.

```
Backend
   ↓
Database
   X
   ↓
Retry
   ↓
Database
   ↓
Success
```

Retries can help with **temporary/transient failures**.

But uncontrolled retries can make an outage worse.

---

#### **Timeout**

Don’t wait forever for a dependency.

```
Backend
   ↓
Database
   ↓
Waiting...
   ↓
Timeout
   ↓
Handle failure
```

A timeout prevents a request from consuming resources indefinitely.

---

#### **Fallback**

Use an alternative source or behavior.

Example:

```
Backend
   ↓
Redis
   X
   ↓
Database
   ↓
Data
```

If the architecture supports it, the backend can fall back to the database.

---

#### **Fail Fast**

If a dependency is known to be unavailable, stop waiting and return an error quickly.

```
Backend
   ↓
Payment Service
   X
   ↓
Fail Fast
   ↓
Error Response
```

This prevents resources from being held unnecessarily.

---

### **7. Cascading Failure**

One of the most important failure concepts in distributed systems.

Suppose:

```
Service A
   ↓
Service B
   ↓
Service C
```

Service C becomes slow.

Service B waits for C.

Service A waits for B.

Eventually:

```
Service C slows
      ↓
Service B waits
      ↓
Service A waits
      ↓
Requests accumulate
      ↓
Threads / connections consumed
      ↓
System becomes overloaded
```

This is a **cascading failure**.

A problem in one component spreads to other components.

---

### **8. Dependency Failure Is Not Always Total Failure**

Suppose an order service uses:

```
Order Service
   ├── Database
   ├── Payment Service
   └── Notification Service
```

Notification service fails.

Does the order necessarily have to fail?

Not necessarily.

If notification is not required to complete the order synchronously:

```
Order
   ↓
Database
   ↓
Order Created ✅
   ↓
Notification
   X
```

The system could process notification separately.

Whether this is appropriate depends on the business requirements.

This distinction leads to **asynchronous processing and event-driven architecture**, which we’ll study later.

---

### **9. Failure Domains**

A **failure domain** is a boundary within which failures can occur together.

For example:

```
          Application
        ┌──────┴──────┐
        ↓             ↓
    Server A       Server B
```

If Server A fails, Server B may continue serving traffic.

But if both servers depend on the same database:

```
Server A ──┐
           ├── Database ❌
Server B ──┘
```

the shared database becomes a larger failure domain.

This is why distributed systems try to avoid **single points of failure**.

---

### **10. Single Point of Failure**

A **Single Point of Failure (SPOF)** is a component whose failure can make the system unavailable or significantly impair it because there is no adequate alternative.

Example:

```
Backend A ──┐
Backend B ──┼──→ Single Database
Backend C ──┘          ❌
```

If that database is the only source of required data and becomes unavailable, the application may be unable to serve critical requests.

HLD often asks:

**What happens if this component goes down?**

If the answer is:

“The entire system stops.”

then we should investigate whether it is a single point of failure.

---

## **🗺️ Diagram**

### **Failure Points in a Request Path**

```
Client
  │
  ├── ❌ Client / Network failure
  ↓
DNS
  │
  ├── ❌ DNS failure
  ↓
Load Balancer
  │
  ├── ❌ LB failure
  ↓
Backend
  │
  ├── ❌ Instance crash
  ↓
Cache
  │
  ├── ❌ Cache unavailable
  ↓
Database
  │
  ├── ❌ Timeout
  ├── ❌ Connection failure
  └── ❌ Database unavailable
  ↓
External Service
  │
  └── ❌ Service unavailable
```

### **Redundancy**

```
                 ┌── Backend A ✅
                 │
Load Balancer ───┼── Backend B ❌
                 │
                 └── Backend C ✅
```

One backend failure does not necessarily mean system failure.

---

### **Cascading Failure**

```
Service A
    ↓
Service B
    ↓
Service C
    ↓
   Slow
    ↓
Service B waits
    ↓
Service A waits
    ↓
Requests accumulate
    ↓
Resources exhausted
    ↓
More failures
```

---

## **💻 Code**

```
<!-- only when useful -->
```

A simplified example of handling a dependency timeout:

```
public Order getOrder(Long orderId) {

    try {
        return orderRepository.findById(orderId)
                .orElseThrow(() -> new OrderNotFoundException(orderId));

    } catch (DatabaseTimeoutException e) {
        throw new ServiceUnavailableException(
                "Order service temporarily unavailable"
        );
    }
}
```

In production systems, timeout, retry, circuit breaking, and fallback behavior are usually handled through dedicated infrastructure or resilience mechanisms rather than manually implementing everything inside business logic.

---

## **🏗️ Real Example**

Consider a food-ordering system:

```
Mobile App
    ↓
Load Balancer
    ↓
Order Service
    ├── Inventory Service
    ├── Payment Service
    ├── Database
    └── Notification Service
```

### **Scenario 1 — Backend instance crashes**

```
Order Service A ❌
Order Service B ✅
Order Service C ✅
```

The load balancer can route new requests to B/C.

---

### **Scenario 2 — Inventory service fails**

```
Order Service
      ↓
Inventory Service
      X
```

If inventory confirmation is mandatory before placing an order, the order may need to fail.

---

### **Scenario 3 — Notification service fails**

```
Order Service
      ↓
Order Database
      ↓
Order Created ✅

Notification Service
      X
```

If notification is asynchronous, the order itself may still succeed and notification can be retried separately.

---

### **Scenario 4 — Database fails**

```
Order Service
      ↓
Database
      X
```

If the database is the source of truth and there is no usable alternative, operations requiring it may become unavailable.

---

## **🎯 Interview Questions**

### **Q1. What is a failure point?**

**Answer:**

A component, dependency, or boundary where a failure can interrupt or degrade request processing.

---

### **Q2. What is a Single Point of Failure?**

**Answer:**

A component whose failure can make a critical part of the system unavailable because there is no adequate alternative.

---

### **Q3. If one backend instance fails, must the entire application go down?**

**Answer:**

No.

If multiple healthy instances exist and the load balancer can route traffic to them, the system can continue serving requests.

---

### **Q4. Can a system with multiple backend servers still have a Single Point of Failure?**

**Answer:**

Yes.

For example:

```
Backend A ──┐
Backend B ──┼──→ Database ❌
Backend C ──┘
```

The database may still be a single point of failure.

---

### **Q5. What is a cascading failure?**

**Answer:**

A failure or severe degradation in one component causes dependent components to become overloaded or fail as well.

Example:

```
Service C slows
    ↓
Service B waits
    ↓
Service A waits
    ↓
Resources exhausted
    ↓
More failures
```

---

### **Q6. Why are timeouts important?**

**Answer:**

A timeout prevents the application from waiting indefinitely for a dependency.

Without appropriate timeouts, slow dependencies can hold connections, threads, or other resources for too long and contribute to cascading failures.

---

### **Q7. Why can retries be dangerous?**

**Answer:**

Retries increase traffic toward an already failing dependency.

For example:

```
100 requests
    ↓
Dependency fails
    ↓
Each request retries 3 times
    ↓
300 additional requests
```

An outage can therefore become worse if retries are uncontrolled.

---

### **Q8. When is a fallback useful?**

**Answer:**

A fallback is useful when an alternative source or behavior can safely provide an acceptable result.

Example:

```
Redis
  ↓
FAIL
  ↓
Database
```

But fallback is only appropriate when the alternative is valid for the operation.

---

### **Q9. Does every dependency failure require the entire request to fail?**

**Answer:**

No.

It depends on whether that dependency is **critical to the operation**.

For example, payment may be critical to placing an order, while sending a notification may be handled asynchronously.

---

### **Q10. How would you make a backend resilient to instance failures?**

**Answer:**

Common approaches include:

- Multiple backend instances
- Health checks
- Load balancing
- Automatic replacement/restart
- Avoiding local state where possible
- Appropriate timeouts and retry policies

The exact design depends on the system’s requirements.

---

## **⚠️ Mistakes / Gotchas**

### **1. Redundancy in one layer doesn’t remove all failures**

Three backend servers don’t help if all of them depend on one unavailable database.

---

### **2. Don’t blindly retry everything**

Retries should generally be limited and appropriate to the operation.

A retry can be particularly dangerous for non-idempotent operations if it can cause duplicate effects.

---

### **3. Timeout is not the same as immediate failure**

A timeout means the system waited up to a defined limit without receiving the expected result.

---

### **4. Not every dependency is equally critical**

```
Critical dependency
→ failure may block the operation

Non-critical dependency
→ operation may continue
```

The business requirements determine which category a dependency belongs to.

---

### **5. A healthy server can still be unable to serve requests**

The server itself may be healthy while its critical dependency is unavailable.

```
Backend ✅
   ↓
Database ❌
```

---

### **6. Failure handling is an architectural decision**

There is no universal rule such as:

“Always retry.”

or:

“Always fall back.”

The correct behavior depends on:

- Operation type
- Data consistency requirements
- Dependency characteristics
- User experience
- Business requirements

---

## **🔑 Key Takeaways**

```
Every component can fail.

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
Cache
  ↓
Database
  ↓
External Services
```

Remember:

1. **Failure can occur at any layer.**
2. **Component failure and dependency failure are different.**
3. **Redundancy allows the system to survive individual component failures.**
4. **A Single Point of Failure can bring down a critical part of the system.**
5. **Timeouts prevent indefinite waiting.**
6. **Retries can help with transient failures but can also amplify outages.**
7. **Fallbacks can keep systems available when alternatives exist.**
8. **Cascading failures occur when one failure spreads through dependent components.**
9. **Not every dependency failure must fail the entire request.**
10. **HLD is largely about understanding failure scenarios before they happen.**