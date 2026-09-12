# 📊 Capacity Estimation — Traffic Estimation
![Capacity Estimation](https://img.shields.io/badge/-Capacity%20Estimation-6366f1)
![Traffic Estimation](https://img.shields.io/badge/-Traffic%20Estimation-6366f1)
![RPS](https://img.shields.io/badge/-RPS-6366f1)
![Read vs Write Traffic](https://img.shields.io/badge/-Read%20vs%20Write%20Traffic-6366f1)
![Peak RPS](https://img.shields.io/badge/-Peak%20RPS-6366f1)
---
## ❓ Problem
Before designing a system, we need to understand **how much traffic the system needs to handle**.
Without traffic estimation, we don't know:
- How many requests the system receives
- How many requests arrive per second
- How much traffic occurs during peak periods
- Whether the workload is read-heavy or write-heavy
- Where potential bottlenecks may appear
Capacity estimation gives us the numbers needed to reason about **scalability, bottlenecks, and architecture**.
---
## 🧠 Theory
### What is Capacity Estimation?
Capacity estimation is the process of estimating how much **traffic, storage, and computing capacity** a system needs.
For traffic estimation, we mainly calculate:
- Requests per day
- Average RPS
- Peak RPS
- Read vs Write traffic
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

⸻

1️⃣ Requests per Day

The first step is estimating how many requests the system receives in a day.

For example:

100 Million requests/day

This does not mean the system receives the same number of requests every second.

Traffic varies throughout the day:

Morning       ████
Afternoon     ███████
Evening       ██████████
Night         ███

So we calculate average traffic first and then estimate peak traffic.

⸻

2️⃣ Average RPS

RPS = Requests Per Second

There are:

1 day = 24 × 60 × 60
      = 86,400 seconds

Therefore:

Average RPS = Requests per day / 86,400

Example

100 Million requests/day
100,000,000 / 86,400
≈ 1,157 RPS

So:

Average RPS ≈ 1.2K

⚡ Interview Shortcut

For quick mental estimation:

1 day ≈ 100,000 seconds

Therefore:

100 Million requests/day
≈ 100,000,000 / 100,000
≈ 1,000 RPS

This shortcut is useful during system design interviews when exact precision is not required.

⸻

3️⃣ Peak RPS

Real-world traffic is not uniform.

There will be periods where traffic is much higher than the average.

For estimation exercises, we can assume:

Peak RPS = Average RPS × Peak Factor

For example, with a 3× peak factor:

Average RPS = 1,157
Peak RPS = 1,157 × 3
         ≈ 3,471 RPS

So:

Average ≈ 1.2K RPS
Peak    ≈ 3.5K RPS

⚠️ Important: 3× is an assumption, not a universal rule.

In an interview, clearly state the assumption:

“I’ll assume peak traffic is approximately 3× average traffic.”

The actual peak factor depends on the system and its traffic pattern.

⸻

4️⃣ Read vs Write Traffic

Not every request performs the same operation.

Some requests write data:

* Create
* Update
* Upload
* Send message

Other requests read data:

* Fetch
* View
* Search
* Get profile

We should estimate the ratio between reads and writes.

For example:

Read : Write = 100 : 1

This means:

100 reads
1 write

The system is read-heavy.

Another example:

Read : Write = 1 : 1

This means the workload is approximately balanced.

Why does this matter?

Read-heavy and write-heavy systems can have very different bottlenecks.

Read-heavy
    ↓
Large number of data reads
    ↓
Read scalability becomes important
Write-heavy
    ↓
Large number of data writes
    ↓
Database write capacity becomes important

We should understand the workload before choosing solutions such as:

* Caching
* Read replicas
* Sharding
* Database scaling
* Other architecture components

⸻

🗺️ Diagram

                  Capacity Estimation
                         │
                         ↓
                  Traffic Estimation
                         │
          ┌──────────────┼──────────────┐
          ↓              ↓              ↓
   Requests/Day     Average RPS     Read/Write
                         │
                         ↓
                     Peak RPS
                         │
                         ↓
                  Identify Workload
                         │
                         ↓
                 Find Bottlenecks
                         │
                         ↓
              Make Architecture Decisions

⸻

🏗️ Real Example

🔗 URL Shortener

Let’s estimate the traffic for a URL shortener.

Assume:

100 Million URLs created per day

Each created URL is a write.

Write RPS

100,000,000 / 86,400
≈ 1,157 RPS

Assuming a 3× peak:

1,157 × 3
≈ 3,471 RPS

Therefore:

Write Traffic
Average ≈ 1.2K RPS
Peak    ≈ 3.5K RPS

⸻

🔗 Redirect Traffic

Now assume every URL receives:

100 redirects/day

Therefore:

100 Million URLs × 100 redirects
= 10 Billion redirects/day

Average read RPS:

10,000,000,000 / 86,400
≈ 115,741 RPS

So:

Average ≈ 116K RPS

With a 3× peak assumption:

115,741 × 3
≈ 347,223 RPS

Therefore:

Peak ≈ 347K RPS

📊 URL Shortener Workload

                    URL Shortener
                         │
          ┌──────────────┴──────────────┐
          ↓                             ↓
     Create URL                    Redirect URL
       WRITE                          READ
         │                              │
    ~1.2K avg RPS                 ~116K avg RPS
    ~3.5K peak RPS                ~347K peak RPS
         │                              │
         └──────────────┬───────────────┘
                        ↓
                     Database

This tells us something important:

The URL shortener is heavily read-dominated.

Redirect traffic is much larger than URL-creation traffic.

This observation will later help us reason about architecture, scalability, and bottlenecks.

⸻

🧠 Important HLD Principle

Don’t jump directly from traffic numbers to technologies.

First understand:

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

Requirements drive architecture.

Capacity estimation gives us the numbers needed to reason about the system before making architecture decisions.

⸻

🎯 Interview Questions

Q: What is RPS?

A: RPS means Requests Per Second. It represents how many requests a system handles per second.

⸻

Q: How do you calculate average RPS?

A:

Average RPS = Requests per day / 86,400

⸻

Q: Why do we calculate peak RPS?

A: Because traffic is not uniform throughout the day. Systems need to handle periods where traffic is significantly higher than the average.

⸻

Q: Is 3× always the correct peak factor?

A: No. 3× is only an estimation assumption. The actual peak factor depends on the system’s traffic pattern and requirements.

⸻

Q: Why is the read/write ratio important?

A: It tells us the workload characteristics of the system. Read-heavy and write-heavy systems can have very different scalability requirements and bottlenecks.

⸻

Q: In the URL Shortener example, which operation dominates?

A: Reads. With 100 redirects per created URL per day, redirect traffic is much larger than URL-creation traffic.

⸻

Q: Are users and requests the same thing?

A: No. One user can generate many requests. Therefore, user count alone is not enough to estimate system traffic.

⸻

⚠️ Mistakes / Gotchas

* Confusing users with requests
* Forgetting that one user can generate multiple requests
* Using requests/day directly as RPS
* Forgetting to divide by 86,400
* Treating average traffic as peak traffic
* Assuming 3× peak is a universal rule
* Ignoring the read/write ratio
* Choosing technologies before understanding the workload
* Focusing on exact numbers instead of clearly stating reasonable assumptions

⸻

🔑 Key Takeaways

* Capacity estimation helps us understand the scale of a system.
* Traffic estimation helps determine how much request load a system must handle.
* RPS means Requests Per Second.
* Average RPS = requests/day ÷ 86,400.
* Peak RPS requires an explicit assumption.
* Users are not the same as requests.
* Read/write ratio helps identify workload characteristics.
* URL shorteners are typically heavily read-dominated.
* Capacity estimation should come before major architecture decisions.

