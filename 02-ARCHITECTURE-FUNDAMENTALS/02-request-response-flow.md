# 🌐 HTTP Request / Response
![HLD](https://img.shields.io/badge/HLD-Architecture%20Fundamentals-blue)
![Topic](https://img.shields.io/badge/Topic-Request%20%2F%20Response%20Flow-orange)
![Protocol](https://img.shields.io/badge/Protocol-HTTP-green)
![Level](https://img.shields.io/badge/Level-Foundational-purple)

> **HTTP (HyperText Transfer Protocol)** is a communication protocol that defines how clients and servers exchange messages using a common, predictable set of rules.
---
## ❓ Problem
A client and a server need to communicate.
For example:
```text
Mobile App
    │
    │  "Give me my order"
    ▼
Backend Server
```

But simply saying:

“Give me order 123”

is not enough.

>Both systems need to agree on:

* Where the request should go
* What operation is being requested
* Which resource is involved
* What data is being sent
* What additional context is required
* How the result should be communicated
* How success and failure should be represented

HTTP provides a standardized way to structure this communication.
```
Client
  │
  │ HTTP Request
  ▼
Server
  │
  │ HTTP Response
  ▼
Client
```
>
> 💡 **First-Principles Insight**
>
> The real problem is not *"how do I call a REST API?"*
>
> It is: **How can two independent systems communicate using a common, predictable set of rules?**
>
> HTTP is one well-established answer to that problem.
>

## 🔑 2. Prerequisites

Before this topic, make sure you understand:

- ✅ Client-Server Architecture
- ✅ Request and Response (conceptual)
- ✅ Backend Server and Database basics
- ✅ Basic networking terminology

Basic architecture:
```
Client
  │
  │ Request
  ▼
Backend
  │
  │ Request
  ▼
Database
```
HTTP is commonly used as the communication protocol between these components.


## 🧠 Theory

### 1. HTTP Fundamentals

HTTP = HyperText Transfer Protocol

HTTP is a communication protocol.

It defines rules for exchanging requests and responses between clients and servers.
```
Client
  │
  │ HTTP Request
  ▼
Server
  │
  │ HTTP Response
  ▼
Client
```

HTTP is not:

* A programming language
* A backend framework
* A database
* A server
* An API

For example:
```
Flutter  ─── HTTP ───▶ Spring Boot
React    ─── HTTP ───▶ Node.js
Android  ─── HTTP ───▶ Go
```
Different technologies can communicate because they follow the same protocol.

---

### 2. HTTP Request

An HTTP request is a message sent from a client to a server.

The client is essentially saying:

“I want something to happen.”

Conceptually:
```
HTTP Request
│
├── Method
├── URL
├── Headers
├── Query Parameters
└── Body
```
Example:
```
POST /orders?coupon=SAVE20 HTTP/1.1
Host: api.example.com
Authorization: Bearer abc123
Content-Type: application/json
Accept: application/json
{
  "items": [
    {
      "itemId": "P100",
      "quantity": 2
    }
  ]
}
```

### 3. Anatomy of a Request

Before looking at the HTTP-specific pieces, think about a request conceptually.
```
REQUEST
│
├── Destination
│     └── Order Service
│
├── Intent
│     └── Create Order
│
├── Parameters
│     ├── Coupon = SAVE20
│     └── Item ID = P100
│
├── Metadata
│     ├── Request ID = abc-789
│     ├── Client = Android
│     └── Authentication information
│
└── Body
      └── Main application payload
```
---
#### Destination

Where should the request go?

Example:

Order Service

In HTTP, the destination is represented through the URL/host/path.

---

#### Intent

What does the sender want?

Example:

Create Order

HTTP methods help communicate this intent.
```
GET     → Retrieve
POST    → Submit / Process
PUT     → Replace
PATCH   → Partially modify
DELETE  → Remove
```
---

Parameters

Which values are required to perform the operation?

Examples:

Order ID = 123
Quantity = 2
Coupon = SAVE20

Parameters may appear in different places depending on API design:

Path
Query
Body

---

Metadata

What additional context accompanies the request?

Examples:

Request ID
Client information
Content type
Authentication information
API version

Metadata is information about the request rather than the main business payload.

---

⚠️ The exact location of a piece of information is API/mechanism dependent. For example, authentication information can be carried in headers, cookies, or other mechanisms.

---

#### Body

The body carries the main application payload when one is needed.

Example:
```
{
  "items": [
    {
      "itemId": "P100",
      "quantity": 2
    }
  ]
}
```

---
### 4. HTTP Response

An HTTP response is a message sent from the server back to the client.

The server is essentially saying:

“Here is what happened as a result of your request.”

Conceptually:
```
HTTP Response
│
├── Status Code
├── Headers
└── Body
```
Example:
```
HTTP/1.1 201 Created
Content-Type: application/json
{
  "orderId": "ORD123",
  "status": "PLACED"
}
```

---
### 5. Anatomy of a Response

A response can be understood conceptually as:
```
RESPONSE
│
├── Result / Data
│     └── Order ID = ORD123
│
├── Success / Failure
│     └── Success
│
├── Status Information
│     └── 201 Created
│
├── Metadata
│     ├── Request ID = abc-789
│     └── Timestamp
│
└── Body
      └── Response payload

          Result / Data
```
What was produced?

Example:

Order ID = ORD123

---

#### Success / Failure

Did the requested operation succeed?

Example:

Success

or:

Failure

---

#### Status Information

Provides more specific information about what happened.

HTTP provides standardized status codes:
```
201 Created
400 Bad Request
404 Not Found
500 Internal Server Error
```
---

#### Metadata

Additional contextual information.

Examples:
```
Request ID
Timestamp
Server information
Caching information
```
---

#### Body

The response body can contain the actual data returned to the client.

Example:

{
  "orderId": "ORD123",
  "status": "PLACED"
}

---

### 6. Request ≠ Operation ≠ Response

These three concepts are related but different.

---
#### Request

What the client asks for.

Transfer ₹10,000 to Rahul

---

#### Operation

The work the backend performs.
```
Authenticate user
       ↓
Authorize transfer
       ↓
Validate request
       ↓
Check balance
       ↓
Debit account
       ↓
Credit Rahul
       ↓
Record transaction
```
---
#### Response

What the backend tells the client afterward.

Transfer successful
Transaction ID = TXN123

---
Mental model:
```
REQUEST
"What do you want?"
       ↓
OPERATION
"What work must I perform?"
       ↓
RESPONSE
"What happened?"
```

---

### 7. URL and Endpoint

#### URL

A URL identifies where a request should be sent.

Example:

https://api.example.com/users/123

Breakdown:
```
https://api.example.com/users/123
│       │               │
│       │               └── Path
│       └────────────────── Host
└────────────────────────── Scheme
```
---

#### Endpoint

An API endpoint represents a specific API operation exposed by a server.

A useful mental model is:

HTTP Method + Path

Examples:
```
GET    /users/123
POST   /orders
PATCH  /users/123
DELETE /users/123
```
The same path can support different operations:
```
GET /users/123
      ↓
Retrieve user
DELETE /users/123
         ↓
Remove user
```
---

### 8. Host and Path

Consider:

https://api.example.com/orders/123

Host

api.example.com

Identifies the network destination.

Path

/orders/123

Identifies the resource or resource hierarchy being addressed.

```
/orders
    ↓
Orders collection
/orders/123
    ↓
Specific order
```

---

#### Host vs Origin

These are different concepts.
```
Host
↓
api.example.com

Origin
↓
https://api.example.com

The origin includes:

Scheme + Host
```
---

### 9. Path Parameters

A path parameter is a value embedded in the URL path that identifies a resource or part of a resource hierarchy.

Example:

GET /orders/123

Template:

/orders/{orderId}

Therefore:

orderId = 123

Examples

GET /users/456
userId = 456

Nested resource:

GET /users/456/orders

Conceptually:

/users/{userId}/orders

This can represent:

Orders belonging to user 456.

Mental Model

Ask:

Does this value help identify which resource or resource hierarchy I am addressing?

If yes, a path parameter is often appropriate.

---

### 10. Query Parameters

Query parameters appear after ?.

Example:

GET /orders?status=shipped&limit=20

Here:
```
status = shipped
limit  = 20
```
Query parameters commonly support:

* Filtering
* Searching
* Sorting
* Pagination
* Optional refinements

Filtering

GET /orders?status=shipped

Searching

GET /restaurants?search=pizza

Sorting

GET /restaurants?sort=rating

Pagination

GET /orders?page=2&limit=20

Traditional offset calculation:
```
offset = (page - 1) × limit
offset = (2 - 1) × 20
       = 20
```
---

### 11. Path vs Query Parameters

Compare:

GET /orders/123

with:

GET /orders?status=shipped

The first identifies a specific resource.

The second addresses the collection and applies a filter.

Useful Heuristic
```
Does removing the value change which resource is addressed?
        ↓
      PATH
Does removing the value leave the same resource/collection
but remove a filter or refinement?
        ↓
      QUERY
```
Both Together

GET /orders/123?include=payment

Here:
```
123
↓
Identifies the order
```
and:
```
include=payment
↓
Refines the requested representation
```
---

### 12. HTTP Methods

HTTP methods communicate the intended operation.


| Method | Common Meaning |
| :--- | :--- |
| **GET** | Retrieve |
| **POST** | Submit / Process |
| **PUT** | Replace |
| **PATCH** | Partially modify |
| **DELETE** | Remove |

---

#### GET

Used to retrieve information.

GET /users/123

Meaning:

Retrieve user 123.

---

#### POST

Used to submit data for processing.

A common use is creating a resource.

POST /orders
{
  "items": [
    {
      "itemId": "P100",
      "quantity": 2
    }
  ]
}

POST is not fundamentally limited to resource creation.

---

#### PUT

Generally represents replacement of a resource representation.

PUT /users/123
{
  "name": "Aditya",
  "email": "aditya@example.com",
  "city": "Bangalore"
}

Conceptually:

Replace the representation of user 123 with this representation.

---

#### PATCH

Generally represents partial modification.

PATCH /users/123
{
  "city": "Bangalore"
}

Only the specified field is being modified.

---
#### PUT vs PATCH
```
❌ PUT   = big update
❌ PATCH = small update
✅ PUT   = replacement semantics
✅ PATCH = partial modification semantics
```
---

#### DELETE

Used to remove a resource.

DELETE /users/123

---

### 13. HTTP Headers

Headers carry additional information and control metadata about an HTTP message.

Example:
```
Authorization: Bearer abc123
Content-Type: application/json
Accept: application/json
```
Mental model:
```
Headers
   ↓
Information about the message
Body
   ↓
Main application payload
```
---

#### Authorization

Example:

Authorization: Bearer abc123

Carries authentication credentials/context in this common scheme.

Remember:
```
Authentication
↓
Who are you?
Authorization
↓
What are you allowed to do?
```
---

#### Content-Type

Describes the media type of the body being sent.

Example:

Content-Type: application/json

Meaning:

The body is JSON.

---

#### Accept

Tells the server what response representation the client can accept or prefers.

Example:

Accept: application/json

Meaning:

The client can accept JSON as the response representation.
```
Content-Type vs Accept

Content-Type
↓
"What format is the body I'm sending?"
Accept
↓
"What response format can I accept?"
```
---

### 14. Request and Response Body

#### Request Body

Contains the main application payload sent by the client.
```
POST /orders
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
---

#### Response Body

Contains the main data/result returned by the server.
```
HTTP/1.1 201 Created
Content-Type: application/json
{
  "orderId": "ORD123",
  "status": "PLACED"
}
```
Body Is Optional

Not every request or response requires a body.

For example:

HTTP/1.1 204 No Content

indicates successful processing with intentionally no response body.

---

### 15. HTTP Status Codes

Status codes communicate the result of processing a request.
```
1xx → Informational
2xx → Success
3xx → Redirection
4xx → Client / Request Problem
5xx → Server / Gateway Problem
```
For backend/HLD fundamentals, focus primarily on 2xx, 4xx, and 5xx.

---

#### 2xx — Success

200 OK

Request successfully processed.

GET /users/123
200 OK
---
201 Created

Request succeeded and a new resource was created.

POST /orders
201 Created
---
204 No Content

Request succeeded with intentionally no response body.

DELETE /users/123
204 No Content

---

#### 4xx — Client / Request Problem

400 Bad Request

The request itself is invalid.

Example:
```
{
  "quantity": -5
}
```
If the API requires a positive integer:

400 Bad Request

---

401 Unauthorized

In practical API usage, generally indicates that authentication is missing, invalid, or failed.

401
↓
Authentication problem

---

403 Forbidden

The user is authenticated but does not have permission to perform the requested operation.
```
Authentication → PASS
Authorization  → FAIL
                    ↓
                   403
```
---

404 Not Found

The requested resource cannot be found.

GET /users/999999

If the user does not exist:

404 Not Found

---

409 Conflict

The request is valid but conflicts with the current state of a resource.

Example:
```
Inventory = 1
Request A → Buy 1
Request B → Buy 1
```
After one request consumes the inventory, the other may encounter a conflict.

409 Conflict

---

####  5xx — Server / Gateway Problem

500 Internal Server Error

An unexpected internal application/server failure occurred.

500 Internal Server Error

It does not necessarily mean the entire server crashed.

---

502 Bad Gateway

A gateway or proxy received an invalid or unexpected response from an upstream service.

```
Client
  ↓
Gateway
  ↓
Backend
  ↓
Unexpected response
  ↓
Gateway
  ↓
502
```
---

503 Service Unavailable

The service is temporarily unavailable.

Possible causes:

* Overload
* Maintenance
* Temporary dependency problems
* Service unavailable

---

504 Gateway Timeout

A gateway or proxy did not receive a timely response from an upstream service.
```
Client
  ↓
Gateway
  ↓
Backend
  ↓
No timely response
  ↓
Gateway
  ↓
504
```
502 vs 504
```
502
↓
Received a bad/unexpected upstream response
504
↓
Did not receive a timely upstream response
```
---

### 16. HTTP Versions

HTTP has evolved through multiple versions.

Important versions:
```
HTTP/0.9
HTTP/1.0
HTTP/1.1
HTTP/2
HTTP/3
```
The basic request/response model remains, but connection and transport behavior evolved.

---

#### HTTP/1.0

Introduced a more complete HTTP message model and headers compared with the early HTTP versions.

---

#### HTTP/1.1

Added important improvements including persistent connections and became widely adopted.

Simplified:
```
HTTP/1.1
    ↓
TCP
```
---

#### HTTP/2

Introduced multiplexing, allowing multiple streams to share a connection.
```
One TCP Connection
        │
        ├── Stream A
        ├── Stream B
        ├── Stream C
        └── Stream D
```
Multiple requests/responses can be in flight over the same connection.

---

#### HTTP/3

HTTP/3 uses QUIC as its transport protocol.

```
HTTP/3
   ↓
QUIC
   ↓
UDP
```
Simplified comparison:
```
HTTP/1.1 → TCP
HTTP/2   → TCP + Multiplexing
HTTP/3   → QUIC + UDP
```
📌 TCP, UDP, QUIC, handshakes, packet loss, and network-level latency will be covered in detail under Networking Fundamentals.

---

🗺️ Diagram

Complete HTTP Request / Response
```

                         HTTP REQUEST
Client ───────────────────────────────────────▶ Backend
       Method + URL + Headers + Body
                                                    │
                                                    │
                                                    ▼
                                             Process Request
                                                    │
                                                    │
                                                    ▼
                                             Database / Services
                                                    │
                                                    │
                                                    ▼
                         HTTP RESPONSE
Client ◀─────────────────────────────────────── Backend
       Status + Headers + Body

```

Request Anatomy

```
REQUEST
│
├── Destination
│     └── Where?
│
├── Intent
│     └── What operation?
│
├── Parameters
│     └── Which values?
│
├── Metadata
│     └── Additional context
│
└── Body
      └── Main payload
```
Response Anatomy

```
RESPONSE
│
├── Result / Data
│     └── What was produced?
│
├── Success / Failure
│     └── Did it succeed?
│
├── Status Information
│     └── What happened?
│
├── Metadata
│     └── Additional context
│
└── Body
      └── Response payload
```
---

💻 Code

HTTP can be observed directly using tools such as curl.

GET Request

curl https://api.example.com/users/123

POST Request
```
curl -X POST https://api.example.com/orders \
  -H "Content-Type: application/json" \
  -d '{
    "items": [
      {
        "itemId": "P100",
        "quantity": 2
      }
    ]
  }'
```
The important thing is not memorizing curl.

The goal is to see that an HTTP request contains:
```
Method
URL
Headers
Body
```
---

🏗️ Real Example

Consider a food-ordering application.

The user taps:

Place Order

Step 1 — Client creates HTTP request
```
POST /orders?coupon=SAVE20 HTTP/1.1
Host: api.example.com
Authorization: Bearer abc123
Content-Type: application/json
Accept: application/json
{
  "items": [
    {
      "itemId": "P100",
      "quantity": 2
    }
  ]
}
```
Step 2 — Backend receives request

The backend can determine:
```
Method
→ POST
Path
→ /orders
Query
→ coupon=SAVE20
Headers
→ Authorization, Content-Type, Accept
Body
→ Order information
```
Step 3 — Backend processes it

Conceptually:
```
Request
   ↓
Process
   ↓
Check required data
   ↓
Perform required operations
   ↓
Produce result
```
The detailed backend processing pipeline is covered separately.

Step 4 — Backend sends response
```
HTTP/1.1 201 Created
Content-Type: application/json
{
  "orderId": "ORD123",
  "status": "PLACED"
}
```
Step 5 — Client interprets response
```
201
↓
Order created
ORD123
↓
Order identifier
PLACED
↓
Current business state
```
Notice the distinction:
```
HTTP Status
↓
201 Created
```
versus:
```
Business Status
↓
PLACED
```
They represent different layers of information.

---

### 🎯 Interview Questions

1. What is HTTP?

Answer:

HTTP (HyperText Transfer Protocol) is a communication protocol that defines how clients and servers exchange requests and responses.

---

2. Why do we need HTTP?

Answer:

Independent systems need a common set of rules to communicate. HTTP provides standardized structures for requests, responses, methods, headers, status codes, and message bodies.

---

3. Is HTTP a programming language or framework?

Answer:

No.

HTTP is a communication protocol.

Frameworks such as Spring Boot, NestJS, Express, and others can be used to build applications that communicate over HTTP.

---

4. What is an HTTP request?

Answer:

An HTTP request is a message sent by a client to a server asking it to perform an operation or provide information.

It commonly contains:
```
Method
URL
Headers
Body
```
---

5. What is an HTTP response?

Answer:

An HTTP response is a message sent by the server back to the client containing the result of processing the request.

It commonly contains:
```
Status Code
Headers
Body
```
---

6. What is the anatomy of a request?

Answer:

Conceptually:
```
Request
├── Destination
├── Intent
├── Parameters
├── Metadata
└── Body
```
In HTTP, these concepts are represented through the URL, method, headers, query/path parameters, and body.

---

7. What is the difference between a request, operation, and response?

Answer:
```
Request
→ What the client asks for
Operation
→ Work performed by the backend
Response
→ Result communicated back to the client
```
Example:
```
Request:
Transfer ₹10,000
Operation:
Authenticate → authorize → validate → debit → credit
Response:
Transfer successful
```
---

8. What is a URL?

Answer:

A URL identifies where a request should be sent.

Example:

https://api.example.com/users/123

It contains components such as the scheme, host, and path.

---

9. What is an API endpoint?

Answer:

An API endpoint represents a specific API operation exposed by a server.

A useful mental model is:

HTTP Method + Path

For example:

GET /users/123

---

10. What is the difference between host and path?

Answer:

The host identifies the network destination.

api.example.com

The path identifies the resource or resource hierarchy.

/users/123

---

11. What is a path parameter?

Answer:

A path parameter is a value embedded in the URL path that identifies a resource or resource hierarchy.

Example:

GET /users/123

Here:

userId = 123

---

12. What is a query parameter?

Answer:

A query parameter is a value included after ? in a URL, commonly used for filtering, searching, sorting, pagination, or optional refinement.

Example:

GET /orders?status=shipped&limit=20

---

13. How do you decide between path and query parameters?

Answer:

Use this heuristic:
```
Changes which resource is addressed
→ Path parameter
Refines/filter/searches the resource or collection
→ Query parameter
```
---

14. What is the difference between PUT and PATCH?

Answer:

PUT generally represents replacement semantics.

PATCH generally represents partial modification.
```
PUT
↓
Replace
PATCH
↓
Partially modify
```
---

15. Is POST only used for creating resources?

Answer:

No.

POST fundamentally represents submitting data for processing. Creating a resource is one of its common API uses.

---

16. What is the difference between Content-Type and Accept?

Answer:

Content-Type describes the format of the body being sent.

Accept tells the server what response representation the client can accept or prefers.
```
Content-Type
→ What format is my body?
Accept
→ What response format can I accept?
```
---

17. What is the difference between 401 and 403?

Answer:

401 generally indicates an authentication problem.

403 indicates that the user is authenticated but does not have permission to perform the operation.

401 → Authentication problem
403 → Authorization problem

---

18. What is the difference between 400 and 404?

Answer:

400 Bad Request indicates that the request itself is invalid.

404 Not Found indicates that the requested resource cannot be found.

400 → Invalid request
404 → Resource not found

---

19. When would you use 409 Conflict?

Answer:

When the request is valid but conflicts with the current state of a resource.

Example:
```
Inventory = 1
Two requests attempt to purchase the last item.
One succeeds.
The other may receive:
409 Conflict
```
---

20. What is the difference between 502 and 504?

Answer:

502
→ Gateway received an invalid/unexpected upstream response
504
→ Gateway did not receive a timely upstream response

---

21. What is the difference between HTTP/1.1, HTTP/2, and HTTP/3?

Answer:

At a high level:
```
HTTP/1.1
→ TCP
HTTP/2
→ TCP + Multiplexing
HTTP/3
→ QUIC + UDP
```
HTTP/2 allows multiple streams over one connection, while HTTP/3 uses QUIC as its transport protocol.

---

22. Can one HTTP request trigger multiple backend operations?

Answer:

Yes.

For example, one:

POST /orders

could cause the backend to:
```
Validate request
   ↓
Check user
   ↓
Check inventory
   ↓
Calculate price
   ↓
Save order
   ↓
Call payment service
   ↓
Return response
```
The HTTP request/response represents the external interaction, while the backend may perform many internal operations.

---

23. Can a backend make another HTTP request?

Answer:

Yes.

A backend can act as a client when communicating with another service.
```
Mobile App
    │
    │ HTTP
    ▼
Order Service
    │
    │ HTTP
    ▼
Payment Service
```
This is an important foundation for understanding service-to-service communication and microservices.

---

### ⚠️ Mistakes / Gotchas

1. HTTP = API

❌ Incorrect:

HTTP is an API.

✅ Correct:

HTTP = communication protocol
API = interface exposed by a system

An API can use HTTP.

---

2. HTTP = Backend

❌ Incorrect:

HTTP is the backend.

✅ Correct:
```
HTTP
↓
Communication protocol
Backend
↓
Application/system providing services
```
---

3. Client controls authorization

❌ Incorrect:

The frontend hides the admin button,
so the operation is secure.

✅ Correct:

Backend must enforce authorization.

The client is not a trusted authority.

---

4. PUT = Big Update, PATCH = Small Update

❌ Incorrect:

PUT   → Big update
PATCH → Small update

✅ Correct:

PUT   → Replacement semantics
PATCH → Partial modification semantics

---

5. 401 and 403 are the same

❌ Incorrect:
```
401 = Access denied
403 = Access denied
```
Better mental model:
```
401 → Authentication problem
403 → Authorization problem
```
---

6. Every error should return 400

Different situations can have different status codes:
```
400 → Invalid request
401 → Authentication problem
403 → Authorization problem
404 → Resource not found
409 → State conflict
500 → Internal server failure
502 → Bad upstream response
503 → Service unavailable
504 → Upstream timeout
```
---

7. Query parameters are only for filtering

Query parameters can also be used for:
```
Search
Sort
Pagination
Optional refinement
```
---

8. HTTP status and business status are the same

They can represent different layers.
```
HTTP Status
→ 201 Created
Business Status
→ PLACED
```
201 describes the HTTP-level outcome.

PLACED describes the application’s business state.

---

🔑 Key Takeaways

HTTP
```
HTTP
↓
Communication Protocol
↓
Defines how clients and servers exchange messages
```
Request
```
Client
   ↓
HTTP Request
   ↓
Server
```
Response
```
Server
   ↓
HTTP Response
   ↓
Client
```
Request Anatomy
```
REQUEST
├── Destination
├── Intent
├── Parameters
├── Metadata
└── Body
```
Response Anatomy
```
RESPONSE
├── Result / Data
├── Success / Failure
├── Status Information
├── Metadata
└── Body
```
Request vs Operation vs Response
```
Request
→ What the client wants
Operation
→ What the backend does
Response
→ What happened
```
URL
```
URL
├── Scheme
├── Host
└── Path
```
Parameters
```
Path
→ Resource identity / hierarchy
Query
→ Filter / search / sort / pagination / refinement
```
Methods
```
GET
→ Retrieve
POST
→ Submit / Process
PUT
→ Replace
PATCH
→ Partially modify
DELETE
→ Remove
```
Headers
```
Authorization
→ Authentication credentials/context
Content-Type
→ Format of body being sent
Accept
→ Response format client can accept
```
Important Status Codes
```
200 → OK
201 → Created
204 → No Content
400 → Bad Request
401 → Authentication problem
403 → Authorization problem
404 → Not Found
409 → Conflict
500 → Internal Server Error
502 → Bad Gateway
503 → Service Unavailable
504 → Gateway Timeout
```
HTTP Versions
```
HTTP/1.1 → TCP
HTTP/2   → TCP + Multiplexing
HTTP/3   → QUIC + UDP
```
---

## 📚 References

- 📖 [MDN — HTTP](https://developer.mozilla.org/en-US/docs/Web/HTTP)
- 📖 [MDN — HTTP Methods](https://developer.mozilla.org/en-US/docs/Web/HTTP/Methods)
- 📖 [MDN — HTTP Status Codes](https://developer.mozilla.org/en-US/docs/Web/HTTP/Status)
- 📖 [MDN — HTTP Headers](https://developer.mozilla.org/en-US/docs/Web/HTTP/Headers)
- 📄 [RFC 9110 — HTTP Semantics](https://httpwg.org/specs/rfc9110.html)
- 📄 [RFC 9112 — HTTP/1.1](https://httpwg.org/specs/rfc9112.html)
- 📄 [RFC 9113 — HTTP/2](https://httpwg.org/specs/rfc9113.html)
- 📄 [RFC 9114 — HTTP/3](https://httpwg.org/specs/rfc9114.html)

