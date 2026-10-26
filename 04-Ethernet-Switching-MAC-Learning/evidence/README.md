
# Lab 04 — Ethernet Switching & MAC Learning Evidence

**Cisco CCNA 200-301 | Cisco Packet Tracer**

## Overview

This folder contains 16 screenshots collected during Lab 04.

The screenshots document the network topology, Cisco IOS MAC address tables, ARP resolution, ICMP traffic, Ethernet frame forwarding, and a controlled switch interface shutdown.

The evidence was collected using Cisco Packet Tracer and its Simulation Mode.

## Network Topology and Initial Configuration

### Evidence 01

![Lab 04 Screenshot 01](LAB04-img1.png)

### Evidence 02

![Lab 04 Screenshot 02](LAB04-img2.png)

### Evidence 03

![Lab 04 Screenshot 03](LAB04-img3.png)

### Evidence 04

![Lab 04 Screenshot 04](LAB04-img4.png)

## Switching and Protocol Analysis

### Evidence 05

![Lab 04 Screenshot 05](LAB04-img5.png)

### Evidence 06

![Lab 04 Screenshot 06](LAB04-img6.png)

### Evidence 07

![Lab 04 Screenshot 07](LAB04-img7.png)

### Evidence 08

![Lab 04 Screenshot 08](LAB04-img8.png)

### Evidence 09

![Lab 04 Screenshot 09](LAB04-img9.png)

### Evidence 10

![Lab 04 Screenshot 10](LAB04-img10.png)

### Evidence 11

![Lab 04 Screenshot 11](LAB04-img11.png)

### Evidence 12

![Lab 04 Screenshot 12](LAB04-img12.png)

## Connectivity Verification and Troubleshooting

### Evidence 13

![Lab 04 Screenshot 13](LAB04-img13.png)

### Evidence 14

![Lab 04 Screenshot 14](LAB04-img14.png)

### Evidence 15

![Lab 04 Screenshot 15](LAB04-img15.png)

### Evidence 16

![Lab 04 Screenshot 16](LAB04-img16.png)

---

## Verified Laboratory Results

| Verification | Result |
|---|---|
| Four-host Ethernet LAN | Operational |
| SW1 and SW2 interconnection | Operational |
| IPv4 addressing | Verified |
| Dynamic MAC learning | Verified |
| ARP Request and Reply | Observed |
| ICMP connectivity PC1 to PC3 | 0% packet loss in final test |
| PC1 ARP cache | PC3 IP-to-MAC association verified |
| SW2 Fa0/2 administrative shutdown | Verified |
| PC1 to PC3 communication during PC4 isolation | Successful |

## Technical Summary

The screenshots demonstrate the forwarding behavior of Cisco Layer 2 switches, including source MAC learning and Ethernet frame forwarding.

ARP was used to resolve the destination host's IPv4 address to a MAC address before normal unicast ICMP communication.

The final tests confirmed successful connectivity between PC1 and PC3, including during the administrative shutdown of PC4's access port.

These observations were used to validate the network's behavior and understand Layer 2 troubleshooting.

---

[Return to Lab 04 Documentation](../README.md)

**Tools:** Cisco Packet Tracer  
**Certification:** Cisco CCNA 200-301  
**Reference:** Jeremy's IT Lab — Ethernet LAN Switching
