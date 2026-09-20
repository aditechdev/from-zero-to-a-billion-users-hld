# Reverse Proxy

![HLD](https://img.shields.io/badge/HLD-Architecture%20Fundamentals-blue)
![Reverse Proxy](https://img.shields.io/badge/Topic-Reverse%20Proxy-orange)
![TLS Termination](https://img.shields.io/badge/Concept-TLS%20Termination-green)
![Routing](https://img.shields.io/badge/Concept-Routing-purple)
![Caching](https://img.shields.io/badge/Concept-Caching-red)

---

## **❓ Problem**

Imagine an application starts with a single backend:

```
Client
   │
   ↓
Backend Server
```

As the system grows, we may have:

```
Client
   ├──→ App 1
   ├──→ App 2
   ├──→ App 3
   └──→ App 4
```

This creates several problems:

- Clients need a stable entry point.
- Backend instances may be added or removed.
- Clients shouldn’t need to know internal server addresses.
- Different URLs may need to reach different services.
- TLS termination may need to happen before the request reaches the application.
- Excessive traffic may need to be filtered.
- Some HTTP responses may be cacheable.
- Multiple backend instances may need load balancing.

A **reverse proxy** provides a controlled intermediary between clients and backend infrastructure.

```
Client
   ↓
Reverse Proxy
   ↓
Backend Infrastructure
```

The client interacts with a stable endpoint while the infrastructure behind that endpoint can change independently.

---

# **📋 Prerequisites**

Before learning Reverse Proxy, understand:

- Client-Server Architecture
- HTTP request/response
- IP addresses and ports
- TCP and HTTPS basics
- DNS
- Load Balancer
- Basic caching concepts

---

# **🧠 Theory**

## **1. Reverse Proxy**

A **reverse proxy** is a server that sits between clients and backend servers and forwards client requests to the appropriate backend.

```
Client
   │
   ↓
Reverse Proxy
   │
   ↓
Backend
```

The important idea isn’t simply:

“It forwards requests.”

The important architectural idea is:

**The reverse proxy provides a stable, controlled entry point between clients and backend infrastructure.**

This allows backend infrastructure to change without requiring clients to understand those changes.

---

# **2. What Problem Does a Reverse Proxy Solve?**

Consider:

```
Client
   ├──→ 10.0.1.10
   ├──→ 10.0.1.11
   ├──→ 10.0.1.12
   └──→ 10.0.1.13
```

The client now knows about backend infrastructure.

If tomorrow we add five more servers:

```
10.0.1.14
10.0.1.15
10.0.1.16
10.0.1.17
10.0.1.18
```

the client shouldn’t need to change.

Instead:

```
Client
   │
   │ api.example.com
   ↓
Reverse Proxy
   │
   ├──→ App 1
   ├──→ App 2
   ├──→ App 3
   └──→ App 4
```

The client only knows:

```
api.example.com
```

The reverse proxy manages the infrastructure behind it.

### **Core principle**

**Clients depend on a stable entry point; infrastructure can evolve behind that entry point.**

---

# **3. Forward Proxy vs Reverse Proxy**

The easiest way to remember the difference is:

**Forward proxy represents the client.**
 **Reverse proxy represents the server/backend.**

### **Forward Proxy**

```
Client
   ↓
Forward Proxy
   ↓
Internet
   ↓
External Server
```

The proxy is acting on behalf of the client.

Common use cases:

- Corporate Internet proxy
- Outbound traffic filtering
- Client-side privacy/control
- Restricting access to websites

### **Reverse Proxy**

```
Client
   ↓
Reverse Proxy
   ↓
Backend
```

The proxy sits in front of the server infrastructure.

Common use cases:

- Backend protection
- Routing
- TLS termination
- Load balancing
- Caching
- Rate limiting

### **Quick comparison**

|  | **Forward Proxy** | **Reverse Proxy** |
| --- | --- | --- |
| Represents | Client | Backend/server |
| Sits in front of | Clients | Servers |
| Hides | Client/network details | Backend infrastructure |
| Typical direction | Client → Proxy → Internet | Client → Proxy → Backend |

---

# **4. Client → Reverse Proxy → Backend**

A simple architecture:

```
Client
   │
   │ HTTPS
   ↓
Reverse Proxy
   │
   │ HTTP/HTTPS
   ↓
Backend
```

The client doesn’t need to know:

```
backend.internal:8080
```

It can simply call:

```
https://api.example.com
```

The reverse proxy determines what happens next.

---

# **5. Backend Protection**

A reverse proxy can provide a controlled boundary between the Internet and backend infrastructure.

Instead of:

```
Internet
   ├──→ App 1
   ├──→ App 2
   └──→ App 3
```

we can have:

```
Internet
   ↓
Reverse Proxy
   ↓
Private Backend Network
   ├──→ App 1
   ├──→ App 2
   └──→ App 3
```

The backend servers can be configured so that they aren’t directly reachable from arbitrary Internet traffic.

### **Important distinction**

Simply hiding a backend IP is **not security**.

Actual access control can involve:

- Firewalls
- Cloud security groups
- Private networking
- Network ACLs
- Authentication
- Authorization
- Service-level security

We’ll study these properly in the Security/Cloud Networking sections.

---

# **6. TLS Termination**

A reverse proxy can terminate TLS.

```
Client
   │
   │ HTTPS
   ↓
NGINX
   │
   │ HTTP/HTTPS
   ↓
Backend
```

The reverse proxy receives the encrypted HTTPS connection, performs the TLS handshake and decrypts the request.

This process is called:

**TLS termination**

The backend connection can then be HTTP or HTTPS depending on the security requirements of the architecture.

### **Important**

NGINX does **not** automatically terminate TLS simply because it is NGINX.

TLS termination is a role that must be configured.

---

# **7. Routing**

One of the most useful reverse-proxy capabilities is request routing.

Suppose an application consists of multiple services:

```
/api/users/*
/api/orders/*
/api/payments/*
```

The reverse proxy can route them:

```
                    Reverse Proxy
                         │
          ┌──────────────┼──────────────┐
          ↓              ↓              ↓
     /api/users     /api/orders    /api/payments
          ↓              ↓              ↓
     User Service   Order Service  Payment Service
```

The client still uses one public API domain:

```
api.example.com
```

instead of knowing the location of every service.

Routing can potentially use:

- Host
- Path
- HTTP method
- Headers
- Cookies
- Other request properties

---

# **8. Caching**

A reverse proxy can cache HTTP responses.

Without caching:

```
Client
   ↓
Reverse Proxy
   ↓
Backend
   ↓
Database
```

With caching:

```
Client
   ↓
Reverse Proxy
   │
   ├── Cache HIT  → Response
   │
   └── Cache MISS → Backend
```

### **Cache Hit**

The requested response is already available.

```
Client
   ↓
NGINX
   ↓
Cache HIT
   ↓
Response
```

The backend doesn’t need to process the request.

### **Cache Miss**

The response isn’t available in the cache.

```
Client
   ↓
NGINX
   ↓
Cache MISS
   ↓
Backend
```

The response may then be stored for subsequent requests.

---

## **What should we cache?**

Not everything is suitable for proxy caching.

Potentially cacheable:

```
GET /products/123
GET /articles/123
GET /public/config
```

Potentially unsafe or unsuitable:

```
GET /my-account
POST /transfer-money
```

Caching depends on:

- Whether the data is public/private
- HTTP method
- Cache-control headers
- Freshness requirements
- How frequently the data changes
- Whether stale data is acceptable

### **Cache invalidation**

Suppose:

```
Database → ₹75,000
Cache    → ₹80,000
```

The cache contains **stale data**.

This leads to the classic caching problem:

**How and when should cached data be invalidated?**

We’ll study cache invalidation, TTL, eviction, cache stampede, hot keys, etc. in the dedicated **Caching** section.

---

# **9. Reverse Proxy Cache vs Redis**

These are related but not interchangeable.

### **Reverse Proxy Cache**

```
Client
   ↓
NGINX
   ↓
HTTP Cache
   ↓
Backend
```

Primarily operates around the **HTTP/proxy layer**.

### **Redis**

```
Client
   ↓
Backend
   ↓
Redis
   ↓
Database
```

Redis is an application-accessible in-memory data store commonly used for:

- Application caching
- Sessions
- Counters
- Rate limiting
- Distributed locks
- Pub/Sub
- Temporary data

### **Mental model**

**NGINX cache → HTTP response caching**

**Redis → application-level shared data/cache**

---

# **10. CDN vs Reverse Proxy Cache vs Redis**

A large architecture might contain several caching layers:

```
Client
   ↓
CDN
   ↓
Reverse Proxy
   ↓
Backend
   ↓
Redis
   ↓
Database
```

They solve different problems.

| **Component** | **Primary purpose** |
| --- | --- |
| CDN | Serve cacheable content closer to users |
| Reverse Proxy Cache | Cache HTTP responses at the proxy/edge |
| Redis | Cache/application data for backend services |
| Database | Persistent source of truth |

Don’t add all of them automatically.

Each additional component brings:

- Cost
- Complexity
- Consistency concerns
- Operational overhead
- Debugging difficulty

---

# **11. Compression**

A reverse proxy can compress HTTP responses.

Suppose the backend generates:

```
1 MB JSON response
```

The reverse proxy can compress it before sending it to the client:

```
Backend
   │
   │ 1 MB
   ↓
NGINX
   │
   │ compressed response
   ↓
Client
```

Common compression technologies include:

- gzip
- Brotli

Compression can reduce:

- Network bandwidth
- Response size
- Transfer time

But compression requires CPU.

Therefore:

**Compression trades CPU cost for reduced network transfer.**

---

# **12. Reverse Proxy vs Load Balancer**

These concepts overlap heavily.

### **Load Balancer**

Primary question:

**Which healthy backend instance should receive this request?**

```
Load Balancer
      │
 ┌────┼────┐
 ↓    ↓    ↓
App1 App2 App3
```

### **Reverse Proxy**

Primary question:

**How should this incoming request be handled/proxied before reaching the backend?**

It can perform:

- Routing
- TLS termination
- Caching
- Compression
- Rate limiting
- Load balancing

Therefore:

**A reverse proxy can also perform load balancing.**

And a modern L7 load balancer can perform many reverse-proxy-like functions.

---

# **13. Why Don’t We Always Use Both?**

Consider:

```
Internet
   ↓
Cloud L7 Load Balancer
   ↓
NGINX
   ↓
App 1
App 2
App 3
```

You might ask:

Why have both?

The answer is **requirements**.

The cloud load balancer might handle:

- Highly available public entry point
- Health checks
- Traffic distribution
- Availability-zone balancing

NGINX might handle:

- Application-specific routing
- TLS termination
- Request manipulation
- Rate limiting
- Caching

But if the cloud load balancer already provides everything required, adding NGINX could introduce:

- An additional network hop
- Additional configuration
- More infrastructure to operate
- More failure/debugging points

### **HLD principle**

**Don’t add infrastructure because it exists. Add it because a requirement justifies it.**

---

# **14. NGINX as Reverse Proxy**

NGINX is a widely used web server and reverse-proxy solution.

It can perform multiple roles:

```
NGINX
├── Reverse Proxy
├── Load Balancer
├── TLS Termination
├── HTTP Routing
├── Caching
├── Compression
└── Rate Limiting
```

Therefore:

**NGINX is not synonymous with reverse proxy. It is software capable of performing the reverse-proxy role.**

---

# **15. Caddy as Reverse Proxy**

Caddy is another web server/reverse proxy.

Conceptually:

```
Client
   ↓
Caddy
   ↓
Backend
```

Caddy supports capabilities such as:

- Reverse proxying
- HTTPS/TLS
- Routing
- Load balancing
- Static file serving

Caddy is particularly known for simple configuration and automatic HTTPS certificate management.

For HLD, the important thing is understanding **why and where a reverse proxy is used**, rather than memorizing Caddy configuration syntax.

---

# **🗺️ Diagram**

## **Complete Reverse Proxy Architecture**

```
                              Internet
                                  │
                                  ↓
                           api.example.com
                                  │
                                  ↓
                     ┌─────────────────────┐
                     │   Reverse Proxy     │
                     │   NGINX / Caddy     │
                     ├─────────────────────┤
                     │ TLS Termination     │
                     │ Routing             │
                     │ Rate Limiting       │
                     │ Caching             │
                     │ Compression         │
                     │ Load Balancing      │
                     └──────────┬──────────┘
                                │
                 ┌──────────────┼──────────────┐
                 ↓              ↓              ↓
           User Service    Order Service   Payment Service
                 │              │              │
                 └──────────────┼──────────────┘
                                ↓
                              Redis
                                ↓
                            Database
```

---

## **Reverse Proxy + Multiple Instances**

```
                         Internet
                            │
                            ↓
                       Reverse Proxy
                            │
                  ┌─────────┼─────────┐
                  ↓         ↓         ↓
                App 1     App 2     App 3
                  │         │         │
                  └─────────┼─────────┘
                            ↓
                           DB
```

The client sees:

```
api.example.com
```

not:

```
10.0.1.10
10.0.1.11
10.0.1.12
```

---

## **Cache Hit / Miss**

```
                    Client
                       │
                       ↓
                 Reverse Proxy
                       │
                 ┌─────┴─────┐
                 │           │
              HIT           MISS
                 │           │
                 ↓           ↓
              Response     Backend
                             │
                             ↓
                          Response
                             │
                             ↓
                         Cache Store
```

---

# **💻 Code**

Configuration syntax is not the focus of this topic. The important part is understanding what the configuration is asking NGINX to do.

A conceptual NGINX configuration:

```
server {
    listen 80;

    location /users/ {
        proxy_pass http://user-service:8080;
    }

    location /orders/ {
        proxy_pass http://order-service:8080;
    }
}
```

This expresses:

```
/users/*  → User Service
/orders/* → Order Service
```

A load-balancing configuration could conceptually look like:

```
upstream backend {
    server app1:8080;
    server app2:8080;
    server app3:8080;
}

server {
    listen 80;

    location / {
        proxy_pass http://backend;
    }
}
```

The important HLD concept is:

```
                    NGINX
                      │
              ┌───────┼───────┐
              ↓       ↓       ↓
            App 1   App 2   App 3
```

NGINX can distribute requests among backend instances.

---

# **🏗️ Real Example**

## **NGINX**

NGINX is commonly used as an HTTP reverse proxy and load balancer in production architectures.

A typical architecture might look like:

```
Users
  ↓
DNS
  ↓
Load Balancer
  ↓
NGINX
  ↓
Application Servers
  ↓
Redis
  ↓
Database
```

NGINX can provide the HTTP-layer functionality needed between the public entry point and application services.

---

## **Caddy**

Caddy can fill a similar reverse-proxy role:

```
Users
  ↓
Caddy
  ↓
Backend Services
```

Its automatic HTTPS capabilities can simplify deployments where manually managing certificates would otherwise add operational work.

---

## 🎯 Interview Questions

## **Fundamentals**

### **1. What is a reverse proxy?**

A reverse proxy is a server that sits between clients and backend servers and forwards client requests to the appropriate backend. It provides a stable, controlled entry point and can handle concerns such as routing, TLS termination, caching, rate limiting, and load balancing.

---

### **2. What problem does a reverse proxy solve?**

It hides backend infrastructure behind a stable entry point and centralizes common concerns such as request routing, TLS termination, caching, rate limiting, and load balancing. This allows backend infrastructure to change without requiring changes on the client side.

---

### **3. Why shouldn’t clients directly communicate with backend instances?**

Clients shouldn’t need to know individual backend addresses or infrastructure details. A reverse proxy provides a stable endpoint while allowing servers to be added, removed, replaced, or relocated without affecting clients. It also provides a controlled boundary for traffic management and security.

---

### **4. Does a reverse proxy require multiple backend servers?**

No. A reverse proxy can sit in front of a single backend server. Multiple backend servers are only needed when the system requires capabilities such as horizontal scaling or load balancing.

```
Client
  ↓
Reverse Proxy
  ↓
Backend
```

---

### **5. What is the difference between a forward proxy and reverse proxy?**

A forward proxy represents the client, while a reverse proxy represents the backend servers.

```
Forward Proxy:

Client → Forward Proxy → Internet → Server

Reverse Proxy:

Client → Reverse Proxy → Backend
```

---

# **Architecture**

### **6. How does a reverse proxy protect backend infrastructure?**

It provides a controlled entry point and can prevent direct public access to backend servers when combined with private networking and firewall/security-group rules. It can also apply rate limiting, request filtering, TLS termination, and routing before traffic reaches the backend.

---

### **7. Is hiding a backend IP sufficient for security?**

No. Hiding an IP is not an actual security boundary. Backend access should also be controlled using mechanisms such as firewalls, security groups, private networking, network policies, authentication, and authorization.

---

### **8. Why might backend servers be placed in a private network?**

To prevent arbitrary Internet traffic from directly reaching backend servers. Only the intended entry point, such as a load balancer or reverse proxy, needs to be publicly accessible.

```
Internet
   ↓
Public Load Balancer / Reverse Proxy
   ↓
Private Network
   ↓
Backend Servers
```

---

### **9. What happens if the reverse proxy becomes a single point of failure?**

If there is only one reverse-proxy instance and it fails, incoming traffic may become unavailable. In production, we can use multiple proxy instances behind a highly available load-balancing setup or use a managed service that provides high availability.

```
                Load Balancer
                 /          \
                ↓            ↓
             Proxy 1      Proxy 2
```

---

### **10. Can a reverse proxy perform load balancing?**

Yes. A reverse proxy such as NGINX can distribute requests across multiple healthy backend instances. Reverse proxying and load balancing are different concepts, but the same component can perform both roles.

---

# **TLS**

### **11. What is TLS termination?**

TLS termination is the process where a component such as a reverse proxy receives the HTTPS connection, performs the TLS handshake, decrypts the traffic, and then forwards the request to the backend.

```
Client
  │ HTTPS
  ↓
NGINX
  │ HTTP/HTTPS
  ↓
Backend
```

---

### **12. Why might TLS terminate at the reverse proxy?**

It centralizes certificate management and TLS processing at the edge. The reverse proxy can decrypt the request and apply HTTP-level routing, security policies, caching, or other processing.

The connection from proxy to backend can still use HTTPS when required.

---

### **13. Does using NGINX automatically mean TLS is terminated there?**

No. NGINX supports TLS termination, but it must be explicitly configured with the required certificates and TLS settings.

---

### **14. Can the connection between reverse proxy and backend also use HTTPS?**

Yes. TLS can be terminated at the reverse proxy and the proxy can establish a separate HTTPS connection to the backend. This is useful when encryption is required across internal networks as well.

```
Client
  │ HTTPS
  ↓
Reverse Proxy
  │ HTTPS
  ↓
Backend
```

---

# **Routing**

### **15. How can a reverse proxy route requests to different services?**

It can inspect request information such as the hostname, URL path, HTTP method, headers, or cookies and forward the request to the appropriate backend service.

Example:

```
/api/users/*    → User Service
/api/orders/*   → Order Service
/api/payments/* → Payment Service
```

---

### **16. How would you route****`/users/*`****and****`/orders/*`****to different services?**

Configure path-based routing in the reverse proxy so requests matching `/users/*` are forwarded to the User Service and requests matching `/orders/*` are forwarded to the Order Service.

```
             Reverse Proxy
              /          \
             ↓            ↓
       /users/*       /orders/*
           ↓              ↓
      User Service   Order Service
```

---

### **17. Can routing be based on hostnames?**

Yes. A reverse proxy can route requests based on the HTTP `Host` header or hostname.

For example:

```
api.example.com     → API Service
admin.example.com   → Admin Service
```

---

### **18. Can routing be based on headers or cookies?**

Yes. An application-aware reverse proxy can use headers, cookies, or other HTTP request attributes to make routing decisions. This can be useful for things such as feature-based routing, tenant routing, or controlled deployments.

---

# **Caching**

### **19. What is a cache hit?**

A cache hit occurs when the requested data or response already exists in the cache, so it can be returned without going to the next layer.

```
Client → NGINX → Cache HIT → Response
```

---

### **20. What is a cache miss?**

A cache miss occurs when the requested data isn’t available in the cache, so the request continues to the next layer, such as the backend.

```
Client → NGINX → Cache MISS → Backend
```

---

### **21. Can a reverse proxy cache HTTP responses?**

Yes. A reverse proxy can cache suitable HTTP responses and return them directly for subsequent requests, reducing backend processing and potentially improving latency.

---

### **22. Why shouldn’t every API response be cached?**

Because some responses are user-specific, sensitive, frequently changing, or unsafe to reuse. Caching such responses can cause stale data or, worse, expose one user’s data to another user if caching is configured incorrectly.

---

### **23. What is stale data?**

Stale data is cached data that no longer represents the current state of the source of truth.

Example:

```
Database → ₹75,000
Cache    → ₹80,000
```

The cached ₹80,000 value is stale.

---

### **24. How is reverse-proxy caching different from Redis?**

Reverse-proxy caching primarily caches HTTP responses at the proxy/edge layer, while Redis is an application-accessible in-memory data store that can be used for application-level caching and shared state.

```
Reverse Proxy Cache:

Client → NGINX → Cache → Backend

Redis:

Client → Backend → Redis → Database
```

---

# **Load Balancing**

### **25. What is the difference between a reverse proxy and a load balancer?**

A reverse proxy is primarily an intermediary that handles and forwards incoming requests to backend infrastructure. A load balancer’s primary responsibility is distributing traffic across multiple backend instances. A reverse proxy can also perform load balancing, so the responsibilities can overlap.

---

### **26. Can NGINX be both?**

Yes. NGINX can act as a reverse proxy and distribute requests across multiple backend instances, effectively performing load balancing.

```
             NGINX
               │
       ┌───────┼───────┐
       ↓       ↓       ↓
     App 1   App 2   App 3
```

---

### **27. Why might an architecture use both a cloud load balancer and NGINX?**

They may have different responsibilities. A cloud load balancer might provide a highly available public entry point, health checks, and traffic distribution, while NGINX may handle application-specific routing, TLS termination, caching, rate limiting, or other HTTP-level concerns.

However, using both isn’t automatically necessary. The architecture should justify the additional component.

---

### **28. When would you avoid adding NGINX behind a cloud L7 load balancer?**

If the cloud L7 load balancer already provides all the required capabilities—such as routing, TLS termination, health checks, rate limiting, and load balancing—adding NGINX may only introduce another hop, additional complexity, and operational overhead.

---

# **Practical HLD**

### **29. Design a reverse-proxy layer for an e-commerce API.**

I would expose a stable API domain through a reverse proxy such as NGINX. It can terminate TLS, apply rate limiting, route requests to the appropriate services, and load balance across healthy instances.

```
                    Internet
                       │
                       ↓
                  api.shop.com
                       │
                       ↓
                     NGINX
                       │
          ┌────────────┼────────────┐
          ↓            ↓            ↓
       /users       /orders      /products
          ↓            ↓            ↓
      User Service Order Service Product Service
                       │
                       ↓
                     Redis
                       │
                       ↓
                    Database
```

---

### **30. How would you handle 10 backend instances behind NGINX?**

I would configure NGINX with an upstream/backend pool containing the 10 instances and use an appropriate load-balancing strategy such as Round Robin or Least Connections. I would also configure health checks or failure handling so unhealthy instances aren’t continuously sent traffic.

---

### **31. What happens if one backend instance becomes unhealthy?**

The proxy/load balancer should detect the unhealthy instance through health checks or failure thresholds and stop sending new traffic to it. It should continue checking the instance and add it back when it becomes healthy again.

---

### **32. How would you protect the backend from excessive requests?**

I would apply rate limiting at the edge, potentially at the reverse proxy or API gateway. For distributed rate limiting across multiple instances, a shared store such as Redis can be used when required.

```
Client
   ↓
NGINX
   ↓
Rate Limit
   ├── Exceeded → 429
   │
   └── Allowed
          ↓
       Backend
```

---

### **33. Where would you terminate TLS and why?**

I would typically terminate TLS at the edge, such as a load balancer or reverse proxy, because it centralizes certificate management and allows the component to perform HTTP-level processing. If internal traffic also needs encryption, I would use HTTPS between the proxy and backend as well.

```
Client
  │ HTTPS
  ↓
Load Balancer / NGINX
  │ HTTPS
  ↓
Backend
```

The exact location depends on the security and operational requirements.

---

# **🧠 30-Second Interview Revision**

If you’re short on time before an interview, remember this:

**A reverse proxy is an intermediary between clients and backend infrastructure. It provides a stable entry point and hides backend topology from clients. It can perform TLS termination, routing, caching, compression, rate limiting, and load balancing. NGINX and Caddy are examples of software that can act as reverse proxies. A reverse proxy and load balancer are not identical concepts, but their responsibilities can overlap. In production, backend servers are often kept private and exposed only through controlled infrastructure such as a load balancer or reverse proxy.**

### **Core mental model**

```
                 Client
                    │
                    ↓
            DNS / Public Endpoint
                    │
                    ↓
        Load Balancer / Reverse Proxy
                    │
       ┌────────────┼────────────┐
       ↓            ↓            ↓
     App 1        App 2        App 3
       │            │            │
       └────────────┼────────────┘
                    ↓
                  Redis
                    ↓
                   DB
```

This is the version I’d use for **interview revision**: question → concise answer → diagram where the diagram actually adds value.

---

# **⚠️ Mistakes / Gotchas**

### **1. ❌ “Reverse proxy = load balancer”**

Not exactly.

A reverse proxy **can perform load balancing**, but load balancing is only one possible capability.

---

### **2. ❌ “NGINX is a load balancer”**

Incomplete.

NGINX can be configured as:

- Reverse proxy
- Load balancer
- TLS terminator
- Cache
- HTTP router
- etc.

The **role depends on how it is deployed/configured**.

---

### **3. ❌ “Hiding the backend IP secures the backend”**

No.

Actual security requires controls such as:

- Firewall
- Security groups
- Private networking
- Authentication
- Authorization
- Network policies

---

### **4. ❌ “A reverse proxy requires multiple servers”**

No.

This is valid:

```
Client
   ↓
NGINX
   ↓
One Backend
```

Multiple servers are needed only if the architecture requires them.

---

### **5. ❌ “NGINX always terminates TLS”**

No.

TLS termination is a configuration/deployment choice.

---

### **6. ❌ “Reverse proxy caching = Redis”**

No.

```
NGINX Cache → HTTP/proxy layer
Redis       → Application/data layer
```

---

### **7. ❌ “Cache highly volatile data”**

Usually risky.

Frequently changing data has a higher chance of becoming stale and requiring frequent invalidation.

---

### **8. ❌ “More infrastructure = better architecture”**

Not necessarily.

Every component introduces:

- Complexity
- Cost
- Latency
- Operational overhead
- Potential failure points

A simpler architecture is often preferable when it satisfies the requirements.

---

### **9. ❌ “Every request should pass through NGINX”**

Not necessarily.

The architecture should determine which traffic requires the reverse-proxy layer and what responsibilities it needs to perform.

---

### **10. ⚠️ Reverse proxy can add latency**

It introduces an additional hop and processing overhead.

However, it can also reduce latency through:

- Caching
- Connection reuse
- Compression
- Efficient routing
- TLS termination

So the correct answer is:

**It can add latency, but the overall architecture may still be faster and more scalable because of the capabilities it provides.**

---

# **🔑 Key Takeaways**

1. **Reverse proxy = intermediary between clients and backend infrastructure.**
2. Its core value is providing a **stable, controlled entry point** .
3. Clients don’t need to know individual backend servers.
4. A reverse proxy does **not require multiple backend servers** .
5. Forward proxy represents the **client** ; reverse proxy represents the **server/backend** .
6. Reverse proxies can provide:
    - Routing
    - TLS termination
    - Rate limiting
    - Caching
    - Compression
    - Load balancing
7. **NGINX and Caddy are implementations that can act as reverse proxies.**
8. NGINX can simultaneously perform multiple infrastructure roles.
9. **Reverse proxy ≠ load balancer** , although a reverse proxy can perform load balancing.
10. **NGINX cache ≠ Redis.**
    - NGINX → HTTP/proxy-layer caching
    - Redis → application-level shared data/cache
11. Hiding backend addresses isn’t sufficient security. Network access controls matter.
12. Don’t add NGINX, Redis, CDN, or another component simply because it exists. **Every component should solve a real requirement.**
13. The HLD mindset is:

**What problem does this component solve, and what trade-off does introducing it create?**

---

# **📚 References**

Only use these when you want to go deeper into the actual tools:

- ⁠ NGINX Documentation
- ⁠ NGINX Reverse Proxy Guide
- ⁠ Caddy Documentation

---

# **🚧 Pending Topic**

The **Reverse Proxy** concept is complete for the current HLD scope.

### **Hands-on still pending**

These should be done as practical labs rather than more theory:

```
Reverse Proxy Hands-on
│
├── NGINX installation                    ⏳
├── Run a simple backend                  ⏳
├── NGINX → Backend proxy                 ⏳
├── Path-based routing                    ⏳
├── Multiple backend instances            ⏳
├── NGINX load balancing                  ⏳
├── Health/failure behavior               ⏳
├── Rate limiting                         ⏳
├── Response caching                      ⏳
├── Compression                           ⏳
└── TLS termination                       ⏳
```

### **Related topics intentionally deferred**

```
Cloud Networking
├── VPC
├── Subnets
├── Public vs Private Subnet
├── Firewall
├── Security Groups
├── Network ACLs
└── Private Backend Access

Caching
├── Cache Strategies
├── Cache-Aside
├── Read/Write Through
├── TTL
├── Eviction
├── Cache Invalidation
├── Cache Stampede
├── Cache Penetration
├── Cache Avalanche
└── Distributed Redis

Security
├── Authentication
├── Authorization
├── OAuth
├── JWT
└── API Security
```

These are **not skipped**; they’re intentionally placed in the sections where they’ll make more sense.

---

## **🧭 Architecture Fundamentals — Current Position**

```
02-ARCHITECTURE-FUNDAMENTALS
│
├── Client-Server Architecture        ✅
├── Request/Response Flow             ✅
├── 3-Layer Architecture              ✅
├── Networking Fundamentals           ✅
├── DNS                               ✅
├── Load Balancer                     ✅
└── Reverse Proxy                     ✅  ← CURRENT
```

**Next → API Gateway**

The natural question we’ll answer there is:

**If a reverse proxy can already route, terminate TLS, rate-limit, and load-balance, what additional problem does an API Gateway solve?**