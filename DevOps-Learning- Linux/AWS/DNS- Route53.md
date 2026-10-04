# AWS Route 53

> **Route 53 = AWS DNS (Domain Name System) service**

DNS converts a **domain name → IP address**.

Example:

```text
myapp.com → 2.22.21.18
```

DNS normally uses **port 53**.

---

# 1. DNS Resolution Flow ⭐

When a user enters:

```text
https://myapp.com
```

The DNS resolution process is approximately:

```text
Browser Cache
     ↓
OS DNS Cache
     ↓
DNS Resolver
     ↓
Root DNS Server
     ↓
.com TLD DNS Server
     ↓
Authoritative Name Server
     ↓
DNS Record (A / AAAA / etc.)
     ↓
IP Address
```

Example:

```text
myapp.com
    ↓
DNS Resolver
    ↓
Root Server
    ↓
.com TLD Server
    ↓
Authoritative DNS Server
    ↓
A Record
    ↓
2.22.21.18
```

### Important

The authoritative DNS server could be:

- Route 53
    
- GoDaddy DNS
    
- Hostinger DNS
    
- Cloudflare DNS
    
- Other DNS providers
    

The **TLD server does not normally contain your A record**. It tells the resolver which authoritative name servers are responsible for the domain.

---

# 2. Route 53 Hosted Zones

A **Hosted Zone** is a container for DNS records for a domain.

Example:

```text
example.com
     |
     ├── www
     ├── api
     ├── dev
     └── mail
```

## Public Hosted Zone

Used for domains that need to be resolved from the **Internet**.

```text
Internet
   ↓
Route 53 Public Hosted Zone
   ↓
myapp.com
```

## Private Hosted Zone

Used for DNS resolution **inside one or more VPCs**.

```text
AWS VPC
   ↓
Private Hosted Zone
   ↓
db.internal
   ↓
Private IP
```

---

# 3. DNS Record Types ⭐

| Record | Purpose                 |
| ------ | ----------------------- |
| A      | Domain → IPv4           |
| AAAA   | Domain → IPv6           |
| CNAME  | Domain → another domain |
| Alias  | AWS resource mapping    |
| MX     | Mail server             |
| TXT    | Text / verification     |
| NS     | Name servers            |
| SOA    | Start of Authority      |

### A Record

```text
myapp.com → 2.22.21.18
```

A record maps a hostname to an **IPv4 address**.

### AAAA Record

```text
myapp.com → IPv6 address
```

### CNAME

Maps one hostname to another hostname.

```text
www.myapp.com
      ↓
myapp.com
```

### Alias

AWS-specific DNS functionality used to point a hostname to supported AWS resources.

Common examples:

```text
Route 53
   ↓
ALB
CloudFront
S3
API Gateway
```

### MX

Used to identify mail servers.

```text
example.com
     ↓
MX
     ↓
Mail Server
```

### TXT

Commonly used for:

- Domain verification
    
- SPF
    
- Other text-based DNS information
    

### NS

Specifies the **authoritative name servers** for a domain.

Example:

```text
ns-123.awsdns-45.com
ns-456.awsdns-78.net
```

### SOA

Contains information about the DNS zone's authority and related DNS parameters.

---

# 4. Domain Transfer / DNS Migration to AWS ⭐

Example:

```text
Domain Registrar:
Hostinger

DNS Provider:
Hostinger

↓ Migration

DNS Provider:
Route 53
```

## Steps

### Step 1 — Create Hosted Zone

Create a Route 53 hosted zone with the same domain:

```text
myapp.com
```

Route 53 provides a set of **NS records**.

Example:

```text
NS

ns-123.awsdns-45.com
ns-456.awsdns-78.net
ns-789.awsdns-12.org
ns-012.awsdns-34.co.uk
```

### Step 2 — Update Name Servers

At the domain registrar/DNS management location, replace the old name servers with the Route 53 name servers.

```text
Old NS
Hostinger
   ↓
Replace
   ↓
Route 53 NS
```

### Step 3 — Delegation Changes

The registrar publishes the new name-server delegation to the domain's **TLD infrastructure**.

Then DNS resolvers gradually start using the new authoritative name servers.

> ⚠️ DNS changes are affected by caching and TTLs, so the change is not necessarily instantaneous.

### Important distinction

**Domain registration ≠ DNS hosting**

You can have:

```text
Registrar → Hostinger
DNS Hosting → Route 53
```

You don't necessarily have to transfer the domain registration to AWS just to use Route 53 DNS.

---

# 5. TTL — Time To Live ⭐⭐⭐

TTL tells DNS resolvers/caches **how long a DNS response can be cached**.

Example:

```text
myapp.com
     ↓
A Record
     ↓
2.22.21.18

TTL = 86400 seconds
```

```text
86400 seconds = 24 hours = 1 day
```

The resolver can cache the DNS answer for the TTL period.

---

# 6. Why TTL Matters

Suppose:

```text
myapp.com → 2.22.21.18

TTL = 1 day
```

A DNS resolver receives:

```text
2.22.21.18
```

and caches it.

For subsequent users whose resolver has a valid cached answer, DNS resolution may be answered from the cache instead of querying the authoritative server again.

### Benefits of higher TTL

```text
Higher TTL
   ↓
More caching
   ↓
Fewer DNS queries
   ↓
Lower DNS lookup traffic
```

But:

```text
Higher TTL
   ↓
DNS changes can take longer
   ↓
Old DNS answers may remain cached
```

---

# 7. Changing an IP Address

Suppose the application currently uses:

```text
myapp.com → 2.22.21.18
TTL = 1 day
```

Now you want:

```text
myapp.com → 2.22.21.19
```

If some DNS resolvers already cached:

```text
2.22.21.18
```

they can continue using that cached answer until its TTL expires.

This is why DNS changes can appear differently to different users during the transition.

---

# 8. DNS Migration Best Practice ⭐

If you know that you are going to change an IP address:

### Before the migration

Reduce the TTL **in advance**.

Example:

```text
1 day
 ↓
1 hour
 ↓
5 minutes
```

Wait for the previous longer TTL to expire from caches.

Then change:

```text
Old IP
2.22.21.18

        ↓

New IP
2.22.21.19
```

After the migration is stable:

```text
TTL
5 minutes
   ↓
1 hour
   ↓
1 day
```

### Important correction

Don't think of this as a **"DNS failure"**.

It is generally a **DNS caching/propagation effect**.

Also, changing the TTL to 1 minute does **not immediately force every cached resolver to use 1 minute**. A resolver that already cached the previous answer with a 1-day TTL may keep that answer until its original TTL expires.

---

# 9. DNS Caching

There can be caching at multiple levels:

```text
Browser Cache
      ↓
OS Cache
      ↓
Local / Corporate DNS Resolver Cache
      ↓
Authoritative DNS
```

Example:

```text
myapp.com
   ↓
Cached IP = 2.22.21.18
```

If the cached record is still valid, the resolver can return it without querying the authoritative DNS server again.

---

# 10. Route 53 Routing Policies ⭐⭐⭐

Route 53 supports different routing policies.

```text
1. Simple
2. Weighted
3. Latency-based
4. Failover
5. Geolocation
6. Geoproximity
7. Multivalue Answer
```

---

## 10.1 Simple Routing

Basic DNS routing.

```text
User
 ↓
Route 53
 ↓
Application
```

Use when you don't need sophisticated routing.

---

## 10.2 Weighted Routing

Distributes traffic according to configured weights.

Example:

```text
                 Route 53
                    |
             ┌──────┴──────┐
             ↓             ↓
          Version 1     Version 2
             90%           10%
```

Useful for:

- Canary deployments
    
- Gradual migration
    
- Testing new versions
    

---

## 10.3 Latency-Based Routing

Routes users to the AWS region that Route 53 determines provides the **lowest latency** based on its measurements.

Example:

```text
India User
    ↓
Route 53
    ↓
AWS Region A

USA User
    ↓
Route 53
    ↓
AWS Region B
```

---

## 10.4 Failover Routing

Used for primary/secondary architectures.

```text
             Route 53
                |
        ┌───────┴───────┐
        ↓               ↓
     Primary         Secondary
        ↓               ↓
       ALB             ALB
```

Health check:

```text
Primary = Healthy
      ↓
Traffic → Primary
```

If primary becomes unhealthy:

```text
Primary = Unhealthy
      ↓
Traffic → Secondary
```

---

## 10.5 Geolocation Routing

Routes users based on their geographic location.

Example:

```text
India
  ↓
India endpoint

USA
  ↓
USA endpoint

Europe
  ↓
Europe endpoint
```

---

## 10.6 Geoproximity Routing

Routes traffic based on the geographic location of:

- Users
    
- AWS resources / endpoints
    

It can also use **bias** to shift traffic toward or away from a location.

---

## 10.7 Multivalue Answer Routing

Route 53 can return multiple healthy records.

Useful when you want DNS to return multiple healthy endpoints rather than a single endpoint.

---

# 11. Route 53 Health Checks ⭐

Route 53 can monitor endpoint health.

Example:

```text
Route 53
    |
    ↓
Health Check
    |
    ↓
Application / Endpoint
```

Example:

```text
Primary ALB
   ↓
Health Check
   ↓
Healthy ✅
```

If the endpoint becomes unhealthy:

```text
Primary ❌
```

A compatible routing configuration can route traffic to another healthy resource.

---

# 12. Common AWS Architecture ⭐⭐⭐

## Route 53 + ALB + EC2

```text
                 User
                   |
                   ↓
                Route 53
                   |
                   ↓
                  ALB
                   |
             ┌─────┴─────┐
             ↓           ↓
            EC2         EC2
```

---

# 13. Route 53 + EKS

Common DevOps architecture:

```text
User
  ↓
Route 53
  ↓
ALB
  ↓
AWS Load Balancer Controller
  ↓
Kubernetes Service
  ↓
Pods
```

Example:

```text
app.example.com
      ↓
Route 53
      ↓
ALB
      ↓
Kubernetes Service
      ↓
Pods
```

---

# 14. Route 53 + CloudFront

```text
User
  ↓
Route 53
  ↓
CloudFront
  ↓
S3 / ALB
```

Route 53:

> DNS resolution

CloudFront:

> CDN / edge caching

---

# 15. Important Interview Questions ⭐⭐⭐

### Q1. What is Route 53?

> Route 53 is AWS's highly available and scalable DNS service.

### Q2. Why is it called Route 53?

> DNS commonly uses port 53.

### Q3. What is a Hosted Zone?

> A hosted zone is a container for DNS records for a domain.

### Q4. Public vs Private Hosted Zone?

```text
Public Hosted Zone
        ↓
Internet DNS resolution

Private Hosted Zone
        ↓
DNS resolution within associated VPCs
```

### Q5. A Record vs CNAME?

```text
A
↓
Hostname → IPv4

CNAME
↓
Hostname → Hostname
```

### Q6. What is an Alias record?

> An AWS-specific DNS record that can point a hostname to supported AWS resources such as ALB, CloudFront, and S3 website endpoints.

### Q7. What is TTL?

> TTL determines how long a DNS response may be cached.

### Q8. What is Weighted Routing?

> Routes traffic according to configured weights.

### Q9. What is Latency-Based Routing?

> Routes users to the region with the lowest measured network latency.

### Q10. What is Failover Routing?

> Routes traffic between primary and secondary resources based on health.

### Q11. What is DNS propagation?

> The period during which DNS changes become visible as cached DNS information expires and resolvers obtain the updated information.

---

# 16. ⭐ Interview Scenario

### Question:

> Your application IP is changing. How will you minimize DNS-related impact?

### Answer:

```text
Current TTL = 24 hours

        ↓

Reduce TTL well before migration

        ↓

Wait for old TTL/cache entries to expire

        ↓

Change DNS record

Old:
2.22.21.18

New:
2.22.21.19

        ↓

Verify application

        ↓

After migration is stable

        ↓

Increase TTL again
```

### Key point

```text
Lower TTL
   ↓
Faster DNS changes
   ↓
More DNS queries

Higher TTL
   ↓
More caching
   ↓
Fewer DNS queries
   ↓
Slower DNS changes
```

---

# 17. Quick Revision

```text
Route 53
│
├── DNS Service
│
├── Hosted Zones
│   ├── Public
│   └── Private
│
├── Records
│   ├── A
│   ├── AAAA
│   ├── CNAME
│   ├── Alias
│   ├── MX
│   ├── TXT
│   ├── NS
│   └── SOA
│
├── Routing Policies
│   ├── Simple
│   ├── Weighted
│   ├── Latency
│   ├── Failover
│   ├── Geolocation
│   ├── Geoproximity
│   └── Multivalue
│
├── Health Checks
│
├── TTL
│
└── Domain Registration
```

# DevOps Must-Know ⭐

```text
Route 53
    ↓
DNS
    ↓
Hosted Zone
    ↓
DNS Records
    ↓
A / CNAME / Alias
    ↓
TTL
    ↓
Routing Policies
    ↓
Health Checks
    ↓
ALB / CloudFront / EKS
```