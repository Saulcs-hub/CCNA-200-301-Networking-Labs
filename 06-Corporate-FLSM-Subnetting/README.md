# Lab 06 — Corporate Network: FLSM Subnetting & Static Routing

**Cisco CCNA 200-301 | Cisco Packet Tracer | IPv4 Subnetting | Static Routing | Network Troubleshooting**

## Overview

This lab demonstrates the planning, implementation, and verification of a small corporate network using **Fixed-Length Subnet Masking (FLSM)** and Cisco IOS static routing.

The company requires four departments to operate in separate IPv4 subnets while maintaining connectivity between them.

The solution uses **two Cisco routers, four Layer 2 switches, and eight end devices**, with a dedicated point-to-point transit network connecting both routers.

The lab also demonstrates practical infrastructure design decisions, including physical interface limitations, efficient IP address allocation, and the use of static and default routes.

## Learning Objectives

- Divide a `/24` IPv4 network into four equal-sized `/26` subnets.
- Calculate network addresses, subnet masks, broadcast addresses, and usable host ranges.
- Design and implement a multi-LAN corporate network in Cisco Packet Tracer.
- Configure Cisco IOS router interfaces and host default gateways.
- Establish connectivity between routers using a `/30` transit subnet.
- Implement static routes and default routes.
- Understand longest-prefix matching and routing table behavior.
- Verify connectivity using ICMP and Cisco IOS diagnostic commands.
- Document network topology, configuration, and troubleshooting decisions.

## Network Topology

**Network components**

| Device | Quantity | Purpose |
|---|---:|---|
| Cisco routers | 2 | Inter-subnet routing |
| Cisco 2960 switches | 4 | Layer 2 LAN connectivity |
| PCs | 8 | End-device connectivity testing |
| Router-to-router links | 1 | Point-to-point transit |

**Logical topology**

```text
                  ENGINEERING LAN
                   172.20.40.0/26
                         |
                        SW1
                         |
                       R1 G0/0
                         |
           +-------------R1-------------+
           |                            |
        R1 G0/2                      R1 G0/1
           |                            |
          SW3                     10.10.12.0/30
           |                            |
      COMMERCIAL LAN                 R2 G0/2
     172.20.40.128/26                  |
                                       R2
                                     /    \
                                  G0/0    G0/1
                                   |        |
                                  SW2      SW4
                                   |        |
                              ADMIN LAN   OPERATIONS LAN
                            172.20.40.64/26 172.20.40.192/26
```

The four departmental LANs use FLSM, while the router-to-router transit link uses a separate private IPv4 block.

## IPv4 Addressing Plan

### FLSM Design

The company was assigned the network:

**172.20.40.0/24**

Four equal-sized subnets were required.

Each department was allocated a `/26` subnet with:

- Subnet mask: `255.255.255.192`
- Total addresses per subnet: 64
- Usable host addresses: 62
- Block increment: 64

| Department | Required Hosts | Network Address | Usable Host Range | Broadcast |
|---|---:|---|---|---|
| Engineering | 48 | `172.20.40.0/26` | `172.20.40.1 – 172.20.40.62` | `172.20.40.63` |
| Administration | 40 | `172.20.40.64/26` | `172.20.40.65 – 172.20.40.126` | `172.20.40.127` |
| Commercial | 36 | `172.20.40.128/26` | `172.20.40.129 – 172.20.40.190` | `172.20.40.191` |
| Operations | 30 | `172.20.40.192/26` | `172.20.40.193 – 172.20.40.254` | `172.20.40.255` |

### Router Interface Addressing

| Router | Interface | IPv4 Address | Connected Network |
|---|---|---|---|
| R1 | G0/0 | `172.20.40.1/26` | Engineering |
| R1 | G0/2 | `172.20.40.129/26` | Commercial |
| R1 | G0/1 | `10.10.12.1/30` | Transit to R2 |
| R2 | G0/0 | `172.20.40.65/26` | Administration |
| R2 | G0/1 | `172.20.40.193/26` | Operations |
| R2 | G0/2 | `10.10.12.2/30` | Transit to R1 |

### End-Device Addressing

| Device | IPv4 Address | Subnet Mask | Default Gateway |
|---|---|---|---|
| PC0 | `172.20.40.10` | `255.255.255.192` | `172.20.40.1` |
| PC1 | `172.20.40.20` | `255.255.255.192` | `172.20.40.1` |
| PC4 | `172.20.40.70` | `255.255.255.192` | `172.20.40.65` |
| PC5 | `172.20.40.80` | `255.255.255.192` | `172.20.40.65` |
| PC2 | `172.20.40.140` | `255.255.255.192` | `172.20.40.129` |
| PC3 | `172.20.40.150` | `255.255.255.192` | `172.20.40.129` |
| PC6 | `172.20.40.200` | `255.255.255.192` | `172.20.40.193` |
| PC7 | `172.20.40.210` | `255.255.255.192` | `172.20.40.193` |

## Routing Configuration

### R1 — Static and Default Routing

R1 provides connectivity to the Engineering and Commercial departments.

```cisco
configure terminal

ip route 172.20.40.64 255.255.255.192 10.10.12.2
ip route 0.0.0.0 0.0.0.0 10.10.12.2

end
copy running-config startup-config
```

The specific route directs traffic destined for Administration through R2.

The default route provides a fallback path for destinations without a more specific matching route.

### R2 — Static and Default Routing

R2 provides connectivity to the Administration and Operations departments.

```cisco
configure terminal

ip route 172.20.40.0 255.255.255.192 10.10.12.1
ip route 0.0.0.0 0.0.0.0 10.10.12.1

end
copy running-config startup-config
```

The specific route directs traffic destined for Engineering through R1.

The default route forwards unmatched traffic toward R1.

### Routing Design Consideration

This configuration demonstrates **Longest Prefix Match (LPM)**.

Specific `/26` routes are preferred over the `/0` default route whenever both match the destination address.

Reciprocal default routes are suitable here for demonstrating routing behavior in a controlled lab. However, they can create routing loops for unknown destinations.

In a production network, more explicit routing policies or an appropriate dynamic routing design would be preferable.

## Verification and Testing

Connectivity was tested using ICMP Echo Requests and Echo Replies.

### Verified Connectivity

| Source | Destination | Observed Result |
|---|---|---|
| Engineering | Administration (`172.20.40.80`) | Successful — 2/4 responses |
| Engineering | Commercial (`172.20.40.150`) | Successful — 3/4 responses |
| Operations (`172.20.40.210`) | Engineering (`172.20.40.10`) | Successful — 3/4 responses |
| Operations (`172.20.40.210`) | Commercial (`172.20.40.150`) | Successful — 3/4 responses |

Initial ICMP requests were lost in some tests, while subsequent requests received successful replies.

These results demonstrate reachability between departments. The screenshots alone do not establish the exact reason for the initial packet loss.

### Cisco IOS Verification Commands

```cisco
show ip interface brief
show ip route
show running-config
show startup-config
ping 10.10.12.2
```

Routing tables were inspected to confirm connected (`C`), local (`L`), static (`S`), and candidate default (`S*`) routes.

## Network Troubleshooting and Design Decisions

### Challenge: Limited Routed Interfaces

The initial network design required four independent routed LAN connections.

The selected router provided three usable GigabitEthernet routed interfaces, while the additional FastEthernet switch module exposed Layer 2 switchports rather than independent Layer 3 interfaces.

**Design solution:** The infrastructure was distributed across two routers, preserving the original FLSM subnet assignments.

### Challenge: Transit Network Addressing

The original `/24` address block was fully allocated to the four departmental `/26` networks.

A separate private transit network, `10.10.12.0/30`, was therefore allocated for connectivity between R1 and R2.

This preserved the departmental addressing plan and provided two usable addresses for the point-to-point link.

### Challenge: Inter-Subnet Connectivity

Departmental hosts could communicate locally, but traffic between LANs attached to different routers required additional routing configuration.

**Solution:** Specific static routes and default routes were configured on both routers, followed by routing table inspection and end-to-end ICMP testing.

## Key Technical Takeaways

- FLSM divides a parent network into equal-sized subnet blocks.
- Routers provide Layer 3 communication between different IPv4 subnets.
- Directly connected routes are installed automatically when interfaces are operational.
- Static routes provide manually defined forwarding paths.
- Default routes act as a fallback when no more specific route matches.
- Longest-prefix matching determines which matching route is selected.
- A working router-to-router link does not automatically guarantee reachability to all remote LANs.
- Both forward and return routing must be considered when troubleshooting.
- Infrastructure design must account for interface availability and address-space constraints.

## Project Files

| File | Description |
|---|---|
| `Lab06-FLSM-Subnetting_01.pkt` | Cisco Packet Tracer project |
| `CCNA_Lab06_FLSM_Corporate_Network_Report.pdf` | Technical report with topology, configurations, and screenshots |
| `README.md` | Lab documentation |

## Tools and Technologies

Cisco Packet Tracer · Cisco IOS · IPv4 · FLSM · Static Routing · Default Routing · ICMP · ARP · Ethernet

## Author

**Saúl V. — Systems Engineering Student**

CCNA 200-301 Certification Preparation

[Back to CCNA Networking Labs](../README.md)
