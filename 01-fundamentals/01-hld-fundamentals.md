# 🏛️ HLD Fundamentals

![What is HLD](https://img.shields.io/badge/-What%20is%20HLD-6366f1) 
![Functional Requirements](https://img.shields.io/badge/-Functional%20Requirements-6366f1)
![Non-Functional Requirements](https://img.shields.io/badge/-Non--Functional%20Requirements-6366f1)
![Architecture Thinking](https://img.shields.io/badge/-Architecture%20Thinking-6366f1)

---

## ❓ Problem

As software systems grow from serving hundreds to millions or billions of users, running everything on a single server fails:

- Server becomes overloaded (CPU/Memory saturation)
- Database becomes a bottleneck
- Response latency degrades significantly
- A single server failure causes complete downtime (Single Point of Failure)
- Data volume exceeds disk capacity

Without High-Level Design (HLD), applications cannot scale reliably or maintain high availability.

---

## 📋 Prerequisites

Before starting HLD, you should have a general understanding of:
- Basic Web Application Architecture (Client, Server, Database)
- Software Engineering fundamentals

---

## 🧠 Theory

### What is HLD?
High-Level Design (HLD) defines the overall architecture of a software system. It focuses on:
- Major system components
- Communication protocols between components
- Data flow and storage strategy
- Scalability, reliability, and performance trade-offs

HLD answers:
> *"How should the overall system be structured?"*

### Basic HLD Thinking
Never start by choosing specific technologies (e.g. "let's use Redis and Kafka").

Instead, follow this design flow:
```text
Requirements
     ↓
Workload & Capacity
     ↓
Problems & Bottlenecks
     ↓
Possible Solutions
     ↓
Trade-offs & Decisions
     ↓
Architecture
```

### Functional Requirements
Functional requirements define **what** the system should do.
Example: For a URL Shortener:
- Shorten a long URL
- Redirect a short URL to the original URL

### Non-Functional Requirements
Non-functional requirements define **how well** the system must perform.
Examples:
- Low Latency (e.g., Redirect latency < 200ms)
- High Availability (e.g., 99.99% uptime)
- Scalability (e.g., Handle 100,000 RPS)
- Reliability & Durability

### Functional vs Non-Functional

| Functional | Non-Functional |
|---|---|
| Create URL | Low latency |
| Redirect URL | High availability |
| Send message | Scalability |
| Upload file | Reliability |

**Key Principle:** *Requirements drive architecture.*

---

## 🗺️ Diagram

```text
User Request
     │
     ▼
┌───────────┐     HTTP     ┌───────────────┐     SQL/NoSQL     ┌──────────┐
│  Client   │ ────────────►│ Load Balancer │ ─────────────────►│ Database │
└───────────┘              └───────────────┘                   └──────────┘
                                   │
                                   ▼
                           ┌───────────────┐
                           │ Application   │
                           │ Backend       │
                           └───────────────┘
```

---

## 🏗️ Real Example

### URL Shortener (e.g. TinyURL / Bitly)
- **Functional:** Given `https://example.com/very/long/url`, produce `short.ly/aB92x` and redirect users upon access.
- **Non-Functional:** Highly read-heavy system (100:1 read-to-write ratio), requiring ultra-low latency redirection (< 100ms) and high availability.

---

## 🎯 Interview Questions

**Q: What is HLD?**  
A: HLD is the process of designing the overall architecture of a software system. It defines the major components, how they communicate, how data flows between them, and how the system handles scalability, reliability, performance, and trade-offs.

**Q: Why do functional requirements precede non-functional requirements in system design interviews?**  
A: Functional requirements define the API boundaries, data entities, and system flow. Non-functional requirements dictate how to scale, replicate, partition, and cache those components.

---

## ⚠️ Mistakes / Gotchas

- **Jumping to technologies first:** Choosing tech stacks before defining requirements or calculating capacity.
- **Ignoring non-functional requirements:** Designing a system that works functionally but crashes under production traffic spikes.
- **Over-engineering early:** Adding microservices, Kafka, and distributed caches when a simple modular monolith suffices.

---

## 🔑 Key Takeaways

- HLD focuses on overall system architecture and component interactions.
- Functional requirements describe what the system does.
- Non-functional requirements describe how well it performs (latency, availability, scale).
- System requirements drive architectural choices—not tech hype.
- Scalability and reliability become crucial as user traffic grows.

---

## 📚 References

- System Design Interview – An Insider's Guide by Alex Xu
- Designing Data-Intensive Applications by Martin Kleppmann

---

## 🚧 Pending Topic

- Capacity Estimation (Traffic, Storage, Bandwidth, Memory calculations)
