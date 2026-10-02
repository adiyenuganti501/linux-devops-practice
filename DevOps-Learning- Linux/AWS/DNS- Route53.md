# AWS Route 53
DNS name  (myapp.com)-> browser cache -> OS -> OS Cache -> DNS resolver -> Root Server-> .com TLD -> GoDaddy/Hostinger NS -> A record 

Route 53 = AWS DNS Service
DNS uses port 53

## Hosted Zones
- Public Hosted Zone → Internet
- Private Hosted Zone → VPC

## DNS Records
- A      → IPv4
- AAAA   → IPv6
- CNAME  → Domain → Domain
- Alias  → AWS resources
- MX     → Mail
- TXT    → Verification/Text
- NS     → Name servers
- SOA    → DNS authority information

## Routing Policies
1. Simple
2. Weighted
3. Latency-based
4. Failover
5. Geolocation
6. Geoproximity
7. Multivalue Answer

## Important
- Health Checks
- TTL
- Domain Registration
- DNS resolution

## Common Architecture

User
 ↓
Route 53
 ↓
ALB
 ↓
EC2 / EKS

## Interview Keywords
Route 53
DNS
Hosted Zone
A Record
CNAME
Alias
TTL
Health Check
Weighted Routing
Latency Routing
Failover Routing
Public Hosted Zone
Private Hosted Zone