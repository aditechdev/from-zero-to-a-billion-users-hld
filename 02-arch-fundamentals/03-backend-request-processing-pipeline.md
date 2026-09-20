# Backend Request Processing Pipeline

![HLD](https://img.shields.io/badge/HLD-Architecture%20Fundamentals-blue)
![Pipeline](https://img.shields.io/badge/Pipeline-Request%20Processing-orange)
![Validation](https://img.shields.io/badge/Step-Validation-green)
![Authentication](https://img.shields.io/badge/Step-Authentication-purple)
![Authorization](https://img.shields.io/badge/Step-Authorization-red)
![Business Logic](https://img.shields.io/badge/Step-Business%20Logic-yellow)

---

## **❓ Problem**

When a backend receives an HTTP request, it cannot simply execute the requested operation immediately.

Consider:

```
POST /orders
```

The backend needs to determine:

- Is the request understandable?
- Is the input valid?
- Who is making the request?
- Is this user allowed to perform the operation?
- Does the requested operation follow business rules?
- Does the backend need data from a database or another service?
- What result should be returned to the client?
- What should happen if something fails?

Without a structured processing flow, backend systems become difficult to secure, maintain, debug, and scale.

The **Backend Request Processing Pipeline** provides a mental model for how a backend processes a request from reception to response.

---

## **📋 Prerequisites**

Before learning this topic, you should understand:

- Client-Server Architecture
- Request / Response Flow
- HTTP fundamentals
- HTTP Request
- HTTP Response
- HTTP Methods
- HTTP Headers
- HTTP Status Codes
- Request Anatomy
- Response Anatomy

---

## **🧠 Theory**

### **1. What Is a Backend Request Processing Pipeline?**

A backend request processing pipeline is the sequence of responsibilities a backend performs while processing an incoming request.

A simplified model is:

```
HTTP Request
     ↓
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
Database / External Services
     ↓
Build Response
     ↓
HTTP Response
```

This is a **mental model**, not a mandatory implementation order.

Real applications may:

- Combine stages
- Reorder stages
- Add additional stages
- Perform some stages in middleware
- Perform cache checks before database access
- Call multiple services

The goal is to understand the **responsibilities**, not memorize a fixed sequence.

---

### **2. Request Reception**

The first responsibility is receiving and interpreting the incoming HTTP request.

For example:

```
POST /orders?coupon=SAVE20
Authorization: Bearer abc123
Content-Type: application/json

{
  "items": [
    {
      "itemId": "P100",
      "quantity": 2
    }
  ]
}
```

The backend needs to understand:

```
Method
  ↓
POST

Path
  ↓
/orders

Query Parameters
  ↓
coupon=SAVE20

Headers
  ↓
Authorization
Content-Type

Body
  ↓
JSON payload
```

The backend cannot process the request until it understands what was received.

---

### **3. Validation**

Validation determines whether the request is structurally and syntactically acceptable.

Example:

```
{
  "quantity": -5
}
```

If the API requires:

```
quantity > 0
```

the request should be rejected during validation.

Examples of validation:

- Required field is missing
- Invalid data type
- Invalid email format
- Invalid UUID
- Negative quantity where positive quantity is required
- Malformed JSON
- Invalid parameter format

Mental model:

```
Validation
    ↓
"Is this input/request acceptable?"
```

Validation is primarily about the **shape and basic correctness of the input**.

---

### **4. Authentication**

Authentication answers:

**Who are you?**

For example:

```
Authorization: Bearer abc123
```

The backend may verify the token and determine:

```
User ID = 12345
```

If the credentials are missing or invalid, the backend may reject the request.

Mental model:

```
Authentication
       ↓
"Who are you?"
```

---

### **5. Authorization**

Authorization answers:

**Are you allowed to perform this operation?**

Suppose:

```
User = Normal User

Request:
DELETE /admin/users/123
```

The user may be authenticated successfully.

However:

```
Authenticated?     YES
Authorized?        NO
```

Therefore, the backend rejects the operation.

Mental model:

```
Authorization
       ↓
"Are you allowed to do this?"
```

---

### **6. Business Logic / Business Rules**

Even if a request is:

- Valid
- Authenticated
- Authorized

the operation may still be invalid according to the application’s domain rules.

Example:

```
User:
Buy 10 items

Authentication:
✓

Authorization:
✓

Validation:
✓

Inventory:
3 items
```

The operation should still be rejected.

Why?

```
Requested quantity = 10
Available quantity = 3
```

This is a **business rule**.

Other examples:

```
Minimum order amount = ₹2,000
Order amount = ₹500
```

```
Transfer amount = ₹10,000
Account balance = ₹5,000
```

```
Restaurant closed
User tries to place order
```

Mental model:

```
Business Rules
       ↓
"Is this operation valid according to our domain?"
```

---

### **7. Database / External Service Interaction**

Once the request passes the necessary checks, the backend may need to interact with other systems.

For example:

```
Backend
   │
   ├──→ Redis
   │
   ├──→ Database
   │
   ├──→ Inventory Service
   │
   └──→ Payment Service
```

A simple request might only require:

```
Backend → Database
```

A more complex request might require multiple operations.

For example, placing an order could involve:

```
Backend
   │
   ├── Check inventory
   ├── Calculate price
   ├── Create order
   ├── Process payment
   └── Save transaction
```

The backend acts as the coordinator between the client and the systems that own the required data or capabilities.

---

### **8. Business Logic vs Data Access**

These responsibilities should be conceptually separated.

```
HTTP Layer
     ↓
Business Logic
     ↓
Data Access
     ↓
Database
```

For example:

```
HTTP Layer:
"POST /orders received."

Business Logic:
"Can this order be placed?"

Data Access:
"Read inventory and save order."

Database:
"Store the data."
```

This separation becomes important later when learning:

- 3-Tier Architecture
- Layered Architecture
- Clean Architecture
- SOLID
- LLD

---

### **9. Building the Response**

After processing the operation, the backend needs to construct an HTTP response.

Suppose the operation succeeds:

```
Business Result:

Order created
Order ID = ORD123
```

The backend can represent that result as:

```
HTTP/1.1 201 Created
Content-Type: application/json

{
  "orderId": "ORD123",
  "status": "PLACED"
}
```

The response contains:

```
HTTP Response
│
├── Status
│     └── 201 Created
│
├── Headers
│     └── Content-Type
│
└── Body
      └── Order information
```

Important distinction:

```
Business Result
      ↓
"Order ORD123 was created"

HTTP Response
      ↓
201 Created
+ Headers
+ Body
```

The business result is represented **inside** the HTTP response.

---

### **10. The Pipeline Is Not Always Linear**

The simplified model:

```
Request
   ↓
Validation
   ↓
Authentication
   ↓
Authorization
   ↓
Business Logic
   ↓
Database
   ↓
Response
```

is useful for understanding.

Real systems may look like:

```
Request
   ↓
Backend
   ↓
Cache?
 ┌─┴─────┐
YES      NO
 ↓        ↓
Response  Database
            ↓
          Business Logic
            ↓
          Response
```

Or:

```
Backend
   │
   ├──→ Redis
   ├──→ Database
   ├──→ Payment Service
   └──→ Inventory Service
```

The actual architecture depends on the system.

---

### **11. One External Request Can Trigger Multiple Operations**

A client may send:

```
POST /orders
```

But the backend could perform:

```
Authenticate user
       ↓
Check restaurant
       ↓
Check products
       ↓
Check inventory
       ↓
Calculate price
       ↓
Create order
       ↓
Process payment
       ↓
Save transaction
```

Therefore:

```
Request ≠ Operation
```

One external request can cause many internal operations.

This distinction becomes increasingly important in distributed systems.

---

### **12. Failure Can Happen at Any Stage**

Every stage can potentially fail.

```
HTTP Request
     ↓
Request Reception       ← Failure possible
     ↓
Validation              ← Failure possible
     ↓
Authentication          ← Failure possible
     ↓
Authorization           ← Failure possible
     ↓
Business Logic          ← Failure possible
     ↓
Database / Services     ← Failure possible
     ↓
Build Response          ← Failure possible
     ↓
HTTP Response
```

Example:

```
Backend
   ↓
Database
   X
Timeout
```

The backend cannot wait indefinitely.

It may eventually return an appropriate error response.

This leads to the next HLD topics:

- Failure Points
- Timeouts
- Reliability
- Retries
- Idempotency
- Resilience

---

### **13. The Core Mental Model**

Remember the pipeline as responsibilities:

```
REQUEST
   ↓
UNDERSTAND
   ↓
VALIDATE
   ↓
AUTHENTICATE
   ↓
AUTHORIZE
   ↓
APPLY BUSINESS RULES
   ↓
READ / WRITE DATA
   ↓
BUILD RESULT
   ↓
RESPONSE
```

Do not memorize the exact implementation.

Instead ask:

**What responsibility does the backend need to perform at this point?**

---

## **🗺️ Diagram**

### **Complete Backend Request Processing Pipeline**

```
                         HTTP REQUEST
                              │
                              ▼
                    ┌──────────────────┐
                    │ Request Reception │
                    │                  │
                    │ Parse HTTP       │
                    │ Method           │
                    │ Path             │
                    │ Headers          │
                    │ Query            │
                    │ Body             │
                    └────────┬─────────┘
                             │
                             ▼
                    ┌──────────────────┐
                    │    Validation    │
                    │                  │
                    │ Is input valid?  │
                    └────────┬─────────┘
                             │
                             ▼
                    ┌──────────────────┐
                    │ Authentication   │
                    │                  │
                    │ Who are you?     │
                    └────────┬─────────┘
                             │
                             ▼
                    ┌──────────────────┐
                    │  Authorization   │
                    │                  │
                    │ Allowed to do it?│
                    └────────┬─────────┘
                             │
                             ▼
                    ┌──────────────────┐
                    │ Business Logic   │
                    │                  │
                    │ Is operation     │
                    │ valid?           │
                    └────────┬─────────┘
                             │
                             ▼
                ┌──────────────────────────┐
                │ Database / External      │
                │ Services                 │
                │                          │
                │ DB / Redis / APIs /      │
                │ Other Services           │
                └────────────┬─────────────┘
                             │
                             ▼
                    ┌──────────────────┐
                    │  Build Response  │
                    │                  │
                    │ Status           │
                    │ Headers          │
                    │ Body             │
                    └────────┬─────────┘
                             │
                             ▼
                       HTTP RESPONSE
                             │
                             ▼
                          CLIENT
```

### **Real-World Distributed Example**

```
                         Mobile App
                             │
                             │ POST /orders
                             ▼
                       Order Backend
                             │
              ┌──────────────┼──────────────┐
              │              │              │
              ▼              ▼              ▼
            Redis        Order DB      Inventory Service
                                            │
                                            ▼
                                      Inventory DB
                             │
                             ▼
                      Payment Service
                             │
                             ▼
                       Payment DB
                             │
                             ▼
                       Order Backend
                             │
                             ▼
                         Response
                             │
                             ▼
                        Mobile App
```

---

## **💻 Code**

A simplified Java/Spring Boot example:

```
@PostMapping("/orders")
public ResponseEntity<OrderResponse> createOrder(
        @RequestBody CreateOrderRequest request,
        Authentication authentication) {

    // 1. Validation
    validate(request);

    // 2. Authentication
    User user = getAuthenticatedUser(authentication);

    // 3. Authorization
    checkPermission(user, "CREATE_ORDER");

    // 4. Business Logic
    Order order = orderService.createOrder(user, request);

    // 5. Build Response
    OrderResponse response = new OrderResponse(
            order.getId(),
            order.getStatus()
    );

    return ResponseEntity
            .status(HttpStatus.CREATED)
            .body(response);
}
```

The actual implementation would usually distribute these responsibilities across controllers, filters, security configuration, services, repositories, and other components.

The important part is understanding the **responsibilities**, not copying this exact structure.

---

## **🏗️ Real Example**

### **Food Ordering System**

Suppose a user taps:

**Place Order**

The client sends:

```
POST /orders
Authorization: Bearer abc123
Content-Type: application/json

{
  "restaurantId": "R100",
  "items": [
    {
      "itemId": "P100",
      "quantity": 2
    }
  ]
}
```

### **Step 1 — Reception**

Backend receives:

```
POST /orders
```

and parses the request.

### **Step 2 — Validation**

Check:

```
restaurantId exists?
itemId exists?
quantity is positive?
request format valid?
```

### **Step 3 — Authentication**

Verify:

```
Bearer abc123
       ↓
User = U500
```

### **Step 4 — Authorization**

Check:

```
Can U500 place orders?
```

### **Step 5 — Business Rules**

Check:

```
Restaurant open?
Item available?
Quantity allowed?
Minimum order amount satisfied?
Current price valid?
```

### **Step 6 — Data / Service Interaction**

Potentially:

```
Order Service
     │
     ├──→ Restaurant DB
     ├──→ Inventory Service
     ├──→ Pricing Service
     └──→ Order DB
```

### **Step 7 — Build Response**

If successful:

```
201 Created
```

```
{
  "orderId": "ORD123",
  "status": "PLACED"
}
```

### **Complete Flow**

```
User
 ↓
Mobile App
 ↓
HTTP Request
 ↓
Backend
 ↓
Reception
 ↓
Validation
 ↓
Authentication
 ↓
Authorization
 ↓
Business Rules
 ↓
Database / Services
 ↓
Order Created
 ↓
HTTP Response
 ↓
Mobile App
 ↓
User sees "Order Placed"
```

---

## **🎯 Interview Questions**

### **1. What is a backend request processing pipeline?**

**Answer:**
 It is a conceptual sequence of responsibilities performed by a backend while processing an incoming request, including request reception, validation, authentication, authorization, business logic, data/service interaction, and response construction.

---

### **2. What is the difference between validation and business logic?**

**Answer:**

**Validation** checks whether the input is structurally/basic-correct.

```
quantity = -5
```

**Business logic** checks whether the requested operation is valid according to domain rules.

```
quantity = 10
inventory = 3
```

Both may reject a request, but for different reasons.

---

### **3. What is the difference between authentication and authorization?**

**Answer:**

```
Authentication → Who are you?
Authorization  → Are you allowed to do this?
```

---

### **4. Can a request be authenticated but still rejected?**

**Answer:**
 Yes.

Example:

```
User authenticated ✓
User authorized to access normal APIs ✓
User requests admin operation ✗
```

The authorization check can reject the request.

---

### **5. Can a request pass validation, authentication, and authorization but still fail?**

**Answer:**
 Yes.

For example:

```
Validation       ✓
Authentication   ✓
Authorization   ✓
Business Rules   ✗
```

The user may try to purchase more items than are available.

Or:

```
Business Rules   ✓
Database         ✗
```

The database may be unavailable or time out.

---

### **6. Can one HTTP request trigger multiple internal operations?**

**Answer:**
 Yes.

For example:

```
POST /orders
```

may trigger:

```
Check inventory
Calculate price
Create order
Process payment
Save transaction
```

Therefore, one external request can correspond to multiple internal operations.

---

### **7. Is the pipeline always executed in exactly this order?**

**Answer:**
 No.

The diagram is a conceptual model. Real systems can reorder, combine, or add stages depending on the architecture and framework.

---

### **8. What happens if the database is unavailable?**

**Answer:**
 The database interaction fails. The backend must handle the failure, typically using mechanisms such as timeouts and appropriate error handling, and return an appropriate response if the request cannot be completed.

---

### **9. Why shouldn’t business logic be placed entirely in the client?**

**Answer:**
 The client is not authoritative or trustworthy. It can be modified, bypassed, or replaced by another client.

Critical business rules should therefore be enforced on the backend.

---

### **10. What is the difference between a business result and an HTTP response?**

**Answer:**

Business result:

```
Order created
Order ID = ORD123
```

HTTP response:

```
201 Created
Content-Type: application/json

{
  "orderId": "ORD123",
  "status": "PLACED"
}
```

The HTTP response is the protocol-level representation of the result sent back to the client.

---

## **⚠️ Mistakes / Gotchas**

### **1. Thinking the pipeline is a fixed sequence**

Don’t assume every backend literally executes:

```
Validation → Authentication → Authorization → ...
```

in exactly this order.

The diagram is a conceptual model.

---

### **2. Confusing authentication with authorization**

Incorrect:

```
Authentication = Permission
```

Correct:

```
Authentication = Identity
Authorization  = Permission
```

---

### **3. Treating business rules as validation**

These are different:

```
quantity = "abc"
→ Validation
```

```
quantity = 10
inventory = 3
→ Business Rule
```

---

### **4. Trusting client-side validation**

Client-side validation improves UX.

It does **not** replace server-side validation and business-rule enforcement.

```
Client validation
       ↓
UX protection

Server validation
       ↓
Actual enforcement
```

---

### **5. Assuming every request requires a database**

Not necessarily.

A backend may:

```
Request
   ↓
Redis Cache
   ↓
Response
```

without querying the database.

Some endpoints may also be completely stateless or call another service instead.

---

### **6. Assuming one request equals one operation**

```
HTTP Request
      ≠
Internal Operation
```

A single request can trigger many operations.

---

### **7. Assuming database interaction means only one database call**

A request can involve:

```
Redis
Database
Payment Service
Inventory Service
Notification Service
```

The complexity grows significantly in distributed systems.

---

### **8. Assuming 5xx means the backend server crashed**

A 5xx response represents a server-side/gateway problem.

It does not necessarily mean the application process crashed.

For example, a service can intentionally return:

```
503 Service Unavailable
```

during temporary unavailability.

---

## **🔑 Key Takeaways**

```
HTTP Request
     ↓
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
Database / External Services
     ↓
Build Response
     ↓
HTTP Response
```

Remember the questions each stage answers:

```
Reception
→ What did I receive?

Validation
→ Is the request structurally valid?

Authentication
→ Who is making this request?

Authorization
→ Are they allowed to perform this operation?

Business Logic
→ Is this operation valid according to the domain?

Data / Services
→ What information or external capabilities do I need?

Response
→ What result should I communicate back?
```

The most important HLD insight:

**A request is a journey through multiple components and responsibilities, and every boundary can introduce latency or failure.**

This becomes the foundation for understanding:

```
Request Lifecycle
       ↓
Failure Points
       ↓
Latency
       ↓
Timeouts
       ↓
Retries
       ↓
Idempotency
       ↓
Reliability
       ↓
Distributed Systems
```

---

## **📚 References**

- HTTP Semantics — RFC 9110
- HTTP/1.1 — RFC 9112
- HTTP/2 — RFC 9113
- HTTP/3 — RFC 9114
- Java Spring Boot documentation