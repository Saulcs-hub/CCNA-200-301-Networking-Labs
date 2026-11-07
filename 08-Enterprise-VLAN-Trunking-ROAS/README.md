# Lab 08 — Enterprise VLANs, 802.1Q Trunking & Router-on-a-Stick

## Overview

This laboratory demonstrates the design, implementation, validation, and troubleshooting of a segmented enterprise network using Cisco Packet Tracer.

The network consists of two Cisco Catalyst 2960 switches, one Cisco 2911 router, and eight end devices.

The primary goal is to implement VLAN segmentation, IEEE 802.1Q trunking, a dedicated native VLAN, and inter-VLAN routing using Router-on-a-Stick.

**Certification:** Cisco CCNA 200-301  
**Learning Resource:** Jeremy's IT Lab — VLANs (Days 16–17)  
**Platform:** Cisco Packet Tracer

---

## Network Topology

The topology includes:

- **R1:** Cisco 2911 router performing inter-VLAN routing.
- **SW1:** Cisco 2960 distribution switch connected to R1.
- **SW2:** Cisco 2960 access switch connected to SW1.
- **PC1–PC8:** End devices distributed across four departmental VLANs.

### Physical Connections

| Device | Interface | Connected To | Interface |
|---|---|---|---|
| R1 | Gi0/0 | SW1 | Gi0/1 |
| SW1 | Gi0/2 | SW2 | Gi0/1 |
| SW1 | Fa0/1–Fa0/4 | PC1–PC4 | Fa0 |
| SW2 | Fa0/1–Fa0/4 | PC5–PC8 | Fa0 |

---

## VLAN and IP Addressing Plan

The network was subnetted from the original IPv4 block `192.168.80.0/24` using Variable Length Subnet Masking (VLSM).

| VLAN | Department | Network | Subnet Mask | Default Gateway |
|---|---|---|---|---|
| 10 | Engineering | 192.168.80.0/26 | 255.255.255.192 | 192.168.80.62 |
| 20 | Administration | 192.168.80.64/26 | 255.255.255.192 | 192.168.80.126 |
| 30 | Development | 192.168.80.128/27 | 255.255.255.224 | 192.168.80.158 |
| 40 | IT Support | 192.168.80.160/28 | 255.255.255.240 | 192.168.80.174 |
| 999 | Native Unused | Not assigned | — | — |

Each department operates in an independent Layer 2 broadcast domain.

---

## Learning Objectives

- Configure VLANs and assign switch access ports.
- Understand VLAN segmentation and broadcast domain separation.
- Configure IEEE 802.1Q trunk links between network devices.
- Configure and verify a dedicated native VLAN.
- Restrict allowed VLANs on trunk interfaces.
- Implement Router-on-a-Stick using Cisco IOS subinterfaces.
- Verify directly connected IPv4 routes.
- Validate communication within and between VLANs.
- Troubleshoot Layer 2 connectivity problems.
- Identify native VLAN mismatches and VLAN-related STP inconsistencies.

---

## VLAN Configuration

Five VLANs were created on both switches:

```cisco
vlan 10
 name ENGINEERING
vlan 20
 name ADMINISTRATION
vlan 30
 name DEVELOPMENT
vlan 40
 name IT_SUPPORT
vlan 999
 name NATIVE_UNUSED
```

Each PC-facing FastEthernet interface was configured as an access port and assigned to its corresponding VLAN.

Example:

```cisco
interface fastEthernet 0/1
 switchport mode access
 switchport access vlan 10
```

Verification command:

```cisco
show vlan brief
```

---

## IEEE 802.1Q Trunk Configuration

Two trunk links were implemented:

1. SW1 Gi0/1 ↔ R1 Gi0/0
2. SW1 Gi0/2 ↔ SW2 Gi0/1

Trunk configuration example:

```cisco
interface gigabitEthernet 0/2
 switchport mode trunk
 switchport trunk allowed vlan 10,20,30,40
 switchport trunk native vlan 999
```

VLAN 999 was reserved as an unused native VLAN, without end-user devices.

The native VLAN was configured consistently on both ends of the switch-to-switch trunk.

Verification:

```cisco
show interfaces trunk
show interfaces gigabitEthernet 0/2 switchport
```

---

## Inter-VLAN Routing — Router-on-a-Stick

R1 provides Layer 3 connectivity between the four departmental VLANs.

Each subinterface uses IEEE 802.1Q encapsulation and serves as the default gateway for its corresponding subnet.

```cisco
interface gigabitEthernet 0/0.10
 encapsulation dot1Q 10
 ip address 192.168.80.62 255.255.255.192

interface gigabitEthernet 0/0.20
 encapsulation dot1Q 20
 ip address 192.168.80.126 255.255.255.192

interface gigabitEthernet 0/0.30
 encapsulation dot1Q 30
 ip address 192.168.80.158 255.255.255.224

interface gigabitEthernet 0/0.40
 encapsulation dot1Q 40
 ip address 192.168.80.174 255.255.255.240
```

R1 learns all four networks as directly connected routes.

Verification:

```cisco
show ip interface brief
show ip route
```

No additional static routes are required for communication between these four directly connected subnets.

---

## Connectivity Validation

ICMP tests were performed to verify communication between devices belonging to the same VLAN and to different VLANs.

Successful tests confirmed:

- VLAN 10 connectivity between switches.
- Inter-VLAN routing through R1.
- Communication between departments across both switches.
- Correct operation of access ports and trunk links.

The routing table and interface status were verified using Cisco IOS commands.

---

## Troubleshooting Scenarios

### Scenario 1 — Incorrect Access VLAN Assignment

**Problem:** PC5 was temporarily moved from VLAN 10 to VLAN 20 while retaining its original IPv4 address.

**Observed Result:** ICMP connectivity from PC1 to PC5 failed with 100% packet loss.

**Root Cause:** The devices remained in the same IPv4 subnet but were separated into different Layer 2 broadcast domains.

**Resolution:**

```cisco
interface fastEthernet 0/1
 switchport access vlan 10
```

**Validation:** Connectivity was restored, with four successful ICMP replies.

### Scenario 2 — VLAN Excluded from Trunk

**Problem:** VLAN 30 was removed from the allowed VLAN list on SW2 Gi0/1.

```cisco
switchport trunk allowed vlan 10,20,40
```

**Observed Result:** Devices in VLAN 30 connected to SW2 could no longer communicate across the trunk.

**Root Cause:** VLAN 30 traffic was not permitted to traverse the SW1–SW2 link.

**Resolution:**

```cisco
switchport trunk allowed vlan add 30
```

**Validation:** VLAN 30 was restored to the allowed list, and connectivity tests succeeded.

### Scenario 3 — Native VLAN Mismatch

**Problem:** A native VLAN inconsistency was observed between SW1 and SW2 during trunk configuration.

**Observed Messages:**

```text
%CDP-4-NATIVE_VLAN_MISMATCH
%SPANTREE-2-RECV_PVID_ERR
%SPANTREE-2-BLOCK_PVID_LOCAL
```

**Root Cause:** The two trunk interfaces used different native VLAN IDs.

**Resolution:** Both ends were configured with native VLAN 999.

**Validation:** Cisco IOS reported restoration of port consistency.

---

## Technical Verification Commands

```cisco
show vlan brief
show interfaces trunk
show interfaces gigabitEthernet 0/1 switchport
show ip interface brief
show ip route
show running-config
```

End-to-end connectivity was evaluated using ICMP ping tests from the Packet Tracer PCs.

---

## Project Files

- **[Technical Report — PDF](CCNA_Lab08_Enterprise_VLAN_Trunking_ROAS_Report.pdf)** — Complete laboratory documentation, screenshots, validation results, and troubleshooting analysis.
- **Lab08-Enterprise-VLANs.pkt** — Cisco Packet Tracer project.

The PDF contains the implementation evidence and troubleshooting screenshots.

---

## Key Takeaways

This laboratory reinforced the relationship between Layer 2 VLAN segmentation and Layer 3 inter-VLAN routing.

Key concepts demonstrated include:

- VLANs separate broadcast domains.
- Access ports assign end devices to specific VLANs.
- Trunk ports transport multiple VLANs using IEEE 802.1Q.
- The native VLAN is distinct from the allowed VLAN list.
- Router-on-a-Stick provides routing between VLANs through router subinterfaces.
- A VLAN configuration error can disrupt connectivity even when IPv4 addressing is correct.
- Cisco IOS verification commands are essential for identifying and resolving network issues.

---

**Project:** CCNA 200-301 Networking Labs  
**Lab:** 08 — Enterprise VLANs, Trunking & Router-on-a-Stick  
**Tools:** Cisco Packet Tracer, Cisco IOS  
**Topics:** VLSM, VLANs, Ethernet Switching, 802.1Q, Native VLAN, ROAS, Troubleshooting
