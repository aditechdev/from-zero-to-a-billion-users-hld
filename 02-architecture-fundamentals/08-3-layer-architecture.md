# 3-Layer Architecture

![HLD](https://img.shields.io/badge/HLD-Architecture%20Fundamentals-blue)
![3-Layer Architecture](https://img.shields.io/badge/Topic-3--Layer%20Architecture-orange)
![Presentation Layer](https://img.shields.io/badge/Layer-Presentation-green)
![Business Layer](https://img.shields.io/badge/Layer-Business-purple)
![Data Access Layer](https://img.shields.io/badge/Layer-Data%20Access-red)

---

## ❓ Problem

Imagine building a food-ordering backend where a single Controller does everything:

```
OrderController
│
├── Reads HTTP request
├── Validates request
├── Checks restaurant status
├── Checks item availability
├── Calculates price
├── Applies business rules
├── Executes SQL
├── Saves order
└── Builds HTTP response
```

Initially this may work.

But as the application grows, the component accumulates unrelated responsibilities.

Now consider some changes:

```
Change REST API
Change business rule
Change MySQL
Change pricing logic
Change database query
```

If all of these are mixed together, changes become harder to isolate.

### **The core problem**

**How do we organize an application so that different responsibilities are separated and changes in one responsibility do not unnecessarily affect others?**

3-Layer Architecture addresses this by separating the application into logical layers.

---

# **📋 Prerequisites**

Before learning 3-Layer Architecture, understand:

- Client-Server Architecture
- Request / Response
- HTTP Request / Response
- Backend request processing
- Basic database concepts
- Basic understanding of backend application code

---

# **🧠 Theory**

## **1. First Principles**

Before introducing layers, ask:

What does a backend actually need to do when handling a request?

Consider:

```
POST /orders
```

The backend needs to:

```
Receive the request
       ↓
Understand what the client wants
       ↓
Decide what should happen
       ↓
Read/write required data
       ↓
Return a result
```

These responsibilities are fundamentally different.

For example:

```
HTTP handling
     ≠
Business decision
     ≠
Database operation
```

So instead of putting everything together, we create boundaries.

```
Communication
      ↓
Application behavior
      ↓
Data access
```

These become our three layers.

---

## **2. What Problem Does 3-Layer Solve?**

3-Layer Architecture separates an application into:

```
Presentation Layer
        ↓
Application / Business Layer
        ↓
Data Access Layer
```

The fundamental questions are:

```
Presentation
→ How do I communicate?

Business
→ What should happen?

Data Access
→ How do I store/retrieve data?
```

The goal is not to create more classes.

The goal is to create **meaningful responsibility boundaries**.

---

# **3. Presentation Layer**

The Presentation Layer is responsible for communicating with external clients.

For a REST API, this commonly contains Controllers.

### **Typical responsibilities**

```
Presentation Layer
├── Receive HTTP request
├── Read path parameters
├── Read query parameters
├── Read request body
├── Handle HTTP-level validation
├── Call application/business logic
└── Build HTTP response
```

Example:

```
POST /orders

        ↓

OrderController
        ↓
OrderService
```

The Controller should generally not contain the core business rules.

---

# **4. Application / Business Layer**

The Application / Business Layer contains the application’s behavior.

It answers:

**What should happen?**

For a food-ordering system:

```
Place Order
     ↓
Restaurant open?
     ↓
Items available?
     ↓
Calculate total
     ↓
Create order
     ↓
Save order
```

This is different from HTTP handling.

The business logic should not need to know that the request came from:

```
POST /orders
```

It should focus on the application’s behavior.

### **Example**

```
OrderService
    ↓
placeOrder()
    ↓
Check restaurant
    ↓
Check items
    ↓
Calculate price
    ↓
Create order
```

---

# **5. Data Access Layer**

The Data Access Layer communicates with persistent storage.

Typical components:

```
Repository
DAO
ORM data-access code
```

It answers:

**How do I store or retrieve data?**

For example:

```
OrderRepository
       ↓
    MySQL
```

Possible operations:

```
findOrder()
saveOrder()
updateOrder()
deleteOrder()
```

The repository should primarily handle persistence rather than business decisions.

---

# **6. Responsibilities of Each Layer**

| **Layer** | **Primary Responsibility** | **Example** |
| --- | --- | --- |
| Presentation | External communication | Controller |
| Business/Application | Application behavior and business rules | Service |
| Data Access | Persistence | Repository |
| Database | Store data | MySQL |

### **Simple mental model**

```
Presentation
"How do I communicate?"

Business
"What should happen?"

Data Access
"How do I store/retrieve it?"
```

---

# **7. Request Flow Through 3 Layers**

Suppose the client sends:

```
POST /orders
```

The request flows:

```
Client
  ↓
Controller
  ↓
Service
  ↓
Repository
  ↓
Database
```

The response travels back:

```
Database
  ↓
Repository
  ↓
Service
  ↓
Controller
  ↓
Client
```

### **Important**

The request itself is not necessarily one operation.

One external request may cause several internal operations:

```
POST /orders
      ↓
Check restaurant
      ↓
Check inventory
      ↓
Calculate price
      ↓
Create order
      ↓
Save order
```

So:

```
External Request ≠ Internal Operation
```

---

# **8. Separation of Concerns**

**Separation of Concerns** means keeping different responsibilities separated so each part focuses on a meaningful concern.

For example:

```
Controller
→ HTTP communication

Service
→ Application behavior

Repository
→ Database interaction
```

Without separation:

```
Controller
├── HTTP
├── Business logic
├── SQL
├── Pricing
└── Inventory
```

With separation:

```
Controller
    ↓
Service
    ↓
Repository
```

Each component has a clearer responsibility.

---

# **9. Presentation vs Business Logic**

These two are often confused.

### **Presentation logic**

Deals with communication:

```
HTTP method
URL
headers
query parameters
request body
HTTP status code
response format
```

### **Business logic**

Deals with application rules:

```
Restaurant must be open
Order cannot exceed stock
Coupon cannot be used after expiry
Customer cannot cancel after shipment
```

### **Example**

```
POST /orders
        ↓
Presentation
"How did the client communicate?"
        ↓
Business
"Can this order actually be placed?"
```

A useful test:

If I changed REST to another communication mechanism, would this rule still exist?

If yes, it is likely business logic.

---

# **10. Business Logic vs Data Access**

These are also different concerns.

### **Business Logic**

```
Can customer cancel this order?
```

### **Data Access**

```
Find order ORD123.
Save updated order.
```

Business decides **what should happen**.

Data Access handles **how data is persisted/retrieved**.

Example:

```
OrderService
    ↓
"Is cancellation allowed?"
    ↓
OrderRepository
    ↓
"Find this order"
    ↓
Database
```

The Repository should not normally decide:

```
"Customer cannot cancel because the order has shipped."
```

That is a business rule.

---

# **11. Dependency Direction**

The typical dependency direction is:

```
Presentation
      ↓
Business
      ↓
Data Access
      ↓
Database
```

Example:

```
OrderController
      ↓
OrderService
      ↓
OrderRepository
      ↓
MySQL
```

The Controller should generally not bypass the Service:

```
❌

Controller
    ↓
Repository
```

Prefer:

```
✅

Controller
    ↓
Service
    ↓
Repository
```

### **Important distinction: Dependency Direction vs Dependency Injection**

These are not the same thing.

**Dependency direction** answers:

Who depends on whom?

**Dependency Injection (DI)** answers:

How is that dependency provided to the component?

For example, Spring can inject an `OrderService` into an `OrderController`.

DI helps provide dependencies, but DI itself does not automatically guarantee good architecture.

---

# **12. Coupling**

**Coupling** describes how strongly one component depends on another.

Consider:

```
Controller
    ↓
Service
    ↓
Repository
```

The components have dependencies, but the goal is to avoid **unnecessary or overly strong dependencies**.

### **Example of strong coupling**

```
Controller
    ↓
Direct SQL
```

Now the Controller knows database implementation details.

Changing the persistence mechanism may require changes in the Controller.

### **Better separation**

```
Controller
    ↓
Service
    ↓
Repository
```

The Controller does not need to know how the database is implemented.

---

# **13. Loose Coupling**

**Loose coupling** means components have limited knowledge of each other’s implementation details and can change with less impact on each other.

For example:

```
Controller
      ↓
OrderService
```

The Controller needs to know:

```
placeOrder(...)
```

It does not need to know every internal step:

```
Check restaurant
Check inventory
Calculate price
Create order
Save order
```

The Service hides those implementation details.

### **Important**

Loose coupling does **not** mean:

```
Zero dependencies
```

Some dependencies are necessary.

The goal is:

**Minimize unnecessary dependencies and implementation knowledge.**

---

# **14. Cohesion**

**Cohesion** describes how closely related the responsibilities inside a component are.

High cohesion is generally desirable.

### **High cohesion**

```
OrderRepository
├── findOrder()
├── saveOrder()
└── updateOrder()
```

These operations are closely related.

### **Low cohesion**

```
OrderController
├── HTTP handling
├── SQL
├── Payment processing
├── Email sending
└── Inventory calculation
```

Unrelated responsibilities are mixed together.

### **Remember**

```
Coupling
→ Between components

Cohesion
→ Within a component
```

A useful target is:

```
Lower unnecessary coupling
+
Higher cohesion
```

---

# **15. Layer Violations**

A layer violation occurs when a component starts taking responsibilities that belong elsewhere or bypasses an intended boundary.

### **Violation 1 — Controller contains business logic**

```
❌

Controller
    ↓
if restaurantClosed
    rejectOrder()
```

Prefer:

```
Controller
    ↓
OrderService
    ↓
Business rule
```

---

### **Violation 2 — Controller directly accesses database**

```
❌

Controller
    ↓
SQL
```

Prefer:

```
Controller
    ↓
Service
    ↓
Repository
    ↓
Database
```

---

### **Violation 3 — Repository contains business rules**

```
❌

Repository
    ↓
if orderAlreadyShipped
    rejectCancellation()
```

The repository should primarily deal with data access.

---

### **Important nuance**

Not every dependency crossing a layer is automatically bad.

The objective is **meaningful separation**, not blindly enforcing a rigid hierarchy.

---

# **16. Change Isolation**

One major reason for layering is to isolate changes.

### **Example 1 — REST → GraphQL**

If the external communication mechanism changes:

```
REST
 ↓
GraphQL
```

the change should primarily affect the Presentation Layer.

---

### **Example 2 — MySQL → PostgreSQL**

```
MySQL
 ↓
PostgreSQL
```

The change should ideally be concentrated in the Data Access Layer.

---

### **Example 3 — Business rule changes**

Suppose:

```
Old:
Customer can cancel before preparation.
```

becomes:

```
New:
Customer can cancel only before restaurant accepts the order.
```

The change belongs primarily to the Business/Application Layer.

### **Core idea**

```
Change
   ↓
Relevant Layer
   ↓
Limited impact on unrelated layers
```

---

# **17. Why Separate the Layers?**

There are several practical reasons.

### **1. Separation of responsibilities**

Each component has a clearer purpose.

### **2. Change isolation**

Changes can be concentrated in the relevant area.

### **3. Maintainability**

Developers can more easily find where functionality belongs.

### **4. Testability**

Different parts can be tested at different levels.

For example, a business rule can be tested independently using a mocked repository.

A database interaction can be tested separately using an integration test.

The architecture does **not** mean every test must avoid the database.

### **5. Team development**

Different developers can work on different responsibilities with clearer boundaries.

### **6. Easier evolution**

The application can change without every change affecting the entire codebase.

### **Trade-off**

More separation also introduces:

```
More classes
More interfaces
More indirection
More code structure
```

Therefore:

**Use enough separation to create useful boundaries, not maximum separation.**

A tiny CRUD application may not need a complicated architecture.

---

# **18. 3-Layer vs Client-Server**

These concepts describe different things.

### **Client-Server**

Describes **communication between systems**:

```
Client
   ↕
Server
```

Example:

```
Mobile App
   ↓ HTTP
Backend
```

### **3-Layer**

Describes the **internal logical organization of the backend**:

```
Presentation
     ↓
Business
     ↓
Data Access
```

They can coexist:

```
             Client
                │
               HTTP
                ↓
        ┌─────────────────┐
        │    Backend      │
        │                 │
        │ Controller      │
        │      ↓          │
        │ Service         │
        │      ↓          │
        │ Repository      │
        └────────┬────────┘
                 ↓
              Database
```

### **Key distinction**

```
Client-Server
→ Who communicates?

3-Layer
→ How is the backend organized?
```

The Client is **not** one of the three application layers.

---

# **19. 3-Layer vs 3-Tier**

These terms are often used inconsistently in the industry.

For our HLD terminology:

### **3-Layer**

Primarily refers to logical separation:

```
Presentation
     ↓
Business/Application
     ↓
Data Access
```

### **3-Tier**

Traditionally emphasizes separate runtime/deployment boundaries:

```
Presentation Tier
       ↓
Application Tier
       ↓
Data Tier
```

Conceptually:

```
Layer
→ Logical responsibility

Tier
→ Runtime/deployment boundary
```

However, real-world terminology is not perfectly consistent.

A developer may call:

```
Controller
Service
Repository
```

a “3-tier architecture” even when all three exist inside one application.

Therefore, don’t rely only on the name.

Ask:

**Are we talking about logical responsibility or physical/runtime separation?**

---

# **20. Physical Deployment vs Logical Layers**

This distinction is critical.

### **Logical architecture**

A single application can contain:

```
Spring Boot Application
│
├── Presentation
├── Business
└── Data Access
```

All three layers can run in the same process.

Therefore:

```
3 Layers ≠ 3 Servers
```

### **Physical deployment**

A typical backend may instead look like:

```
                 Load Balancer
                 /     |     \
                ↓      ↓      ↓
              App 1  App 2  App 3
                │      │      │
                └──────┼──────┘
                       ↓
                    MySQL
```

Each application instance contains:

```
Controller
Service
Repository
```

### **Why not deploy each layer separately?**

You could technically create:

```
Controller Server
       ↓
Business Server
       ↓
Repository Server
       ↓
Database
```

But this introduces network communication between layers:

```
serialization
network latency
timeouts
network failures
deployment complexity
```

There should be a concrete reason before introducing such boundaries.

### **Core principle**

**A logical boundary does not automatically require a deployment boundary.**

---

# **21. Scaling Individual Tiers**

Suppose traffic increases.

First identify where the bottleneck is.

### **Case 1 — Application is overloaded**

```
Backend CPU → 95%
Database CPU → 30%
```

Adding backend replicas may help:

```
                 Load Balancer
                /      |      \
               ↓       ↓       ↓
             App 1   App 2   App 3
```

---

### **Case 2 — Database is overloaded**

```
Backend CPU → 25%
Database CPU → 95%
```

Adding more backend replicas does not automatically solve the database bottleneck.

The database or the way it is being accessed needs investigation.

---

### **Important principle**

**Scale the bottleneck, not everything.**

---

### **Does scaling mean decomposition?**

No.

Running:

```
App 1
App 2
App 3
```

means you have multiple replicas of the application.

It does not mean:

```
Controller Service
Business Service
Repository Service
```

were created.

These are different concepts.

```
Scaling
→ Increase capacity

Decomposition
→ Split an application into separate parts
```

---

# **🗺️ Diagram**

## **Complete Logical Architecture**

```
                         CLIENT
                            │
                            │ HTTP
                            ▼
                 ┌─────────────────────┐
                 │  PRESENTATION       │
                 │                     │
                 │  Controller         │
                 │                     │
                 │  "How communicate?" │
                 └──────────┬──────────┘
                            │
                            ▼
                 ┌─────────────────────┐
                 │  BUSINESS /         │
                 │  APPLICATION        │
                 │                     │
                 │  Service            │
                 │                     │
                 │  "What happens?"    │
                 └──────────┬──────────┘
                            │
                            ▼
                 ┌─────────────────────┐
                 │  DATA ACCESS        │
                 │                     │
                 │  Repository         │
                 │                     │
                 │  "How store?"       │
                 └──────────┬──────────┘
                            │
                            ▼
                       ┌──────────┐
                       │ Database │
                       └──────────┘
```

## **Logical vs Physical**

```
LOGICAL
──────────────────────────────

Application
│
├── Presentation
├── Business
└── Data Access

PHYSICAL
──────────────────────────────

             Load Balancer
             /     |     \
            ↓      ↓      ↓
          App 1  App 2  App 3
            │      │      │
            └──────┼──────┘
                   ↓
                Database
```

The logical layers can exist inside every application replica.

---

# **💻 Code**

## **Example Spring Boot Structure**

```
src/main/java/com/example/orders/

├── controller/
│   └── OrderController.java
│
├── service/
│   └── OrderService.java
│
└── repository/
    └── OrderRepository.java
```

### **Controller**

```
@RestController
class OrderController {

    private final OrderService orderService;

    OrderController(OrderService orderService) {
        this.orderService = orderService;
    }

    @PostMapping("/orders")
    public OrderResponse placeOrder(
            @RequestBody CreateOrderRequest request) {

        return orderService.placeOrder(request);
    }
}
```

The Controller handles the HTTP boundary and delegates application work.

---

### **Service**

```
@Service
class OrderService {

    private final OrderRepository orderRepository;

    OrderService(OrderRepository orderRepository) {
        this.orderRepository = orderRepository;
    }

    public OrderResponse placeOrder(CreateOrderRequest request) {

        // Business/application logic

        return orderRepository.save(request);
    }
}
```

The Service coordinates the application behavior.

---

### **Repository**

```
@Repository
class OrderRepository {

    public OrderResponse save(CreateOrderRequest request) {

        // Database interaction

        return new OrderResponse();
    }
}
```

The Repository handles persistence.

The code is intentionally simplified. The important thing is the **responsibility boundary**, not the exact Spring implementation.

---

# **🏗️ Real Example**

## **Food Ordering System**

Customer clicks:

**Place Order**

### **Complete flow**

```
Mobile App
    │
    │ POST /orders
    ▼
OrderController
    │
    │ placeOrder()
    ▼
OrderService
    │
    ├── Is restaurant open?
    ├── Are items available?
    ├── Calculate total
    ├── Create order
    │
    ▼
OrderRepository
    │
    │ INSERT
    ▼
MySQL
```

Response:

```
MySQL
  ↓
OrderRepository
  ↓
OrderService
  ↓
OrderController
  ↓
Mobile App
```

Example response:

```
HTTP/1.1 201 Created
Content-Type: application/json

{
  "orderId": "ORD123",
  "status": "PLACED"
}
```

---

## **Change Scenario**

Suppose the company changes from:

```
MySQL
```

to:

```
PostgreSQL
```

Ideally:

```
Controller
    ↓
Service
    ↓
Repository  ← major change here
    ↓
PostgreSQL
```

The Controller should not need to know that the database changed.

Similarly, if the business rule changes:

```
Restaurant must be open
```

to:

```
Restaurant can accept orders until 11 PM
```

the primary change belongs in the Business/Application area.

---

# **🎯 Interview Questions**

## **Q1. What is 3-Layer Architecture?**

**Answer:**

A logical architecture that separates an application into:

```
Presentation
Business/Application
Data Access
```

Each layer has a distinct responsibility.

---

## **Q2. Why shouldn’t a Controller directly execute SQL?**

**Answer:**

Because the Controller should focus on external communication, while database access belongs to the Data Access Layer.

Direct SQL would mix responsibilities and increase coupling between the HTTP layer and persistence implementation.

---

## **Q3. Where should the rule “restaurant must be open before placing an order” live?**

**Answer:**

Primarily in the Business/Application Layer because it is a business rule.

---

## **Q4. What is the difference between Presentation Logic and Business Logic?**

**Answer:**

Presentation logic handles communication details such as HTTP requests, parameters, headers, and response formatting.

Business logic determines what the application should do according to business rules.

---

## **Q5. What is dependency direction in 3-Layer Architecture?**

**Answer:**

The typical direction is:

```
Presentation
    ↓
Business
    ↓
Data Access
    ↓
Database
```

For example:

```
Controller
    ↓
Service
    ↓
Repository
```

---

## **Q6. What is coupling?**

**Answer:**

Coupling describes the strength of dependency between components.

Good architecture generally tries to reduce unnecessary coupling.

---

## **Q7. What is loose coupling?**

**Answer:**

Loose coupling means components have limited dependency on each other’s implementation details, making them easier to change independently.

It does **not** mean having zero dependencies.

---

## **Q8. What is cohesion?**

**Answer:**

Cohesion describes how closely related the responsibilities within a component are.

Generally:

```
High cohesion → desirable
```

Remember:

```
Coupling → between components
Cohesion → within a component
```

---

## **Q9. Is Controller → Repository always a layer violation?**

**Answer:**

In the conventional 3-layer structure we’re learning, bypassing the Business/Application Layer is generally discouraged because it weakens the intended responsibility boundary.

However, architecture is context-dependent; a simple read-only operation may not justify unnecessary layers.

---

## **Q10. Does 3-Layer Architecture require three servers?**

**Answer:**

No.

Layers are logical boundaries and can exist inside one application process.

```
One Spring Boot Application
├── Controller
├── Service
└── Repository
```

---

## **Q11. What is the difference between 3-Layer and Client-Server Architecture?**

**Answer:**

Client-Server describes communication:

```
Client ↔ Server
```

3-Layer describes the internal organization of the application:

```
Presentation
     ↓
Business
     ↓
Data Access
```

---

## **Q12. What is the difference between a layer and a tier?**

**Answer:**

A layer primarily represents a logical responsibility boundary.

A tier commonly refers to a runtime/deployment boundary.

However, the terms are used inconsistently in industry, so the actual architecture matters more than the label.

---

## **Q13. Can all three layers run in one application?**

**Answer:**

Yes.

For example:

```
Spring Boot
├── Controller
├── Service
└── Repository
```

can run as one deployable application.

---

## **Q14. If we run five copies of the application, have we created five architectures?**

**Answer:**

No.

We have five application replicas.

Each replica can contain the same three logical layers.

---

## **Q15. If database CPU is 95% but application CPU is 20%, should we immediately add more application instances?**

**Answer:**

No.

The database appears to be the current bottleneck, so adding application replicas may not address the underlying problem.

The database and its workload should be investigated.

---

## **Q16. What is the difference between scaling and decomposition?**

**Answer:**

Scaling increases capacity:

```
App → App + App + App
```

Decomposition splits an application into separate parts:

```
Application
   ↓
Orders
Payments
Inventory
```

They are different architectural decisions.

---

# **⚠️ Mistakes / Gotchas**

### **1. ❌ “3-Layer means 3 servers”**

```
3 Layers ≠ 3 Servers
```

---

### **2. ❌ “Controller should contain business logic”**

Controller should primarily handle the communication boundary.

---

### **3. ❌ “Repository should decide business rules”**

Repository should primarily handle persistence.

---

### **4. ❌ “Loose coupling means no dependencies”**

Dependencies are normal.

The goal is to avoid **unnecessary implementation coupling**.

---

### **5. ❌ “More layers always means better architecture”**

More abstraction can also mean:

```
More complexity
More indirection
More code
```

Use layers when they provide useful separation.

---

### **6. ❌ “Every layer should be a microservice”**

No.

A single application can contain all three layers.

---

### **7. ❌ “Scaling replicas means decomposition”**

It doesn’t.

```
Scaling
→ More capacity

Decomposition
→ Separate application parts
```

---

### **8. ❌ “3-Layer and Client-Server are competing architectures”**

They describe different dimensions.

```
Client-Server
→ Communication structure

3-Layer
→ Internal application structure
```

They can exist together.

---

### **9. ❌ Treating terminology as more important than architecture**

“3-layer” and “3-tier” are sometimes used interchangeably.

Always determine whether someone is discussing:

```
Logical responsibility
```

or:

```
Physical/runtime deployment
```

---

### **10. ❌ Assuming every change must stay completely inside one layer**

Real systems are not perfectly isolated.

A change can legitimately cross boundaries.

The goal is to **minimize unnecessary impact**, not guarantee zero impact.

---

# **🔑 Key Takeaways**

### **The three layers**

```
Presentation
     ↓
Business/Application
     ↓
Data Access
     ↓
Database
```

### **Their responsibilities**

```
Presentation
→ Communication

Business
→ Application behavior / business rules

Data Access
→ Persistence
```

### **Dependency direction**

```
Controller
    ↓
Service
    ↓
Repository
    ↓
Database
```

### **Two architectural qualities**

```
Coupling
→ Keep unnecessary dependencies low

Cohesion
→ Keep related responsibilities together
```

### **Change isolation**

```
REST → GraphQL
        ↓
Presentation

MySQL → PostgreSQL
        ↓
Data Access

Business rule changes
        ↓
Business/Application
```

### **Architecture vs deployment**

```
Layer
→ Logical responsibility

Tier
→ Often runtime/deployment boundary
```

But industry terminology varies.

### **Scaling**

```
Load Balancer
   ↓
App 1
App 2
App 3
   ↓
Database
```

Multiple replicas do not automatically mean decomposition.

---

## **🧠 Final Mental Model**

When you see a backend request, think:

```
                CLIENT
                   │
                   │ HTTP
                   ▼
          ┌─────────────────┐
          │  PRESENTATION   │
          │                 │
          │ "How?"          │
          └────────┬────────┘
                   │
                   ▼
          ┌─────────────────┐
          │    BUSINESS     │
          │                 │
          │ "What?"         │
          └────────┬────────┘
                   │
                   ▼
          ┌─────────────────┐
          │   DATA ACCESS   │
          │                 │
          │ "How stored?"   │
          └────────┬────────┘
                   │
                   ▼
               DATABASE
```

And remember:

**3-Layer Architecture is primarily about separating responsibilities inside an application. It does not, by itself, dictate how many servers, processes, or services you deploy.**

---

# **📚 References**

- Martin Fowler — *Patterns of Enterprise Application Architecture*
- Robert C. Martin — *Clean Architecture*
- Eric Evans — *Domain-Driven Design*
- Spring Framework documentation — layered application development