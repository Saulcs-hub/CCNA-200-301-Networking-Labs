# Lab 09 — Multilayer Switching & Inter-VLAN Routing

**Cisco CCNA 200-301 | Jeremy's IT Lab — Day 18**

## Overview

This laboratory explores the implementation of inter-VLAN routing using Cisco multilayer switches.

Unlike traditional Router-on-a-Stick architectures, a Layer 3 switch can perform routing between VLANs internally through Switch Virtual Interfaces (SVIs).

The project includes two practical implementations:

1. **Multilayer Switching from Scratch:** Building a segmented network with a Cisco Catalyst 3560, configuring SVIs, enabling IPv4 routing, and connecting a remote server through an external router.
2. **Jeremy's IT Lab — Day 18:** Migrating an existing Router-on-a-Stick topology to a Layer 3 switching architecture while maintaining network connectivity.

The laboratory also reinforces static routing, default routes, routed ports, and network troubleshooting.

---

## Learning Objectives

- Understand the differences between Layer 2 and Layer 3 switches.
- Configure VLANs and assign access ports.
- Implement Switch Virtual Interfaces (SVIs).
- Enable inter-VLAN routing using `ip routing`.
- Understand the operational requirements of SVIs.
- Convert physical switchports into Layer 3 routed interfaces.
- Configure IPv4 point-to-point links.
- Implement default routes and static return routes.
- Analyze routing tables and connected networks.
- Verify connectivity using ICMP and traceroute.
- Migrate an existing Router-on-a-Stick network to multilayer switching.

---

# Part 1 — Multilayer Switching from Scratch

## Network Architecture

The initial topology consists of:

- One Cisco Catalyst 3560 multilayer switch.
- Three PCs distributed across different VLANs.
- One Cisco router acting as an external gateway.
- One remote server connected to the external router.

The multilayer switch operates as the default gateway for all internal VLANs.

## VLAN and IP Addressing

| VLAN | Department | Network | SVI / Default Gateway |
|---|---|---|---|
| 10 | Engineering | 192.168.80.0/26 | 192.168.80.62 |
| 20 | Administration | 192.168.80.64/26 | 192.168.80.126 |
| 30 | Development | 192.168.80.128/27 | 192.168.80.158 |

### End Devices

| Device | IP Address | VLAN |
|---|---|---|
| PC1 | 192.168.80.1/26 | 10 |
| PC2 | 192.168.80.65/26 | 20 |
| PC3 | 192.168.80.129/27 | 30 |

---

## SVI Configuration

The following Layer 3 interfaces were created on SW-CORE:

```cisco
interface vlan 10
 description GATEWAY-ENGINEERING
 ip address 192.168.80.62 255.255.255.192
 no shutdown

interface vlan 20
 description GATEWAY-ADMINISTRATION
 ip address 192.168.80.126 255.255.255.192
 no shutdown

interface vlan 30
 description GATEWAY-DEVELOPMENT
 ip address 192.168.80.158 255.255.255.224
 no shutdown
```

Each SVI provides a Layer 3 gateway for its corresponding VLAN.

## Enabling IPv4 Routing

```cisco
ip routing
```

Before enabling IPv4 routing, the PCs successfully communicated with their local SVIs but could not communicate between VLANs.

After enabling `ip routing`, inter-VLAN communication was successfully established.

This demonstrated the distinction between an operational SVI and an active Layer 3 routing function.

---

## Routed Port Configuration

A physical switch interface was converted into a routed port using:

```cisco
interface gigabitEthernet 0/1
 no switchport
 ip address 10.10.10.1 255.255.255.252
 no shutdown
```

The external router was configured with:

```cisco
interface gigabitEthernet 0/0
 ip address 10.10.10.2 255.255.255.252
 no shutdown
```

The routed port provides direct Layer 3 connectivity without using 802.1Q trunking.

---

## Default Routing

SW-CORE was configured with a default route pointing toward the external router:

```cisco
ip route 0.0.0.0 0.0.0.0 10.10.10.2
```

The external router was configured with static routes toward the internal VLAN networks.

A remote server network was implemented:

| Device | IP Address |
|---|---|
| R2-EDGE | 172.16.50.1/24 |
| SERVER-01 | 172.16.50.10/24 |

Successful ICMP communication between PC1 and SERVER-01 demonstrated routing across multiple Layer 3 devices.

---

# Part 2 — Jeremy's IT Lab: Router-on-a-Stick Migration

## Scenario

An existing network was originally configured using Router-on-a-Stick.

The objective was to migrate the inter-VLAN routing functionality from R1 to a multilayer switch, SW2.

The new design required:

- Removing the previous router subinterfaces.
- Converting the SW2-to-R1 connection into a routed port.
- Configuring SVIs for VLANs 10, 20, and 30.
- Enabling IPv4 routing on SW2.
- Configuring a default route toward R1.
- Adding static return routes on R1.
- Verifying inter-VLAN and external network connectivity.

## Point-to-Point Network

| Device | Interface | IP Address |
|---|---|---|
| SW2 | Gi1/0/2 | 10.0.0.193/30 |
| R1 | Gi0/0 | 10.0.0.194/30 |

The point-to-point network is `10.0.0.192/30`.

### SW2 Routed Port

```cisco
interface gigabitEthernet 1/0/2
 no switchport
 ip address 10.0.0.193 255.255.255.252
 no shutdown
```

### R1 Interface

```cisco
interface gigabitEthernet 0/0
 ip address 10.0.0.194 255.255.255.252
 no shutdown
```

## Multilayer Switch SVI Configuration

| VLAN | Network | Gateway |
|---|---|---|
| 10 | 10.0.0.0/26 | 10.0.0.62 |
| 20 | 10.0.0.64/26 | 10.0.0.126 |
| 30 | 10.0.0.128/26 | 10.0.0.190 |

The existing VLAN gateways were migrated from R1 subinterfaces to SW2 SVIs.

This allowed the end devices to retain their original default gateway IP addresses.

## Routing Configuration

### Default Route on SW2

```cisco
ip route 0.0.0.0 0.0.0.0 10.0.0.194
```

### Static Return Routes on R1

```cisco
ip route 10.0.0.0 255.255.255.192 10.0.0.193
ip route 10.0.0.64 255.255.255.192 10.0.0.193
ip route 10.0.0.128 255.255.255.192 10.0.0.193
```

These routes provide return connectivity from R1 toward the internal VLAN networks.

---

# Verification and Troubleshooting

## Cisco IOS Verification Commands

```cisco
show vlan brief
show ip interface brief
show ip route
show running-config
show interfaces gigabitEthernet 1/0/2 switchport
ping
traceroute
```

## Key Troubleshooting Findings

### Missing IPv4 Configuration on R1

**Symptom:** The multilayer switch could not ping the router across the point-to-point connection.

**Diagnosis:** SW2 Gi1/0/2 was correctly configured, but R1 Gi0/0 still retained its previous Router-on-a-Stick configuration and did not have the required point-to-point IPv4 address.

**Resolution:** Remove the obsolete router subinterfaces and configure `10.0.0.194/30` on R1 Gi0/0.

**Result:** Connectivity was restored successfully.

### Routing and Return Paths

The migration also required verifying that SW2 could forward unknown destinations to R1 and that R1 had routes toward the VLAN subnets.

Static routes were configured to establish the required return paths.

### Traceroute Analysis

A traceroute was performed from PC1 toward the simulated external destination `1.1.1.1`.

The observed route was:

```text
1    10.0.0.62
2    10.0.0.194
3    1.1.1.1
```

This demonstrated the forwarding path through the multilayer switch, R1, and the simulated external network.

The `1.1.1.1` destination is part of the Packet Tracer exercise and does not prove access to the public Internet.

---

## Results

The laboratory demonstrated successful:

- Communication with the three SVI gateways.
- Inter-VLAN routing after enabling `ip routing`.
- Layer 3 connectivity between a multilayer switch and an external router.
- Communication with a remote server network.
- Migration from Router-on-a-Stick to SVI-based inter-VLAN routing.
- Point-to-point connectivity between SW2 and R1.
- Traceroute analysis toward a simulated external destination.

---

## Project Documentation

**[Download / View Complete Technical Report (PDF)](CCNA_Lab09_Complete_With_Jeremy_Day18.pdf)**

The technical report contains the implementation details, Cisco IOS outputs, topology screenshots, connectivity tests, and troubleshooting analysis.

Packet Tracer project files are maintained separately from the PDF documentation.

---

## Key Takeaways

This laboratory demonstrates how multilayer switching combines traditional Ethernet switching with IPv4 routing.

Unlike Router-on-a-Stick, an SVI-based architecture can route traffic between VLANs internally without requiring every inter-VLAN packet to traverse an external router.

Additionally, routed ports allow multilayer switches to establish direct Layer 3 links with other network devices.

The exercises reinforced the importance of routing table analysis, gateway configuration, static return routes, and systematic troubleshooting.

---

**Certification Track:** Cisco CCNA 200-301  
**Course:** Jeremy's IT Lab — Day 18  
**Lab Number:** 09  
**Technologies:** VLANs, SVIs, Multilayer Switching, Routed Ports, Static Routing, Default Routes, ICMP, Traceroute  
**Platform:** Cisco Packet Tracer
