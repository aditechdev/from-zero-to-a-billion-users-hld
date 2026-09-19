# **DNS — Domain Name System**

## **❓ Problem**

Computers communicate using network addresses such as:

```
203.0.113.20
```

Humans don’t want to remember IP addresses for every service they use.

We want to access services using names:

```
api.example.com
google.com
github.com
```

But the network ultimately needs an address to establish communication.

DNS solves this problem by providing a **distributed naming system** that resolves domain names into DNS records such as IP addresses.

```
api.example.com
        ↓
       DNS
        ↓
203.0.113.20
```

The important point:

**DNS does not simply “convert a domain into an IP.” It resolves a name into DNS records. An A record can provide an IPv4 address, an AAAA record an IPv6 address, a CNAME another hostname, and NS records identify authoritative nameservers.**

---

## **📋 Prerequisites**

Before learning DNS, understand:

- IP Address & Port
- TCP vs UDP
- TCP Connection
- HTTP
- HTTPS / TLS
- Network Latency
- Client-Server Architecture
- Request / Response Flow

---

# **🧠 Theory**

## **1. What Problem Does DNS Solve?**

Without DNS:

```
Browser
   ↓
203.0.113.20
```

With DNS:

```
Browser
   ↓
api.example.com
   ↓
DNS
   ↓
203.0.113.20
```

DNS gives applications a **stable naming abstraction** over potentially changing infrastructure.

For example:

```
api.example.com
       ↓
Old server: 203.0.113.20
```

Later:

```
api.example.com
       ↓
New server: 203.0.113.50
```

The client can continue using:

```
api.example.com
```

without needing to know the new IP.

---

# **2. Domain Name vs IP Address**

### **Domain name**

Human-friendly:

```
api.example.com
```

### **IP address**

Network address:

```
203.0.113.20
```

Think:

```
Human
  ↓
api.example.com
  ↓
DNS
  ↓
203.0.113.20
  ↓
Network
```

A domain name is not itself an IP address.

It is a name that DNS can resolve into one or more records.

---

## **Domain hierarchy**

Consider:

```
api.example.com.
```

Read it from right to left:

```
api.example.com.
│   │       │  │
│   │       │  └── Root
│   │       └──── TLD: com
│   └──────────── Domain label: example
└──────────────── Host/subdomain label: api
```

Conceptually:

```
.
└── com
    └── example
        └── api
```

For:

```
www.example.co.in
```

the hierarchy is:

```
.
└── in
    └── co
        └── example
            └── www
```

`in` is the country-code TLD (`ccTLD`).

`co.in` is a delegated domain structure commonly used for commercial domains in India.

### **Important**

Don’t memorize:

“The third part is always the TLD.”

DNS is hierarchical, and the meaning of labels depends on the domain hierarchy.

---

# **3. DNS Resolution**

The next question is:

When I type `api.example.com`, how does my computer actually find its IP?

A simplified flow:

```
Client
  ↓
Recursive Resolver
  ↓
Root
  ↓
TLD
  ↓
Authoritative DNS
  ↓
DNS Record
  ↓
IP Address
```

For example:

```
api.example.com
       ↓
203.0.113.20
```

The client normally doesn’t perform the entire hierarchy itself.

It asks a **recursive DNS resolver**.

---

# **4. Recursive Resolver**

A recursive resolver finds DNS answers on behalf of clients.

Your ISP may operate a recursive resolver, but it doesn’t have to.

You can use public recursive resolvers such as:

```
8.8.8.8
```

operated by Google, or:

```
1.1.1.1
```

operated by Cloudflare.

So:

```
              Recursive Resolvers

ISP DNS Resolver
       │
       ├── 8.8.8.8
       │
       ├── 1.1.1.1
       │
       └── Other DNS providers
```

A recursive resolver typically:

1. Receives the client’s DNS query.
2. Checks its cache.
3. If necessary, queries the DNS hierarchy.
4. Obtains the answer.
5. Caches the result according to TTL.
6. Returns the answer to the client.

### **Mental model**

**Client asks the recursive resolver to find the answer.**

---

# **5. Root DNS Server**

At the top of the DNS hierarchy is the **DNS root**:

```
.
```

The root doesn’t normally know:

```
api.example.com → 203.0.113.20
```

Instead, it knows where to find TLD infrastructure.

For example:

```
Root
 ├── .com
 ├── .org
 ├── .net
 └── .in
```

If the resolver asks:

```
"Where should I look for .com?"
```

the Root provides information about the `.com` TLD servers.

### **Mental model**

**Root → tells you where the TLD is.**

---

# **6. TLD DNS Server**

TLD means:

**Top-Level Domain**

Examples:

```
.com
.org
.net
.in
.uk
```

Suppose the resolver asks `.com`:

```
"Who handles example.com?"
```

The `.com` TLD infrastructure provides delegation information identifying the authoritative nameservers for `example.com`.

Conceptually:

```
.com
 │
 └── example.com
        ↓
    ns1.example-dns.com
    ns2.example-dns.com
```

### **Mental model**

**TLD → tells you where the domain’s authoritative DNS servers are.**

The TLD generally does **not** provide:

```
api.example.com → 203.0.113.20
```

---

# **7. Authoritative DNS Server**

The authoritative DNS server is responsible for DNS records in its zone.

For example:

```
api.example.com
        ↓
203.0.113.20
```

or:

```
www.example.com
        ↓
203.0.113.10
```

A real company can use a DNS provider such as:

- Cloudflare
- Amazon Route 53
- Google Cloud DNS
- GoDaddy
- Namecheap

These providers can host authoritative DNS for domains.

### **Important distinction**

**Registrar:**

Manages domain registration.

**Authoritative DNS provider:**

Hosts DNS records.

**Recursive DNS resolver:**

Finds DNS records on behalf of clients.

A company can provide more than one of these roles.

---

# **8. Delegation**

Delegation is the mechanism by which responsibility for a DNS zone is assigned to authoritative nameservers.

For example:

```
.com
  │
  │ delegation
  ▼
example.com
  │
  ├── ns1.cloudflare.com
  └── ns2.cloudflare.com
```

The `.com` infrastructure effectively says:

“For `example.com`, these nameservers are authoritative.”

This allows DNS to be distributed instead of having one enormous global database.

---

# **9. DNS Caching**

If every DNS query followed:

```
Root
 ↓
TLD
 ↓
Authoritative
```

the Internet would generate enormous DNS traffic.

Recursive resolvers therefore cache DNS answers.

Example:

```
api.example.com
        ↓
203.0.113.20
TTL = 300
```

A resolver can cache:

```
api.example.com → 203.0.113.20
```

and answer subsequent queries directly.

```
Client
  ↓
Recursive Resolver
  ↓
Cache HIT
  ↓
203.0.113.20
```

No Root/TLD/Authoritative lookup is required for that query.

---

# **10. TTL — Time To Live**

TTL controls how long a DNS answer can be cached.

Example:

```
api.example.com
A
203.0.113.20
TTL = 300
```

`300` seconds = 5 minutes.

After the TTL expires, the cached copy can no longer be reused as a fresh answer and the resolver needs to obtain fresh information.

### **TTL does NOT mean:**

“The authoritative DNS record disappears after 300 seconds.”

The authoritative record remains.

Only the cached copy expires.

---

## **TTL trade-off**

### **High TTL**

```
TTL = 86400
```

Advantages:

```
Long cache lifetime
      ↓
Fewer DNS queries
      ↓
Lower DNS traffic
```

Disadvantage:

```
DNS changes
      ↓
Old cached information may remain longer
```

### **Low TTL**

```
TTL = 60
```

Advantages:

```
Short cache lifetime
      ↓
Fresher DNS information
```

Disadvantage:

```
More cache misses
      ↓
More DNS queries
```

So:

**TTL is a freshness vs caching-efficiency trade-off.**

---

# **11. What happens when an IP changes?**

Suppose:

```
api.example.com
    ↓
203.0.113.10
TTL = 300
```

A recursive resolver caches it.

You then change the authoritative record:

```
api.example.com
    ↓
203.0.113.20
```

The resolver may still return:

```
203.0.113.10
```

until its cached record expires.

After expiration:

```
Cache expired
     ↓
Fresh DNS lookup
     ↓
Authoritative DNS
     ↓
203.0.113.20
```

Different recursive resolvers may therefore temporarily have different cached answers.

### **Important**

There is no central DNS server pushing your new IP to every resolver.

Resolvers refresh their cached information independently.

---

# **12. DNS Record Types**

DNS doesn’t only store IP addresses.

The most important records for this roadmap are:

```
A
AAAA
CNAME
NS
```

---

## **A Record**

Maps a hostname to an IPv4 address.

```
api.example.com
        ↓
A
        ↓
203.0.113.20
```

Example:

```
api.example.com. 300 IN A 203.0.113.20
```

Mental model:

```
A
↓
Hostname → IPv4
```

---

# **13. AAAA Record**

Maps a hostname to an IPv6 address.

Example:

```
api.example.com
        ↓
AAAA
        ↓
2001:db8::20
```

Mental model:

```
AAAA
 ↓
Hostname → IPv6
```

A hostname can have both:

```
api.example.com

A
→ 203.0.113.20

AAAA
→ 2001:db8::20
```

---

# **14. Why IPv6 Exists**

IPv4 uses:

```
32 bits
```

Therefore:

```
2³² ≈ 4.3 billion
```

possible addresses.

The Internet eventually needed a much larger address space.

IPv6 uses:

```
128 bits
```

Therefore:

```
2¹²⁸
```

possible addresses.

Approximately:

```
3.4 × 10³⁸
```

This is vastly larger.

### **IPv4**

```
192.168.1.10
```

### **IPv6**

```
2001:db8:85a3::8a2e:370:7334
```

### **Why didn’t IPv4 immediately disappear?**

Techniques such as **NAT (Network Address Translation)** allowed many private devices to share a public IPv4 address.

```
Laptop → 192.168.1.10 ─┐
Phone  → 192.168.1.11 ─┤
TV     → 192.168.1.12 ─┤
                       ▼
                     Router
                       │
                      NAT
                       │
                       ▼
                 Public IPv4
```

NAT didn’t increase the IPv4 address space; it reduced the number of public IPv4 addresses required.

---

# **15. CNAME Record**

CNAME means:

**Canonical Name**

A CNAME maps one hostname to another hostname.

```
www.example.com
       ↓
    CNAME
       ↓
example.com
```

Suppose:

```
example.com
    ↓
A
    ↓
203.0.113.20
```

Then:

```
www.example.com
       ↓
CNAME
       ↓
example.com
       ↓
A
       ↓
203.0.113.20
```

### **CNAME ≠ IP mapping**

Remember:

```
A
↓
Hostname → IPv4

AAAA
↓
Hostname → IPv6

CNAME
↓
Hostname → Hostname
```

---

# **16. Same IP ≠ Same Application**

Multiple hostnames can eventually resolve to the same IP:

```
www.aditechdev.com       ─┐
devapi.aditechdev.com    ─┤
preprod.aditechdev.com   ─┤
prod.aditechdev.com      ─┘
                           ↓
                     203.0.113.10
```

That does **not** mean the server cannot distinguish them.

The HTTP request contains the hostname:

```
GET /orders HTTP/1.1
Host: prod.aditechdev.com
```

A load balancer/reverse proxy can route based on the hostname:

```
203.0.113.10
      ↓
Load Balancer
      │
      ├── Host: devapi.aditechdev.com
      │       ↓
      │     Dev API
      │
      ├── Host: preprod.aditechdev.com
      │       ↓
      │     Preprod API
      │
      └── Host: prod.aditechdev.com
              ↓
            Prod API
```

This is commonly called **host-based routing / virtual hosting**.

With HTTPS, TLS can also provide the hostname through **SNI (Server Name Indication)** during the TLS handshake.

### **Important HLD distinction**

```
DNS
 ↓
Gets the client to the infrastructure/IP

Load Balancer / Reverse Proxy
 ↓
Routes to the correct service
```

DNS does not perform application-level routing.

---

# **17. NS Record**

NS means:

**Name Server**

An NS record identifies authoritative nameservers for a DNS zone.

Example:

```
aditechdev.com
        ↓
NS
        ↓
ns1.cloudflare.com
ns2.cloudflare.com
```

It answers:

**“Which nameservers are authoritative for this zone?”**

Compare:

```
A
↓
Hostname → IPv4

AAAA
↓
Hostname → IPv6

CNAME
↓
Hostname → Hostname

NS
↓
Zone → Authoritative nameservers
```

### **NS does NOT contain every domain on the Internet**

It is scoped to a particular zone.

Conceptually:

```
aditechdev.com
│
├── NS → ns1.cloudflare.com
├── NS → ns2.cloudflare.com
│
├── A
├── AAAA
├── CNAME
└── ...
```

---

# **18. Recursive vs Iterative Resolution**

These terms describe **how the lookup is handled**.

### **Recursive**

The client tells the recursive resolver:

“Find the answer for me.”

```
Client
  ↓
Recursive Resolver
  ↓
"I'll find it."
```

### **Iterative**

The resolver asks DNS servers one at a time.

```
Resolver → Root
              ↓
          "Ask .com"

Resolver → .com
              ↓
          "Ask authoritative"

Resolver → Authoritative
              ↓
          "Here is the answer"
```

The key mental model:

```
Client
  │
  │ Recursive query
  ▼
Recursive Resolver
  │
  │ Iterative queries
  ├────→ Root
  ├────→ TLD
  └────→ Authoritative
```

Don’t memorize:

Recursive = resolver, Iterative = authoritative.

That’s too simplistic.

Recursive/iterative describe the **query behavior**.

---

# **🗺️ Diagram**

## **Complete DNS Resolution**

```
                         DNS

Client / Browser
       │
       │ api.example.com?
       ▼
Recursive Resolver
       │
       │ Cache?
       │
   ┌───┴────┐
   │        │
  HIT      MISS
   │        │
   ▼        ▼
  IP       Root
            │
            │ .com
            ▼
          .com TLD
            │
            │ delegation
            ▼
      Authoritative DNS
            │
            │ A / AAAA / CNAME
            ▼
         DNS Answer
            │
            ▼
      Recursive Resolver
            │
        Cache result
            │
            ▼
          Client
```

---

# **🏗️ Real Example —****`api.aditechdev.com`**

Assume:

```
Domain:
aditechdev.com

Authoritative DNS:
Cloudflare

Nameservers:
ns1.cloudflare.com
ns2.cloudflare.com
```

DNS records:

```
api.aditechdev.com
    A → 203.0.113.20
    TTL → 300

www.aditechdev.com
    CNAME → aditechdev.com
```

A user opens:

```
https://api.aditechdev.com/orders/123
```

### **Step 1 — DNS**

```
Browser
   ↓
1.1.1.1
```

Assume cache miss.

### **Step 2 — Root**

```
1.1.1.1
   ↓
Root
   ↓
.com TLD information
```

### **Step 3 — TLD**

```
1.1.1.1
   ↓
.com TLD
   ↓
ns1.cloudflare.com
ns2.cloudflare.com
```

### **Step 4 — Authoritative DNS**

```
1.1.1.1
   ↓
Cloudflare Authoritative DNS
   ↓
api.aditechdev.com
   ↓
203.0.113.20
```

### **Step 5 — Cache**

```
1.1.1.1
   ↓
Cache:
api.aditechdev.com
→ 203.0.113.20
→ TTL 300
```

### **Step 6 — Network request**

```
Browser
   ↓
203.0.113.20:443
   ↓
TCP
   ↓
TLS
   ↓
HTTP
   ↓
Load Balancer
   ↓
API Server
   ↓
Database
```

---

# **🔄 Real Request Flow — DNS → Backend**

A realistic production request can look like:

```
User
 │
 │ https://api.aditechdev.com/orders/123
 ▼
Browser
 │
 │ DNS lookup
 ▼
Recursive Resolver
 │
 │ cache miss
 ▼
Root → TLD → Authoritative
 │
 │ 203.0.113.20
 ▼
Browser
 │
 │ TCP :443
 ▼
TLS
 │
 │ HTTP GET
 ▼
Load Balancer
 │
 ▼
API Server
 │
 ▼
Controller
 │
 ▼
Application Service
 │
 ▼
Repository
 │
 ▼
Database
 │
 ▼
HTTP Response
```

This ties together:

```
DNS
Networking
TLS
HTTP
Load Balancing
3-Layer Architecture
Database
```

---

# **💻 Code**

DNS itself is usually interacted with through OS/browser/network tooling rather than application code.

### **Resolve a domain**

```
dig api.example.com
```

or:

```
nslookup api.example.com
```

### **Ask a specific recursive resolver**

```
dig @1.1.1.1 api.example.com
```

### **Inspect different record types**

```
dig A api.example.com
dig AAAA api.example.com
dig CNAME www.example.com
dig NS example.com
```

### **Useful DNS investigation**

```
dig +trace api.example.com
```

This is particularly useful for learning the:

```
Root → TLD → Authoritative
```

resolution path.

---

# **🔍 Practical Networking Tools**

For packet-level investigation, useful tools include:

### **Wireshark**

A graphical packet analyzer for inspecting network traffic.

Useful for learning:

- TCP handshake
- TCP flags
- DNS packets
- TLS handshake
- HTTP traffic
- retransmissions
- packet timing

### **tcpdump**

Command-line packet capture tool.

The TCP SYN/SYN-ACK inspection command discussed during this topic:

```
sudo tcpdump -i any -nn 'tcp[tcpflags] & (tcp-syn|tcp-ack) != 0'
```

This was intentionally deferred for hands-on practice rather than mixing packet analysis into the DNS fundamentals.

---

# **🎯 Interview Questions**

### **1. What problem does DNS solve?**

**Answer:**
 DNS provides a distributed naming system that allows applications to resolve human-readable domain names into DNS records such as IP addresses.

---

### **2. What is a recursive DNS resolver?**

**Answer:**
 A recursive resolver receives DNS queries from clients, checks its cache, and if necessary performs the lookup through the DNS hierarchy before returning the result.

---

### **3. What does the Root DNS server do?**

**Answer:**
 The Root provides information about the authoritative infrastructure for TLDs such as `.com`, `.org`, and `.in`. It does not normally provide the final IP address for a specific hostname.

---

### **4. What does a TLD DNS server do?**

**Answer:**
 A TLD server provides delegation information for domains under that TLD. For example, `.com` can identify the authoritative nameservers for `example.com`.

---

### **5. What is an authoritative DNS server?**

**Answer:**
 A DNS server that is authoritative for a zone and hosts the authoritative DNS records for that zone.

---

### **6. What is DNS caching?**

**Answer:**
 DNS caching stores DNS answers temporarily so subsequent queries can be answered without repeatedly traversing the DNS hierarchy.

---

### **7. What is TTL?**

**Answer:**
 TTL specifies how long a DNS answer may be cached before the cached information must be refreshed.

---

### **8. What happens when a DNS record’s IP changes?**

**Answer:**
 Existing cached answers may continue being returned until their TTL expires. After expiration, recursive resolvers obtain fresh information from the DNS hierarchy.

---

### **9. What is the difference between A and AAAA?**

**Answer:**

```
A     → IPv4
AAAA  → IPv6
```

---

### **10. What is a CNAME?**

**Answer:**
 A CNAME maps one hostname to another hostname.

```
www.example.com
      ↓
CNAME
      ↓
example.com
```

---

### **11. What is an NS record?**

**Answer:**
 An NS record identifies the authoritative nameservers for a DNS zone.

---

### **12. What is delegation?**

**Answer:**
 Delegation assigns responsibility for a DNS zone to specific authoritative nameservers.

---

### **13. Does changing DNS provider necessarily change the server IP?**

**Answer:**
 No. Changing the authoritative DNS provider changes where DNS records are hosted and the nameservers used for delegation. The actual A/AAAA records can remain unchanged.

---

### **14. Why can multiple domains/subdomains use the same IP?**

**Answer:**
 Multiple hostnames can resolve to the same IP. The load balancer or web server can distinguish them using information such as the HTTP `Host` header and TLS SNI.

---

### **15. Does DNS route an HTTP request to the correct application?**

**Answer:**
 Not at the application-routing level. DNS resolves the hostname to infrastructure such as an IP address. A load balancer or reverse proxy can then route the request to the appropriate service.

---

### **16. Recursive vs iterative DNS resolution?**

**Answer:**

```
Recursive:
"Find the answer for me."

Iterative:
"Here's the next place you should ask."
```

A common architecture is:

```
Client
 ↓ recursive query
Recursive Resolver
 ↓ iterative queries
Root → TLD → Authoritative
```

---

### **17. Why doesn’t every DNS query start at Root?**

**Answer:**
 Recursive resolvers cache DNS information. If the required answer is already cached and its TTL hasn’t expired, the resolver can return it directly.

---

### **18. Why does IPv6 exist?**

**Answer:**
 IPv4 uses only 32-bit addresses, providing about 4.3 billion possible addresses. IPv6 uses 128-bit addresses, providing a vastly larger address space suitable for the scale of the Internet.

---

# **⚠️ Mistakes / Gotchas**

### **1. DNS ≠ IP address database**

DNS stores different types of records, not just IP addresses.

---

### **2. Recursive resolver ≠ ISP**

An ISP can operate a resolver, but public resolvers such as `1.1.1.1` and `8.8.8.8` are also available.

---

### **3. TLD ≠ authoritative DNS**

```
TLD
 ↓
Who handles example.com?

Authoritative
 ↓
What is api.example.com?
```

---

### **4. Root doesn’t know every domain**

The hierarchy distributes responsibility.

```
Root → TLD
TLD → Domain
Domain → DNS records
```

---

### **5. CNAME doesn’t mean “same IP”**

CNAME means:

```
Hostname → Hostname
```

The target hostname can then resolve to an IP.

---

### **6. NS doesn’t contain every domain**

NS information is scoped to a DNS zone.

```
example.com
   ↓
NS
   ↓
authoritative nameservers
```

---

### **7. DNS propagation isn’t a global push**

Resolvers independently cache and refresh records.

---

### **8. TTL doesn’t delete the authoritative record**

TTL controls cached copies.

---

### **9. Same IP doesn’t mean same application**

```
api.example.com ──┐
www.example.com ──┼──→ Same IP
admin.example.com ─┘
                     ↓
                Load Balancer
                     ↓
              Host-based routing
```

---

### **10. DNS happens before the normal TCP/HTTP flow**

For a typical HTTPS request:

```
DNS
 ↓
TCP
 ↓
TLS
 ↓
HTTP
```

For HTTP/3:

```
DNS
 ↓
QUIC
 ↓
HTTP/3
```

---

# **🔑 Key Takeaways**

If you remember only these points:

### **DNS hierarchy**

```
Root
 ↓
TLD
 ↓
Authoritative DNS
 ↓
DNS Record
```

### **Resolver**

```
Client
 ↓
Recursive Resolver
 ↓
"Find the answer for me."
```

### **Caching**

```
Cache HIT
 ↓
Return answer

Cache MISS
 ↓
Resolve through hierarchy
```

### **Records**

```
A      → IPv4
AAAA   → IPv6
CNAME  → Hostname
NS     → Authoritative nameservers
```

### **TTL**

```
Higher TTL
→ better cache efficiency
→ slower visibility of changes

Lower TTL
→ fresher information
→ more DNS queries
```

### **Request flow**

```
Domain
 ↓
DNS
 ↓
IP
 ↓
TCP / QUIC
 ↓
TLS
 ↓
HTTP
 ↓
Load Balancer
 ↓
Application
 ↓
Database
```

### **Core interview sentence**

**A client typically sends a recursive DNS query to a recursive resolver. If the answer isn’t cached, the resolver follows the DNS hierarchy through the Root and TLD to the authoritative nameserver, obtains the DNS record, caches it according to TTL, and returns the answer to the client.**

---

# **📚 References**

- ⁠ [ICANN — DNS](https://www.icann.org/resources/pages/dns-2012-02-25-en)
- ⁠ [IANA — Root Zone Management](https://www.iana.org/domains/root)
- ⁠ [RFC 1034 — Domain Names: Concepts and Facilities](https://www.rfc-editor.org/rfc/rfc1034)
- ⁠ [RFC 1035 — Domain Names: Implementation and Specification](https://www.rfc-editor.org/rfc/rfc1035)
- ⁠ [RFC 3596 — DNS Extensions to Support IP Version 6](https://www.rfc-editor.org/rfc/rfc3596)
- ⁠ [Cloudflare Learning Center — DNS](https://www.cloudflare.com/learning/dns/)
- ⁠ [Google Public DNS](https://developers.google.com/speed/public-dns)

---

# **🚧 Pending Topic**

The following topics were intentionally deferred from the current DNS/networking fundamentals pass and should be covered later as hands-on/advanced networking:

### **Packet Inspection**

- Wireshark hands-on
- Capture DNS packets
- Inspect TCP 3-way handshake
- Inspect TCP flags
- Inspect TLS handshake
- Packet timing and latency
- Retransmissions

### **`tcpdump`**

```
sudo tcpdump -i any -nn 'tcp[tcpflags] & (tcp-syn|tcp-ack) != 0'
```

- Understand each part of the command
- Capture SYN
- Capture SYN-ACK
- Capture ACK
- Inspect actual packet flow

### **HTTP/2**

- Why HTTP/2 was introduced
- HTTP/1.1 limitations
- Binary framing
- Streams
- Frames
- Multiplexing
- HPACK
- HTTP/2 vs HTTP/1.1
- Head-of-line blocking at the TCP layer

### **HTTP/3**

- Why HTTP/3 was introduced
- QUIC
- HTTP/3 over QUIC
- QUIC over UDP
- HTTP/2 vs HTTP/3
- TLS 1.3 integration with QUIC
- Stream-level loss handling
- Connection migration

### **Practical comparison**

```
HTTP/1.1
    ↓
TCP
    ↓
TLS

HTTP/2
    ↓
TCP
    ↓
TLS

HTTP/3
    ↓
QUIC
    ↓
UDP
```
