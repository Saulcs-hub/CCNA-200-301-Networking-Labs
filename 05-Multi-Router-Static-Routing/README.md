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

A `/30` mask provides four IPv4 addresses per subnet, including two usable host addresses, making it suitable for these point-to-point connections.

## 5. Static Routing Configuration

Static routes were manually configured on each router to provide reachability between all four LANs.

The configuration uses the following Cisco IOS syntax:

```bash
ip route <destination-network> <subnet-mask> <next-hop-address>
```

### R1 — Static Routes

```text
ip route 192.168.2.0 255.255.255.0 10.10.13.2
ip route 192.168.3.0 255.255.255.0 10.10.12.2
ip route 192.168.4.0 255.255.255.0 10.10.13.2
```

### R2 — Static Routes

```text
ip route 192.168.1.0 255.255.255.0 10.10.12.1
ip route 192.168.2.0 255.255.255.0 10.10.24.2
ip route 192.168.4.0 255.255.255.0 10.10.24.2
```

### R3 — Static Routes

```text
ip route 192.168.1.0 255.255.255.0 10.10.13.1
ip route 192.168.3.0 255.255.255.0 10.10.13.1
ip route 192.168.4.0 255.255.255.0 10.10.34.2
```

### R4 — Static Routes

```text
ip route 192.168.1.0 255.255.255.0 10.10.34.1
ip route 192.168.2.0 255.255.255.0 10.10.34.1
ip route 192.168.3.0 255.255.255.0 10.10.24.1
```

Each router maintains routes toward three remote LANs. Its own LAN is already known through a directly connected route.

The configurations were saved to NVRAM using:

```bash
copy running-config startup-config
```

## 6. Routing Table Verification

The following Cisco IOS commands were used:

```bash
show ip interface brief
show ip route
show running-config
```

The routing table uses the following codes:

| Code | Meaning |
|---|---|
| C | Directly Connected Route |
| L | Local Route |
| S | Static Route |

The `show ip route` output confirmed that connected, local, and static routes were installed on the routers.

No default routes or dynamic routing protocols were configured.

## 7. Connectivity Testing

ICMP Echo Requests were used to validate communication across different LANs.

### Recorded Test Results

| Source | Destination | Result |
|---|---|---|
| PC1 — LAN A | 192.168.3.7 — LAN C | 3/4 replies |
| PC1 — LAN A | 192.168.2.67 — LAN B | 3/4 replies |
| PC2 — LAN C | 192.168.2.67 — LAN B | 4/4 replies |
| PC2 — LAN C | 192.168.4.98 — LAN D | 3/4 replies |
| LAN B host | 192.168.3.14 — LAN C | 3/4 replies |
| LAN B host | 192.168.4.39 — LAN D | 4/4 replies |
| LAN B host | 192.168.1.75 — LAN A | 4/4 replies |
| PC7 — LAN D | 192.168.2.4 — LAN B | 3/4 replies |
| PC7 — LAN D | 192.168.3.7 — LAN C | 4/4 replies |

Successful replies demonstrated inter-LAN connectivity through the configured static routes.

Some initial ICMP requests timed out. These are consistent with address resolution occurring during the first exchanges, although the captures alone do not establish ARP as the definitive cause.

## 8. Packet Forwarding and ARP Analysis

A packet traveling from LAN A to LAN D follows these general steps:

1. The source PC determines that the destination belongs to a remote network.
2. The PC sends the packet toward its configured default gateway.
3. ARP is used when the MAC address of the next-hop device is unknown.
4. The source switch forwards the Ethernet frame based on its MAC address table.
5. R1 checks its IPv4 routing table and identifies the static route toward LAN D.
6. R1 forwards the packet to R3 using the configured next-hop address.
7. R3 consults its own routing table and forwards the packet to R4.
8. R4 identifies LAN D as directly connected and delivers the packet to the destination host.
9. The destination generates an ICMP Echo Reply, which returns through the configured routing paths.

During forwarding, IPv4 source and destination addresses remain unchanged in this topology, while Ethernet source and destination MAC addresses are updated at each routed Ethernet hop.

The IPv4 TTL is decremented when a router forwards the packet.

## 9. Asymmetric Routing

This lab also demonstrates asymmetric routing.

For example, traffic from LAN C to LAN B can follow:

```text
R2 → R4 → R3
```

The return traffic can follow:

```text
R3 → R1 → R2
```

This occurs because routing decisions are made independently by each router using its local routing table.

The forward and return paths do not need to be identical for basic IP connectivity to succeed.

## 10. Troubleshooting and Observations

Several important behaviors were observed during the lab:

**Connected versus remote networks:** Routers initially knew only their directly connected networks. Remote LAN communication required additional routes.

**Interface status:** Proper IPv4 configuration and operational `up/up` interfaces were necessary for connected routes to appear in the active routing table.

**Initial ICMP timeouts:** Some tests experienced a timeout on the first request, followed by successful replies.

**Next-hop selection:** Static routes had to reference reachable next-hop IP addresses. Incorrect next-hop addressing could prevent successful forwarding.

**Bidirectional communication:** Forward reachability alone was insufficient; valid return paths were also required.

## 11. Lessons Learned

This lab reinforced the relationship between Layer 2 switching and Layer 3 routing.

The main technical outcomes were:

- Understanding how connected and static routes coexist in the routing table.
- Selecting appropriate next-hop addresses for remote destinations.
- Recognizing why each router makes its own forwarding decisions.
- Configuring bidirectional connectivity between multiple subnets.
- Applying `/30` subnetting to inter-router links.
- Interpreting ICMP results and routing table entries.
- Understanding how ARP, Ethernet encapsulation, and IP forwarding work together.
- Observing asymmetric routing across a multi-path topology.

The laboratory strengthened practical Cisco IOS configuration and verification skills as part of ongoing CCNA 200-301 preparation.

---

## 12. Technical Report

The complete laboratory report, including configuration details and supporting evidence, is available here:

[View Lab 05 — Multi-Router Static Routing Report](./CCNA_Lab05_Multi-Router_Static_Routing.pdf)

---

**Lab Status:** Completed — Static routing configured and inter-LAN connectivity verified.

**Next Learning Objectives:** Default Routes, Routing Troubleshooting, and Advanced Packet Forwarding Analysis.
