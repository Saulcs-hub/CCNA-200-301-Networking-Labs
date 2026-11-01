# Lab 05 — Multi-Router Static Routing

**Cisco CCNA 200-301 | Network Engineering Laboratory**

**Author:** Systems Engineering Student | CCNA Candidate  
**Simulation Tool:** Cisco Packet Tracer  
**Topics:** IPv4 Addressing, Static Routing, Route Selection, ARP, ICMP, Network Troubleshooting

---

## 1. Overview

This lab demonstrates the implementation of static routing across a multi-router enterprise network using Cisco Packet Tracer.

The network consists of four interconnected routers, four Layer 2 switches, and nine end devices distributed across four independent LANs.

The routers are interconnected through point-to-point links using `/30` subnet masks. Each router provides connectivity to its local LAN, while manually configured static routes enable communication between remote networks.

The topology includes two possible physical paths between R1 and R4, providing an opportunity to examine routing decisions and asymmetric traffic flows.

**The primary objective is to establish end-to-end IPv4 connectivity between all four LANs without using dynamic routing protocols or default routes.**

## 2. Learning Objectives

- Configure IPv4 addresses on Cisco router interfaces.
- Design point-to-point networks using `/30` subnet masks.
- Verify interface operational status with `show ip interface brief`.
- Identify connected (`C`) and local (`L`) routing table entries.
- Configure static routes using `ip route`.
- Understand next-hop IP addresses and outgoing interfaces.
- Implement forward and return routes.
- Analyze longest prefix match and route selection.
- Verify end-to-end connectivity using ICMP.
- Observe ARP resolution and Ethernet encapsulation.
- Examine asymmetric routing across multiple paths.
- Troubleshoot basic Layer 2 and Layer 3 connectivity issues.

## 3. Network Topology

The topology consists of four routers connected in a diamond arrangement.

```text
                   LAN B
              192.168.2.0/24
                    |
                    R3
                  /    \
                 /      \
               R1        R4
                 \      /
                  \    /
                    R2
                    |
                   LAN C
              192.168.3.0/24

LAN A: R1 — 192.168.1.0/24
LAN D: R4 — 192.168.4.0/24
```

**Network components:**

- 4 Cisco routers (R1, R2, R3, R4)
- 4 Layer 2 switches (SWA, SWB, SWC, SWD)
- 9 end devices
- 4 independent IPv4 LANs
- 4 point-to-point inter-router links

The full network topology is documented in the technical PDF report included in this repository.

## 4. IPv4 Addressing Plan

### LAN Networks

| LAN | Network Address | Default Gateway | Connected Router |
|---|---|---|---|
| LAN A | 192.168.1.0/24 | 192.168.1.1 | R1 |
| LAN B | 192.168.2.0/24 | 192.168.2.1 | R3 |
| LAN C | 192.168.3.0/24 | 192.168.3.1 | R2 |
| LAN D | 192.168.4.0/24 | 192.168.4.1 | R4 |

### Point-to-Point Networks

| Link | Network | Router A | Router B |
|---|---|---|---|
| R1–R2 | 10.10.12.0/30 | R1: 10.10.12.1 | R2: 10.10.12.2 |
| R1–R3 | 10.10.13.0/30 | R1: 10.10.13.1 | R3: 10.10.13.2 |
| R2–R4 | 10.10.24.0/30 | R2: 10.10.24.1 | R4: 10.10.24.2 |
| R3–R4 | 10.10.34.0/30 | R3: 10.10.34.1 | R4: 10.10.34.2 |

A `/30` mask provides four IPv4 addresses per subnet, including
