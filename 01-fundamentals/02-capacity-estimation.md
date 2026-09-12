
# 📊 Capacity Estimation — Traffic Estimation

![Capacity Estimation](https://img.shields.io/badge/-Capacity%20Estimation-6366f1)
![Traffic Estimation](https://img.shields.io/badge/-Traffic%20Estimation-6366f1)
![RPS](https://img.shields.io/badge/-RPS-6366f1)
![Read vs Write Traffic](https://img.shields.io/badge/-Read%20vs%20Write%20Traffic-6366f1)
![Peak RPS](https://img.shields.io/badge/-Peak%20RPS-6366f1)

## 🧠 What is Capacity Estimation?
Capacity estimation is the process of estimating how much **traffic, storage, and computing capacity** a system needs.

For traffic estimation, we mainly calculate:
- Requests per day
- Average RPS
- Peak RPS
- Read vs Write traffic

Capacity estimation helps us understand the scale of the system before designing the architecture.

---

## 📈 Traffic Estimation
Traffic estimation answers:
> "How much traffic will our system need to handle?"

A useful flow is:
```text
Users / Events
      ↓
Requests per day
      ↓
Average RPS
      ↓
Peak RPS
      ↓
Read / Write traffic
```

---

## 1️⃣ Requests per Day
The first step is estimating how many requests the system receives in a day.

For example:
```text
100 Million requests/day
```

This does not mean the system receives the same number of requests every second.
Traffic varies throughout the day.

```text
Morning       ████
Afternoon     ███████
Evening       ██████████
Night         ███
```

So we calculate average traffic first and then estimate peak traffic.

---

## 2️⃣ Average RPS
RPS = Requests Per Second

```text
1 day = 24 × 60 × 60
      = 86,400 seconds
```

Therefore:
```text
Average RPS = Requests per day / 86,400
```

**Example**
```text
100 Million requests/day
100,000,000 / 86,400
≈ 1,157 RPS
```

So: **Average RPS ≈ 1.2K**

**Interview Shortcut**

For quick estimation:
```text
1 day ≈ 100,000 seconds
```

Therefore:
```text
100 Million requests/day
≈ 100,000,000 / 100,000
≈ 1,000 RPS
```

This is useful for mental calculations during interviews.

---

## 3️⃣ Peak RPS
Real-world traffic is not uniform. There will be periods where traffic is much higher than the average.

For estimation exercises, we can assume:
```text
Peak RPS = Average RPS × Peak Factor
```

For example, with a 3× peak factor:
```text
Average RPS = 1,157
Peak RPS = 1,157 × 3
         ≈ 3,471 RPS
```

So: **Average ≈ 1.2K RPS · Peak ≈ 3.5K RPS**

⚠️ **Important:** 3× is an assumption, not a universal rule. In an interview, clearly state the assumption:
> "I'll assume peak traffic is approximately 3× average traffic."

---

## 4️⃣ Read vs Write Traffic
Not every request performs the same operation.

Some requests **write** data: Create, Update, Upload, Send message
Others **read** data: Fetch, View, Search, Get profile

We should estimate the ratio. For example:
```text
Read : Write = 100 : 1
```
This means 100 reads for every 1 write — the system is **read-heavy**.

Another example:
```text
Read : Write = 1 : 1
```
This means the workload is balanced.

**Why does this matter?** Read-heavy and write-heavy systems have different bottlenecks.

```text
Read-heavy  → Large number of data reads   → Need to handle read scalability
Write-heavy → Large number of data writes  → Database write capacity becomes important
```

We should understand the workload before choosing solutions such as caching, replicas, sharding, or other architecture components.

---

## 🔗 URL Shortener — Capacity Estimation
Let's estimate the traffic for a URL shortener.

**Assumption:** 100 Million URLs created per day. Each created URL is a write.

**Write RPS**
```text
100,000,000 / 86,400
≈ 1,157 RPS
```
Assuming 3× peak:
```text
1,157 × 3
≈ 3,471 RPS
```
Write traffic: **Average ≈ 1.2K RPS · Peak ≈ 3.5K RPS**

---

## 🔗 Redirect Traffic
Now assume every URL receives 100 redirects/day.

```text
100 Million URLs × 100 redirects
= 10 Billion redirects/day
```

Average read RPS:
```text
10,000,000,000 / 86,400
≈ 115,741 RPS
```
So: **Average ≈ 116K RPS**

With a 3× peak assumption:
```text
115,741 × 3
≈ 347,223 RPS
```
**Peak ≈ 347K RPS**

---

## 📊 URL Shortener Workload
```text
                URL Shortener
        ┌─────────────────────────┐
        │     Create URL           │
        │     Write Operation      │
        │     ~1.2K avg RPS        │
        │     ~3.5K peak RPS       │
        └────────────┬────────────┘
                      ↓
                   Database
        ┌─────────────────────────┐
        │     Redirect URL         │
        │     Read Operation       │
        │     ~116K avg RPS        │
        │     ~347K peak RPS       │
        └────────────┬────────────┘
                      ↓
                   Database
```

This tells us something important: **the URL shortener is heavily read-dominated.** The redirect
traffic is much larger than URL-creation traffic. That observation will later help us reason about
architecture and bottlenecks.

---

## 🧠 Important HLD Principle
Don't jump directly from traffic numbers to technologies. First understand:

```text
Requirements
     ↓
Workload
     ↓
Traffic
     ↓
Bottlenecks
     ↓
Possible Solutions
     ↓
Trade-offs
     ↓
Architecture
```

Capacity estimation gives us the numbers needed to reason about the architecture.

---

## 🎯 Interview Questions
**Q: What is RPS?**
A: RPS means Requests Per Second. It represents how many requests a system handles per second.

**Q: How do you calculate average RPS?**
A: `Average RPS = Requests per day / 86,400`

**Q: Why do we calculate peak RPS?**
A: Because traffic is not uniform throughout the day. Systems must be designed to handle periods
of significantly higher traffic than the average.

**Q: Why is read/write ratio important?**
A: It tells us the workload characteristics of the system. A read-heavy system and a write-heavy
system can have very different scalability and bottleneck considerations.

**Q: In the URL shortener example, which operation dominates?**
A: Reads. With 100 redirects per created URL per day, redirect traffic is much larger than
URL-creation traffic.

---

## ⚠️ Common Mistakes
- Confusing users with requests
- Forgetting that one user can generate many requests
- Using requests/day directly as RPS
- Forgetting to divide by 86,400
- Treating average traffic as peak traffic
- Assuming 3× peak is a universal rule
- Ignoring read/write ratio
- Choosing technologies before understanding the workload

---

## 🔑 Key Takeaways
- Capacity estimation helps us understand the scale of a system.
- RPS means Requests Per Second.
- Average RPS = requests/day ÷ 86,400.
- Peak RPS requires an explicit peak assumption.
- Users are not the same as requests.
- Read/write ratio helps identify workload characteristics.
- URL shorteners are typically heavily read-dominated.
- Capacity estimation should come before architecture decisions.

## 🔁 Revision Log
- 2026-09-12: First written
