# HLD Networking Fundamentals

![HLD](https://img.shields.io/badge/HLD-Architecture%20Fundamentals-blue)
![Networking](https://img.shields.io/badge/Topic-Networking%20Fundamentals-orange)
![TCP/IP](https://img.shields.io/badge/Protocol-TCP%2FIP-green)
![HTTP/TLS](https://img.shields.io/badge/Protocol-HTTP%2FTLS-purple)
![Latency](https://img.shields.io/badge/Concept-Latency-red)

---

## ❓ Problem

Before designing a distributed backend, we need to understand what actually happens when one service communicates with another.

For example:

```
Client
   ↓
https://api.example.com/orders
   ↓
Network
   ↓
Backend Server
   ↓
Database
```

Several questions appear immediately:

- How does the client find the server?
- What does an IP address identify?
- Why do we need ports?
- How does data reliably travel between two machines?
- Why does HTTP use TCP?
- Why is HTTPS different from HTTP?
- Why don’t we perform a TCP handshake for every HTTP request?
- Why does a request sometimes take 20 ms and sometimes 500 ms?

These concepts form the networking foundation required for understanding load balancers, reverse proxies, APIs, databases, caching, microservices, and distributed systems.

---

## **📋 Prerequisites**

You should be comfortable with:

- Basic client-server architecture
- Basic HTTP request/response
- Basic backend request flow
- Basic understanding of servers and applications

---

# **🧠 Theory**

## **1. IP Address & Port**

### **IP Address**

An **IP address identifies a network destination/interface** so packets can be routed toward it.

Example:

```
192.168.1.10
```

Think:

**IP → Where should the network traffic go?**

A machine can have multiple network interfaces and therefore multiple IP addresses.

---

### **Port**

A **port identifies a transport-layer endpoint/service** on a host.

Example:

```
192.168.1.10:8080
```

Here:

```
192.168.1.10 → IP address
8080         → Port
```

Think:

**IP → Which host/interface?**
 **Port → Which network service/endpoint?**

One machine can run multiple services:

```
192.168.1.10
│
├── :80    → HTTP
├── :443   → HTTPS
├── :3306  → MySQL
└── :6379  → Redis
```

### **Important**

A port is **not simply an application ID**.

It identifies a transport endpoint that applications/services can listen on.

---

## **2. TCP vs UDP**

Applications don’t directly decide that “this is a TCP packet” at the business-logic level.

The application uses a networking API/protocol stack, which determines whether communication uses TCP or UDP.

Conceptually:

```
Application
     ↓
HTTP / Custom Protocol
     ↓
TCP / UDP
     ↓
IP
     ↓
Network
```

### **TCP**

TCP provides:

- Connection-oriented communication
- Reliable delivery
- Ordered delivery
- Retransmission
- Flow control
- Congestion control

Example:

```
Sender
  │
  ├── Packet 1 ──────→
  ├── Packet 2 ──────→
  ├── Packet 3 ──X    │ lost
  │
  │ ←── ACK ──────────
  │
  └── Retransmit 3 ──→
```

### **UDP**

UDP is much simpler:

- Connectionless
- No TCP-style delivery guarantee
- No ordering guarantee
- No built-in retransmission
- Lower protocol overhead

The application can implement its own reliability if required.

### **Don’t memorize:**

TCP = slow
 UDP = fast

That is an oversimplification.

The actual trade-off depends on the protocol, network conditions, application behavior, and required guarantees.

---

## **3. TCP Connection — Conceptual**

TCP establishes a connection using the **three-way handshake**.

```
Client                         Server
  │                              │
  │ ─────── SYN ───────────────→ │
  │                              │
  │ ←──── SYN + ACK ──────────── │
  │                              │
  │ ─────── ACK ───────────────→ │
  │                              │
  │       Connection Ready       │
```

### **Why three messages?**

Both sides need to establish that communication is possible and synchronize TCP state.

Think:

```
Client: "Can we communicate?"
Server: "Yes, I received you. I'm ready too."
Client: "Confirmed."
```

After this, application data can be transmitted.

### **Important**

The TCP handshake happens when establishing the TCP connection.

It does **not** necessarily happen before every HTTP request because the connection can be reused.

---

## **4. HTTP over TCP**

HTTP operates at the application layer.

TCP operates below it.

```
Application
     ↓
HTTP
     ↓
TCP
     ↓
IP
     ↓
Network
```

HTTP understands:

```
GET /orders/123
```

TCP does not understand:

```
GET
/orders/123
```

TCP sees a stream of bytes.

Its responsibility is to transport those bytes reliably and in order.

Conceptually:

```
IP Packet
└── TCP Segment
    └── HTTP Data
```

At the receiver:

```
Network
   ↓
IP
   ↓
TCP
   ↓
HTTP
   ↓
Application
```

This is an example of **encapsulation**: each networking layer carries information from the layer above it.

---

# **5. HTTPS / TLS**

HTTPS is essentially:

```
HTTP + TLS
```

Traditional HTTPS over TCP:

```
HTTP
 ↓
TLS
 ↓
TCP
 ↓
IP
```

TLS provides three important properties:

### **1. Confidentiality**

Others shouldn’t be able to read the protected HTTP contents.

### **2. Integrity**

Data should not be silently modified in transit.

### **3. Authentication**

TLS allows the client to verify the server’s identity through certificates.

---

### **Traditional HTTPS connection**

Conceptually:

```
TCP Handshake
      ↓
TLS Handshake
      ↓
Secure Connection
      ↓
HTTP Request
      ↓
HTTP Response
```

TCP provides:

Reliable transport

TLS provides:

Secure communication

HTTP provides:

Application semantics

These are different responsibilities.

---

## **6. What Can a Network Observer See?**

HTTPS does **not** mean that every piece of network metadata becomes invisible.

Depending on where the observer is located, they may still observe things such as:

```
Source IP
Destination IP
Port
Packet timing
Packet sizes
Traffic patterns
```

But the HTTP application contents are protected by TLS.

So:

```
HTTPS ≠ "everything about communication is invisible"
```

---

# **7. Connection Reuse / Keep-Alive**

Suppose a client needs to make three requests.

Without connection reuse:

```
TCP handshake
Request 1
Response 1
Connection close

TCP handshake
Request 2
Response 2
Connection close

TCP handshake
Request 3
Response 3
Connection close
```

This repeatedly pays connection-establishment overhead.

With connection reuse:

```
TCP handshake
      ↓
Request 1 → Response 1
      ↓
Request 2 → Response 2
      ↓
Request 3 → Response 3
      ↓
Connection eventually closes
```

This is **connection reuse / persistent connections**.

### **Why is this useful?**

Because establishing a connection has overhead.

Reusing connections can reduce:

- Connection setup overhead
- Latency
- CPU overhead
- Network overhead

---

### **Keep-alive does NOT mean:**

The connection stays open forever.

Connections can be closed because of:

- Idle timeout
- Server configuration
- Client configuration
- Network failure
- Resource management

---

## **Connection Reuse ≠ HTTP/2 Multiplexing**

These concepts are related but different.

### **Connection reuse**

Reuse the same connection for multiple requests.

```
One TCP connection
│
├── Request 1
├── Request 2
└── Request 3
```

### **HTTP/2 multiplexing**

Multiple streams can be active concurrently over one TCP connection.

```
One TCP connection
│
├── Stream 1 ─ Request A
├── Stream 2 ─ Request B
├── Stream 3 ─ Request C
└── Stream 4 ─ Request D
```

HTTP/2 will be covered separately.

---

# **8. Network Latency**

**Latency** is the time taken for an operation or communication to complete.

Network latency is only one component of total request latency.

For example:

```
Client
  ↓
Network
  ↓
Backend
  ↓
Database
  ↓
Backend
  ↓
Network
  ↓
Client
```

Total latency can include:

```
Network latency
+ Queueing
+ Application processing
+ Database latency
+ Downstream service latency
+ Response transmission
```

---

## **RTT — Round Trip Time**

RTT is approximately:

```
Client → Server → Client
```

It measures the round-trip communication time.

For example:

```
Client
  │
  │ Request
  ├──────────────────→
  │                   Server
  │
  │ Response
  ←───────────────────┤
```

If:

```
RTT ≈ 50 ms
```

a round trip between the two endpoints takes approximately 50 ms under the observed conditions.

---

## **Sequential Calls**

Suppose:

```
Service A
   ↓
Service B
   ↓
Service C
   ↓
Service D
```

The calls depend on each other.

Their latency contributes along the critical path:

```
Latency ≈ A + B + C + D
```

---

## **Parallel Calls**

If calls are independent:

```
             ┌──→ Service B
Service A ───┼──→ Service C
             └──→ Service D
```

They can potentially execute concurrently.

The completion time is closer to:

```
max(B, C, D)
```

rather than:

```
B + C + D
```

This is why understanding **dependencies and the critical path** is important in system design.

---

# **🗺️ Diagram**

## **Complete Networking Mental Model**

```
                         APPLICATION
                              │
                              │ HTTP
                              ▼
                    ┌──────────────────┐
                    │   HTTP / HTTPS   │
                    └────────┬─────────┘
                             │
                    HTTPS → TLS
                             │
                             ▼
                    ┌──────────────────┐
                    │    TCP / UDP     │
                    └────────┬─────────┘
                             │
                             ▼
                    ┌──────────────────┐
                    │        IP        │
                    └────────┬─────────┘
                             │
                             ▼
                          NETWORK
```

### **Typical HTTPS request**

```
Client
  │
  │ 1. Resolve server address
  │
  │ 2. TCP connection
  │
  │ 3. TLS handshake
  │
  │ 4. HTTP request
  ▼
Server
  │
  │ 5. HTTP response
  ▼
Client
```

---

# **💻 Code**

Networking concepts can be demonstrated using Java’s networking APIs.

### **TCP**

```
Socket socket = new Socket("example.com", 443);
```

Conceptually:

```
Socket
  ↓
TCP connection
  ↓
example.com:443
```

### **UDP**

```
DatagramSocket socket = new DatagramSocket();
```

The important distinction is not the exact Java syntax.

The important concept is:

```
Socket
   ↓
Transport protocol
   ↓
TCP / UDP
```

---

# **🏗️ Real Example**

Consider a user opening:

```
https://api.example.com/orders
```

A simplified flow is:

```
Browser
   │
   │ DNS
   ▼
IP Address
   │
   │ TCP
   ▼
TCP Connection
   │
   │ TLS
   ▼
Secure Connection
   │
   │ HTTP
   ▼
GET /orders
   │
   ▼
Backend
   │
   ├── Business Logic
   │
   ├── Database
   │
   └── Other Services
   │
   ▼
HTTP Response
   │
   ▼
Browser
```

The important mental model is:

**DNS finds the destination.**
 **IP provides addressing/routing.**
 **TCP provides reliable ordered transport.**
 **TLS provides security.**
 **HTTP provides application semantics.**

---

# **🎯 Interview Questions**

### **1. What is the difference between an IP address and a port?**

**Answer:**

An IP address identifies a network destination/interface, while a port identifies a transport-layer endpoint/service on that host.

Example:

```
192.168.1.10:8080
```

```
192.168.1.10 → IP
8080         → Port
```

---

### **2. Why do we need ports if we already have IP addresses?**

**Answer:**

A single host can run multiple network services.

For example:

```
Server
├── 443  → HTTPS
├── 3306 → MySQL
└── 6379 → Redis
```

The IP identifies the host/interface; the port identifies the transport endpoint.

---

### **3. Why does HTTP commonly run over TCP?**

**Answer:**

HTTP needs reliable, ordered delivery of its application data. TCP provides those transport guarantees.

HTTP itself does not implement TCP’s reliability mechanisms.

---

### **4. Does TCP understand HTTP?**

**Answer:**

No.

TCP sees a byte stream.

HTTP defines what those bytes mean at the application layer.

```
HTTP → application semantics
TCP  → reliable ordered byte transport
```

---

### **5. What happens before an HTTPS request is sent?**

**Answer:**

For a traditional HTTPS connection:

```
TCP handshake
      ↓
TLS handshake
      ↓
HTTP request
```

---

### **6. What does TLS provide that TCP doesn’t?**

**Answer:**

TLS provides:

- Confidentiality
- Integrity
- Authentication

TCP provides reliable transport but does not encrypt the application data.

---

### **7. Why don’t we perform a TCP handshake for every HTTP request?**

**Answer:**

Because persistent connections allow the same TCP connection to be reused for multiple requests.

```
TCP handshake
     ↓
Request 1
Request 2
Request 3
```

This reduces connection-establishment overhead.

---

### **8. Is connection reuse the same as HTTP/2 multiplexing?**

**Answer:**

No.

Connection reuse means multiple requests can use an existing connection.

HTTP/2 multiplexing allows multiple streams to be active concurrently over the same TCP connection.

---

### **9. What is RTT?**

**Answer:**

RTT, or Round Trip Time, is the time required for communication to travel from one endpoint to another and back.

```
A → B → A
```

---

### **10. Why can multiple sequential microservice calls increase latency?**

**Answer:**

Because dependent calls add to the critical path.

```
A → B → C → D
```

If each call takes time, the total latency approximately accumulates:

```
A + B + C + D
```

Independent calls can potentially execute in parallel.

---

### **11. Is UDP always faster than TCP?**

**Answer:**

No.

UDP has lower protocol overhead and doesn’t provide TCP’s reliability mechanisms, but actual application performance depends on the network, protocol design, packet loss, congestion, and application behavior.

---

### **12. Does HTTPS hide everything from a network observer?**

**Answer:**

No.

TLS protects application contents, but depending on the observation point, metadata such as IP addresses, ports, timing, packet sizes, and traffic patterns may still be observable.

---

# **⚠️ Mistakes / Gotchas**

### **❌ “IP identifies the application”**

Not exactly.

```
IP   → network destination/interface
Port → transport endpoint
```

---

### **❌ “Port identifies the application”**

Too simplistic.

A port identifies a transport-layer endpoint that a service/process can listen on.

---

### **❌ “TCP is slow and UDP is fast”**

Oversimplified.

TCP provides important guarantees that UDP does not provide.

---

### **❌ “HTTPS means encrypted TCP”**

More precisely:

```
HTTP
 ↓
TLS
 ↓
TCP
```

TLS protects the application data carried over TCP.

---

### **❌ “Every HTTP request requires a TCP handshake”**

No.

Persistent connections allow connection reuse.

---

### **❌ “Keep-alive means permanent connection”**

No.

Connections can eventually close due to timeout, configuration, errors, or resource management.

---

### **❌ “HTTP/2 multiplexing = connection reuse”**

Related, but different.

```
Connection reuse
→ reuse an existing connection

Multiplexing
→ multiple concurrent streams over one connection
```

---

### **❌ “Latency means network latency”**

Not necessarily.

End-to-end latency can include:

```
Network
+ Queueing
+ Application
+ Database
+ Downstream services
```

---

### **❌ “Adding more servers always reduces latency”**

Not necessarily.

If the bottleneck is:

```
Database
```

adding application servers may not solve the problem.

Always identify the bottleneck first.

---

# **🔑 Key Takeaways**

```
IP
↓
Identifies network destination/interface

Port
↓
Identifies transport endpoint

TCP
↓
Reliable + ordered byte transport

UDP
↓
Lightweight datagram transport without TCP-style guarantees

HTTP
↓
Application-level communication protocol

TLS
↓
Confidentiality + Integrity + Authentication

HTTPS
↓
HTTP secured with TLS

Keep-Alive
↓
Reuse existing connections

Latency
↓
Time taken for communication/operation
```

The most important mental model:

```
Application
    ↓
   HTTP
    ↓
   TLS       ← HTTPS
    ↓
   TCP
    ↓
    IP
    ↓
  Network
```

And for request latency:

```
Sequential dependencies
        ↓
Critical path
        ↓
More dependent work
        ↓
Higher latency
```

---

# **📚 References**

- RFC 9293 — Transmission Control Protocol
- RFC 768 — User Datagram Protocol
- RFC 9110 — HTTP Semantics
- RFC 8446 — TLS 1.3
- RFC 7540 — HTTP/2
- RFC 9114 — HTTP/3

---

# **🚧 Pending Topic**

These were discussed briefly but intentionally **not covered deeply yet** and should remain separate learning items:

- Wireshark hands-on
- `tcpdump` hands-on
- TCP packet/flag inspection
- `sudo tcpdump -i any -nn 'tcp[tcpflags] & (tcp-syn|tcp-ack) != 0'`
- HTTP/2
- HTTP/2 multiplexing
- HTTP/2 streams
- HTTP/2 vs HTTP/1.1
- HTTP/3
- QUIC
- HTTP/3 over QUIC/UDP
- HTTP/2 vs HTTP/3
- Practical packet inspection with Wireshark/tcpdump

**Note:** These are intentionally kept pending so the core networking mental model stays clean before moving into protocol-specific internals.