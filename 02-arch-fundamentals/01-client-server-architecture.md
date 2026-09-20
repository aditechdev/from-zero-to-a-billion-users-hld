# Client-Server Architecture

![HLD](https://img.shields.io/badge/HLD-Architecture%20Fundamentals-blue)
![Client-Server](https://img.shields.io/badge/Client--Server-Architecture-green)
![Shared State](https://img.shields.io/badge/Concept-Shared%20State-orange)
![Business Logic](https://img.shields.io/badge/Concept-Business%20Logic-purple)
![Source of Truth](https://img.shields.io/badge/Concept-Source%20of%20Truth-red)
![Trust Boundary](https://img.shields.io/badge/Concept-Trust%20Boundary-yellow)

---

## ❓ Problem

Modern applications may have millions of clients interacting with the same application.

For example:
```
User A → Mobile App ──┐
User B → Mobile App ──┼──→ Shared Application
User C → Web App    ──┘
```
All of these clients may need to:

* Access the same user data
* Follow the same business rules
* Modify shared state
* Perform operations consistently
* Receive authoritative results

If every client maintained its own data and business rules, the system would quickly become inconsistent and difficult to secure.

For example:
```
User A's App:
Balance = ₹50,000
User B's App:
Balance = ₹50,000
User C's App:
Balance = ₹50,000
```
Which client should be trusted if one of them says:

Balance = ₹10,00,000

We need a central authority that owns the authoritative state and business rules.

That is the fundamental problem Client-Server Architecture solves.

---

## 📋 Prerequisites

Before this topic, make sure you understand:
- HLD Fundamentals (What is HLD, Functional vs Non-Functional requirements)

---

## 🧠 Theory

### First Principles

The fundamental chain is:
```
Multiple Clients
       ↓
Need Shared State
       ↓
Need One Source of Truth
       ↓
Need Common Business Rules
       ↓
Need a Trusted Authority
       ↓
     Backend
```
The backend provides a centralized place where:

* Shared state can be maintained
* Business rules can be enforced
* Requests can be validated
* Authoritative decisions can be made
* State changes can be controlled

### Core Principle

The client displays and interacts with the system, while the server owns the authoritative system state and enforces important business rules.

---

### 🧩 What is a Client?

A client is a component that requests a service or data from another component.

Examples:

```
Mobile App → Backend
Web Browser → Backend
Backend → Database
```

The important point is that client is a role in an interaction, not a permanent identity.

---

### 🖥️ What is a Server?

A server is a component that provides a service or data to another component.

For example:
```
Mobile App
    │
    │ Request
    ↓
 Backend
    │
    │ Database Request
    ↓
 Database
```
Here:
```
Mobile App → Backend
Mobile App = Client
Backend    = Server
```
But:
```
Backend → Database
Backend  = Client
Database = Server
```
Therefore:

Client/Server describes the relationship between components, not the permanent identity of a component.

---

### 🔄 Client-Server Relationship

A component can be a server in one interaction and a client in another.
```
                    Request
Mobile App ─────────────────────→ Backend
   Client                         Server
                                    │
                                    │ Request
                                    ↓
                                Database
                                Server
                                    ↑
                                  Client
                                Backend

```
This distinction becomes extremely important as systems become more distributed.

---

### 🏗️ Responsibilities

#### Client Responsibilities

The client is primarily responsible for:

* User interaction
* UI rendering
* Presentation
* Collecting user input
* Local UI state
* Client-side validation for better UX
* Sending requests
* Displaying server results

Example:
```
User
 ↓
Mobile App
 ↓
"Transfer ₹10,000"
 ↓
Send request to Backend
```
---

#### Server Responsibilities

The server is responsible for authoritative operations such as:

* Maintaining shared state
* Applying business rules
* Validating requests
* Authentication/authorization
* Reading and modifying authoritative data
* Making authoritative decisions
* Returning the result

Example:
```
Client
  │
  │ "Transfer ₹10,000"
  ↓
Server
  │
  ├── Is user authenticated?
  ├── Is user authorized?
  ├── Is balance sufficient?
  ├── Is transfer allowed?
  └── Execute transfer
```
---

### 🔐 Why Can’t We Trust the Client?

The client is controlled by the user.

A legitimate application might contain:
```
if (!isLoggedIn) {
    hideFollowButton();
}
```
But hiding a button is not security.

A malicious user could modify the application or bypass the UI and directly send a request.

Therefore:
```
Client:
"User is logged in."
        ↓
Server:
"Let me verify that myself."
```
Core Principle

Client-side checks are useful for user experience, but server-side checks are authoritative.

---

## 🗺️ Diagram

```
User A (Mobile) ──┐
User B (Mobile) ──┼──► [ Client-Side UI ] ──► (HTTP Request) ──► [ Backend Server ] ──► [ Database ]
User C (Web)    ──┘                                               (Trusted Authority)    (Source of Truth)
```

---

## 🏗️ Real Example

### 💰 Example: Banking Application

Suppose the application displays:
```
Balance: ₹50,000
[ Transfer ₹10,000 ]
```
The client can display the balance.

But the server must determine:
```
Is the actual balance ₹50,000?
        ↓
Is the user authenticated?
        ↓
Is the user authorized?
        ↓
Is ₹10,000 transferable?
        ↓
Should the transfer happen?
```
The client cannot be the final authority.

Why?

Because:

Client = controlled by user
Server = trusted authority

---

#### 🎟️ Example: Coupon

Suppose a user enters:

SAVE50

The client can send:

Coupon = SAVE50

But the server should determine:
```
Is the coupon valid?
Is it expired?
Is the user eligible?
What discount applies?
What is the maximum discount?
```
The client can display:

You saved ₹50

But the server determines whether the ₹50 discount is actually valid.

---

#### 📦 Example: Last Available Product

Suppose:

Inventory = 1

Two users simultaneously attempt to purchase it.
```
User A ──┐
         ├──→ Backend
User B ──┘
```
Both clients cannot independently decide that the product is available.

The backend must maintain the authoritative inventory state and ensure the final state is valid.

Expected result:
```
User A → SUCCESS
User B → SOLD OUT
Inventory → 0
```
Not:
```
User A → SUCCESS
User B → SUCCESS ❌
Inventory → -1
```
This introduces the concept of **concurrency**, which will be studied properly later.

---

🧠 Shared State

One of the primary reasons for a backend is to maintain shared state.

Consider:
```
                 Backend
                    │
          ┌─────────┼─────────┐
          ↓         ↓         ↓
       Client A  Client B  Client C
```
All clients interact with the same underlying system.

Examples of shared state:

* User account
* Account balance
* Product inventory
* Order status
* Followers
* Messages
* Payment status

The backend acts as the central authority for this state.

---

#### 🎯 Source of Truth

A system needs to know:

Which value should be considered correct?

For example:
```
Client:
Balance = ₹10,00,000
Backend:
Balance = ₹50,000
```
The backend’s value is authoritative.

The client may display:

₹50,000

but it should not be able to declare:

₹10,00,000

and make that the system’s truth.

Mental Model
```
Server
  ↓
Authoritative State
  ↓
Client
  ↓
Presentation
```
The client displays the state; the server owns the authoritative state.

---

🔄 Client and Server Don’t Need the Same UI

The same backend can serve:
```
                 Backend
              Common Data
             /     |      \
            ↓      ↓       ↓
          iOS   Android   Web
```
Each client can have a different presentation:
```
iOS       → iOS UI
Android   → Android UI
Web       → Browser UI
```
The backend provides the underlying data and decisions.

This allows different clients to share the same system state and business rules without requiring identical UI implementations.

---

⚖️ Client-Side vs Server-Side Validation

Client-side validation:
```
User enters:
"abc"
Client:
"Email format looks invalid."
```
Useful for:

* Fast feedback
* Better UX
* Reducing unnecessary requests

But it is not authoritative.

Server-side validation:
```
Client → "Transfer ₹10,000"
```
Server:
```
✓ Authenticated?
✓ Authorized?
✓ Sufficient balance?
✓ Valid request?
```
The server must independently enforce important rules.

Principle

Never assume that the client followed your rules.

---

🏛️ Logical Server vs Physical Server

A server does not necessarily mean:
```
One application
    ↓
One physical machine
```
A backend service might actually run on many machines:

              Backend Service
             /      |       \
            ↓       ↓        ↓
         Server   Server   Server

From the client’s perspective, these can collectively behave as one logical server/service.

The physical architecture becomes important when we study:

* Load balancing
* Horizontal scaling
* Service discovery
* Distributed systems

These will be covered later.

---

🗺️ Overall Architecture

A simple modern application can be represented as:
```
                Users
                  │
          ┌───────┴───────┐
          ↓               ↓
     Mobile App       Web Browser
          │               │
          └───────┬───────┘
                  ↓
               Backend
                  │
                  ↓
              Database
```
At this stage, don’t worry about:
```
DNS
Load Balancer
API Gateway
Cache
Kafka
Microservices
```
Those are separate architectural concepts that build on this foundation.

---

⚠️ Common Mistakes

Mistake 1: “The client should contain all business logic.”

❌ Not for authoritative business rules.

The client is controlled by the user.

---

Mistake 2: “Client-side validation provides security.”

❌ It provides UX.

Security-critical validation must happen on the server.

---

Mistake 3: “Server means one physical machine.”

❌ Server is a logical role/service.

A server can run across many machines.

---

Mistake 4: “A backend is always a server.”

❌ Not necessarily.
```
Mobile App → Backend
             Server
Backend → Database
          Client
```
Roles depend on the interaction.

---

Mistake 5: “Client and server must use the same UI/data representation.”

❌ No.

The backend provides data/services; different clients can present them differently.

---

## ⚠️ Mistakes / Gotchas

Mistake 6: “Client-server exists mainly because mobile devices don’t have enough storage.”

❌ Storage is only one possible concern.

The deeper reason is:
```
Multiple clients
       ↓
Shared state
       ↓
Common rules
       ↓
Trusted authority
```
---

## 🎯 Interview Questions

Beginner

Q1. What problem does Client-Server Architecture solve?

Expected direction:

Multiple clients need to interact with shared state while following common business rules, so a central trusted authority is needed.

---

Q2. What is a client?

A component that requests data or services from another component.

---

Q3. What is a server?

A component that provides data or services to another component.

---

Q4. Can a backend be a client?

Yes.

Backend → Database

The backend is the client in that interaction.

---

Intermediate

Q5. Why shouldn’t important business rules be trusted to the client?

Because the client is controlled by the user and can potentially be modified or bypassed.

---

Q6. Why do we need a central source of truth?

Because multiple clients need to agree on the same authoritative state.

---

Q7. Why can different clients use the same backend?

Because the backend provides shared services/data while each client independently handles presentation and interaction.

---

🧪 Mental Model

When you see:

A → B

ask:

Who is requesting something?

That component is the client.

Then ask:

Who is providing it?

That component is the server.

And when designing a system, ask:

Where does the authoritative state live?

Where are the important business rules enforced?

Those questions will take you much further than simply memorizing “client = frontend, server = backend.”

---

## 🔑 Key Takeaways

1. Client-Server Architecture separates requesting components from providing components.
2. The fundamental problem is shared state and common business rules across multiple clients.
3. The backend acts as the trusted authority for authoritative state and business decisions.
4. Clients are controlled by users and therefore should not be trusted to enforce security-critical business rules.
5. Client-side validation improves UX; server-side validation provides authoritative enforcement.
6. Client/server is a relationship, not a permanent identity.
```
Mobile App → Backend
   Client     Server
Backend → Database
 Client    Server
```
7. The client primarily handles presentation and interaction.
8. The server primarily handles authoritative state and business rules.
9. A logical server can run across multiple physical machines.
10. Concurrency occurs when multiple operations interact with shared state around the same time.

11. Concurrency occurs when multiple operations interact with shared state around the same time.
---

## 📚 References
- System Design Interview – An Insider's Guide by Alex Xu
- Designing Data-Intensive Applications by Martin Kleppmann

---

## 🚧 Pending Topic
- Request / Response Flow
- Backend Request Processing Pipeline

