# Computer Networks Project
## Private Network Service Platform

### Member :-
1. Punit - 2401010359


### Infrastructure
Submission Type: Type 4 — AWS Cloud Infrastructure

### Architecture

The project uses one AWS VPC with four EC2 instances.

| Instance | Private IP | Role |
|---|---|---|
| CN-DNS-CLIENT | 10.10.1.230 | Private DNS + Test Client |
| CN-EDGE | 10.10.1.76 | nginx Reverse Proxy + Load Balancer + TLS |
| CN-BACKEND-A | 10.10.1.17 | Node.js Backend A - Port 3001 |
| CN-BACKEND-B | 10.10.1.165 | Node.js Backend B - Port 3002 |

### Request Flow

Client
→ Private DNS
→ app.team1.test resolves to CN-EDGE
→ TCP connection
→ TLS/HTTPS
→ nginx reverse proxy/load balancer
→ Backend A or Backend B
→ Response returned to client

### Phase 1 Tasks

#### Task A — Private Network
Created an AWS VPC and verified private connectivity between all EC2 instances.

#### Task B — Private DNS
Configured dnsmasq on CN-DNS-CLIENT.

DNS records:

app.team1.test → 10.10.1.76

api.team1.test → 10.10.1.76

#### Task C — Backend Services
Backend A:
10.10.1.17:3001

Backend B:
10.10.1.165:3002

Both provide:

GET /

GET /api/status

and return an X-Backend header.

#### Task D — Reverse Proxy and Load Balancing
nginx runs on CN-EDGE and performs round-robin load balancing between Backend A and Backend B.

#### Task E — HTTPS / TLS
TLS terminates at nginx on port 8443.

A local CA was used so clients can validate the certificate without bypassing certificate verification.

#### Task F — HTTP Caching
Implemented:

Cache-Control: public, max-age=60

ETag: "v1"

Conditional requests demonstrate:

304 Not Modified

#### Task G — Protocol Capture
Packet captures demonstrate:

- DNS query and response
- TCP three-way handshake
- TLS handshake
- HTTPS traffic
- port identification
- load balancing

The packet capture is available in:

pcap/phase1-client.pcap

### Failure Demonstrations

The following failures were tested:

1. Wrong DNS record
2. Wrong DNS server
3. Backend A stopped
4. Both backends stopped
5. Wrong destination port

Observed results are included in the evidence folder.

### Main Technologies

- AWS VPC
- AWS EC2
- Security Groups
- Node.js
- nginx
- dnsmasq
- OpenSSL
- curl
- dig
- tcpdump
- Wireshark

### Main Request Path

app.team1.test
→ DNS Server
→ CN-EDGE
→ HTTPS/TLS
→ nginx
→ Backend A / Backend B
