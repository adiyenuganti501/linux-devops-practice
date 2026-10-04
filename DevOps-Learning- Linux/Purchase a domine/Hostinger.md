# 🌐 Domain & DNS — khiranya.online

## Domain

**Domain:** `khiranya.online`

### Current Nameservers

```text
aurora.dns-parking.com
nebula.dns-parking.com
```

These are the authoritative nameservers currently configured for the domain.

---

# 🔍 DNS Resolution Flow

When a user enters:

```text
https://khiranya.online
```

The simplified DNS resolution flow is:

```text
Browser Cache
     ↓
OS DNS Cache
     ↓
DNS Resolver
     ↓
Root DNS Server
     ↓
.online TLD DNS Server
     ↓
Authoritative Nameserver
     ↓
DNS Record
     ↓
IP Address
```

### Example

```text
khiranya.online
      ↓
DNS Resolver
      ↓
Root
      ↓
.online TLD
      ↓
aurora.dns-parking.com
      ↓
A Record
      ↓
IP Address
```

### Important

The **Root** server does not know the IP address of `khiranya.online`.

The **`.online` TLD server** does not normally contain the A record either.

Instead:

```text
Root
 ↓
"Ask the .online TLD"

.online TLD
 ↓
"Ask these authoritative nameservers"

aurora.dns-parking.com
nebula.dns-parking.com
 ↓
A Record
 ↓
IP Address
```

---

# 📌 Important DNS Components

|Component|Responsibility|
|---|---|
|Browser Cache|Stores recently resolved DNS information|
|OS DNS Cache|Stores DNS results locally|
|DNS Resolver|Performs DNS lookup on behalf of the client|
|Root DNS|Directs resolver to the appropriate TLD|
|TLD DNS|Directs resolver to authoritative nameservers|
|Authoritative DNS|Stores the actual DNS records|
|A Record|Maps hostname → IPv4 address|

---

# 🔄 Domain DNS Migration to AWS Route 53

Suppose the domain is currently using Hostinger DNS:

```text
khiranya.online
        ↓
Hostinger Nameservers
        ↓
aurora.dns-parking.com
nebula.dns-parking.com
```

We want to move DNS management to **AWS Route 53**.

## Step 1 — Create Route 53 Hosted Zone

In AWS:

```text
Route 53
   ↓
Hosted zones
   ↓
Create hosted zone
```

Domain:

```text
khiranya.online
```

Choose:

```text
Public hosted zone
```

---

## Step 2 — Route 53 Creates Nameservers

AWS will provide nameservers similar to:

```text
ns-123.awsdns-45.org
ns-456.awsdns-78.com
ns-789.awsdns-12.net
ns-012.awsdns-34.co.uk
```

These are the **authoritative nameservers for the Route 53 hosted zone**.

---

## Step 3 — Create DNS Records

Inside the Route 53 hosted zone, create the required records.

Example:

```text
khiranya.online
        ↓
A Record
        ↓
<IP Address>
```

For example:

```text
Type: A
Name: khiranya.online
Value: <your IPv4 address>
```

You might also create:

```text
www.khiranya.online
```

using an A/AAAA, Alias, or CNAME record depending on the architecture.

---

# Step 4 — Update Nameservers at the Registrar

This is the critical step.

At the domain registrar where `khiranya.online` is registered, replace the current nameservers:

```text
aurora.dns-parking.com
nebula.dns-parking.com
```

with the Route 53 nameservers:

```text
ns-123.awsdns-45.org
ns-456.awsdns-78.com
ns-789.awsdns-12.net
ns-012.awsdns-34.co.uk
```

The **registrar** publishes this delegation through the `.online` TLD infrastructure.

---

# 🔄 After Nameserver Change

The DNS hierarchy becomes:

```text
User
 ↓
DNS Resolver
 ↓
Root
 ↓
.online TLD
 ↓
Route 53 Nameservers
 ↓
A Record
 ↓
IP Address
```

So:

```text
khiranya.online
       ↓
.online TLD
       ↓
Route 53 NS
       ↓
A Record
       ↓
Server IP
```

---

# ⚠️ Important: Domain Registration vs DNS Hosting

These are two different things.

### Domain Registrar

Responsible for:

```text
Domain registration
Domain ownership
Nameserver delegation
Domain renewal
```

Example:

```text
Hostinger
```

### DNS Provider

Responsible for:

```text
DNS records
A
AAAA
CNAME
MX
TXT
NS
etc.
```

Example:

```text
AWS Route 53
```

Therefore, you can have:

```text
Domain Registrar
      ↓
Hostinger

DNS Provider
      ↓
AWS Route 53
```

You do **not necessarily need to transfer the domain registration** to AWS.

You can simply change the nameservers at the registrar.

---

# 🎯 Interview Explanation

### Question:

**How do you migrate a domain's DNS from Hostinger to Route 53?**

### Answer:

> First, I create a public hosted zone in Route 53 using the same domain name. Route 53 provides a set of authoritative nameservers. I create the required DNS records in the hosted zone. Then, at the domain registrar, I replace the existing Hostinger nameservers with the Route 53 nameservers. The registrar updates the delegation, and eventually DNS resolvers start querying Route 53 for the domain's DNS records.

---

# 🧠 Easy Mental Model

```text
REGISTRAR
   │
   │ "Who manages DNS for this domain?"
   ↓
TLD (.online)
   │
   │ "These are the authoritative NS"
   ↓
ROUTE 53
   │
   │ "What is the A record?"
   ↓
A RECORD
   │
   ↓
IP ADDRESS
```

### One-line interview answer

> **Registrar controls the domain and nameserver delegation; the authoritative DNS provider controls the DNS records.**