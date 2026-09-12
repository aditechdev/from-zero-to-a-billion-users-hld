# 🏛️ HLD Fundamentals
![What is HLD](https://img.shields.io/badge/-What%20is%20HLD-6366f1) 
![Functional Requirements](https://img.shields.io/badge/-Functional%20Requirements-6366f1)
![Non-Functional Requirements](https://img.shields.io/badge/-Non--Functional%20Requirements-6366f1)
![Architecture Thinking](https://img.shields.io/badge/-Architecture%20Thinking-6366f1)

## 🧠 What is HLD?
High-Level Design (HLD) is the process of designing the overall architecture of a software system.
It focuses on:
- Major components
- How components communicate
- Data flow
- Scalability
- Reliability
- Performance
- Trade-offs

HLD answers:
> "How should the overall system be structured?"

---

## ❓ Why do we need HLD?
A simple application can run on one server:

```text
User
  ↓
Backend
  ↓
Database
```

As the number of users and requests increases, problems appear:

- Server becomes overloaded
- Database becomes a bottleneck
- Requests become slower
- A server failure can bring down the system
- More data needs to be stored
- More traffic needs to be handled

HLD helps us design the system to handle these problems.

---

## 🗺️ Basic HLD Thinking
We should not start by choosing technologies.

Instead:

```text
Requirements
     ↓
Workload
     ↓
Problems / Bottlenecks
     ↓
Possible Solutions
     ↓
Trade-offs
     ↓
Architecture
```

---

## 📋 Functional Requirements
Functional requirements describe what the system should do.

**Example: URL Shortener**
- Create a short URL
- Convert a short URL back to the original URL
- Redirect the user to the original URL

```text
Long URL
https://example.com/very/long/url
              ↓
Short URL
short.ly/aB92x
```

When the user visits `short.ly/aB92x`, the system should redirect them to the original URL.

---

## ⚙️ Non-Functional Requirements
Non-functional requirements describe how well the system should work.

Examples:
- Low latency
- High availability
- Scalability
- Reliability
- Durability
- Security

For example:
```text
Redirect latency < 200ms
Availability = 99.99%
```

The exact requirements depend on the system.

---

## 📊 Functional vs Non-Functional

| Functional | Non-Functional |
|---|---|
| Create URL | Low latency |
| Redirect URL | High availability |
| Send message | Scalability |
| Upload file | Reliability |

---

## 🔑 Key Principle
**Requirements drive architecture.**

We should understand what the system needs before deciding which technologies or components to use.

---

## 🎯 Interview Questions
**Q: What is HLD?**
A: HLD is the process of designing the overall architecture of a software system. It defines
the major components, how they communicate, how data flows between them, and how the system
handles scalability, reliability, performance, and failures.

---

## ⚠️ Key Takeaways
- HLD focuses on the overall system architecture.
- Functional requirements describe what the system does.
- Non-functional requirements describe how well it should work.
- We should understand requirements before choosing technologies.
- Scalability and reliability become increasingly important as the system grows.

## 🔁 Revision Log
- 2026-09-12: First written
