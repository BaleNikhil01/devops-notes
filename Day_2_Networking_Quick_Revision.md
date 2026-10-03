# Day 2 — Networking | Interview Cheat Sheet

## 1. IP Address

Identifies a device/network interface on a network.

```text
192.168.0.105
```

- IPv4 = 32 bits
- Private IP → internal network
- Public IP → Internet-facing communication

**Remember:** IP = logical network address.

---

## 2. MAC Address

Identifies a network interface at the data-link layer.

```text
38:8d:3d:7d:6f:3a
```

**Remember:**

```text
IP  → logical address
MAC → interface address
```

---

## 3. Port

Identifies a service/application endpoint on a host.

| Port | Use |
|---:|---|
| 22 | SSH |
| 53 | DNS |
| 80 | HTTP |
| 443 | HTTPS |
| 3306 | MySQL |
| 5432 | PostgreSQL |

**Remember:** IP = machine, Port = service.

---

## 4. Protocol

Rules used for communication.

Examples:

```text
TCP, UDP, DNS, HTTP, HTTPS, SSH
```

---

## 5. TCP vs UDP

| TCP | UDP |
|---|---|
| Connection-oriented | Connectionless |
| Reliable, ordered delivery | No TCP-style delivery guarantee |
| More overhead | Lower overhead |
| SSH, HTTP/HTTPS | DNS, real-time traffic |

**Interview:** TCP provides reliable, ordered delivery; UDP is connectionless with lower overhead.

---

## 6. DNS

DNS resolves domain names to IP addresses/other DNS records.

```text
google.com → DNS → IP address
```

**Remember:** DNS is not the actual web connection; it helps find the destination.

---

## 7. HTTP / HTTPS

```text
HTTP  → 80
HTTPS → 443
```

HTTPS = HTTP protected using **TLS**.

**Remember:** TLS provides encryption and server authentication.

---

## 8. SSH

Used for secure remote access to Linux servers.

```bash
ssh user@server-ip
```

Default port:

```text
22
```

---

## 9. Subnet

A logical division of an IP network.

Example:

```text
VPC:           10.0.0.0/16
Subnet:        10.0.1.0/24
```

**Remember:** A subnet is part of a larger network.

---

## 10. CIDR

CIDR represents a network using a prefix length.

```text
10.0.0.0/24
```

### Quick trick

```text
Smaller / number → Bigger network → More IPs
Larger / number  → Smaller network → Fewer IPs
```

Example:

```text
/16 → bigger
/24 → smaller
```

Don't focus on binary calculations yet.

---

## 11. Gateway

Provides a path from one network to another.

Your machine:

```text
192.168.0.105
       ↓
192.168.0.1  ← Default Gateway
       ↓
Internet
```

**Remember:** If the destination isn't local, traffic normally goes through the default gateway.

---

## 12. Routing

Routing determines where packets should go.

Check Linux routing table:

```bash
ip route
```

Your route:

```text
default via 192.168.0.1 dev wlo1
```

Meaning:

```text
default destination
      ↓
gateway = 192.168.0.1
      ↓
interface = wlo1
```

**Remember:** Routing table = instructions for where packets go.

---

## 13. NAT

### Interview Question: Why do private instances need a NAT Gateway?

Private instances do not have public IP addresses, but they may still need **outbound Internet access** for OS updates, security patches, or external APIs.

A NAT Gateway lets them access the Internet **without giving the instances public IP addresses**.

```text
Private EC2
    ↓
NAT Gateway
    ↓
Internet Gateway
    ↓
Internet
```

**Remember:** NAT Gateway = outbound Internet access for private resources.

NAT = Network Address Translation.

Common use:

```text
Private IP → NAT → Public IP → Internet
```

It allows private-addressed machines to communicate externally without exposing their private IP directly.

### AWS NAT Gateway

Private instances may need Internet access for:

- OS updates
- Security patches
- External APIs

```text
Private EC2
    ↓
NAT Gateway
    ↓
Internet Gateway
    ↓
Internet
```

**Important:** NAT Gateway provides outbound Internet access; unsolicited inbound connections are not forwarded to the private instance.

---

## 14. Public vs Private Subnet — AWS

### Public subnet

Has a route to an **Internet Gateway**.

```text
Internet
   ↓
Internet Gateway
   ↓
Public Subnet
```

### Private subnet

Does not have a direct route to the Internet Gateway.

For outbound Internet access:

```text
Private Subnet
      ↓
NAT Gateway
      ↓
Internet Gateway
      ↓
Internet
```

**Important interview point:** A resource being in a public subnet does not automatically make it Internet-accessible. Routing, public addressing, and security rules also matter.

---

## 15. Firewall / Security Group

A firewall controls traffic using rules such as:

```text
Protocol + Port + Source/Destination
```

### AWS Security Group

Acts as a virtual firewall for resources such as EC2.

Example:

```text
22  → SSH
80  → HTTP
443 → HTTPS
```

**Important:** Security Groups are **stateful**.

---

# 16. What Happens When You Enter https://google.com?

Interview-level flow:

```text
URL
 ↓
DNS resolves google.com
 ↓
IP address
 ↓
TCP connection
 ↓
TLS handshake
 ↓
HTTPS request
 ↓
Google server
 ↓
HTTPS response
 ↓
Browser renders page
```

Remember:

```text
DNS → finds destination
TCP → reliable connection
TLS → encryption/authentication
HTTPS → web communication over TLS
```

---

# 17. Common DevOps Networking Commands

```bash
ip addr       # IP addresses/interfaces
ip link       # interfaces + MAC
ip route      # routing table
ss -tuln      # listening TCP/UDP ports
ping <host>   # connectivity test
nslookup <domain>  # DNS lookup
curl <url>    # HTTP/HTTPS test
```

---

# 18. Interview Questions — One-Line Answers

**What is an IP address?**  
A logical network address used to identify a device/interface and enable network communication.

**What is a subnet?**  
A logical subdivision of an IP network.

**What is CIDR?**  
A notation that represents an IP network using a prefix length, such as `/24`.

**TCP vs UDP?**  
TCP is connection-oriented and reliable; UDP is connectionless with lower overhead and no TCP-style delivery guarantee.

**What is DNS?**  
A system that resolves domain names to IP addresses and other DNS records.

**What happens when you enter a URL?**  
DNS resolution → TCP connection → TLS for HTTPS → HTTP request → server response.

**What is a port?**  
A number that identifies a service/application endpoint on a host.

**What is NAT?**  
Network Address Translation; commonly used to allow private IPs to access external networks through a public address.

**Public vs private subnet?**  
Public subnet has a route to an Internet Gateway; private subnet does not have a direct Internet Gateway route.

**What is a gateway?**  
A device/service that provides a path to another network.

---

# 19. Final Memory Map

```text
IP       → WHERE?
Port     → WHICH SERVICE?
Protocol → HOW?
DNS      → FIND THE IP
Subnet   → NETWORK DIVISION
CIDR     → NETWORK SIZE
Gateway  → PATH TO ANOTHER NETWORK
Routing  → WHERE SHOULD THE PACKET GO?
NAT      → PRIVATE ↔ PUBLIC ADDRESS TRANSLATION
Firewall → ALLOW / BLOCK TRAFFIC
SG       → AWS VIRTUAL FIREWALL
```
