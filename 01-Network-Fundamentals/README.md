# Lab 01 — Basic LAN Connectivity

**CCNA 200-301 | Cisco Packet Tracer**

## Objective

Build and verify a basic Local Area Network (LAN) using two PCs and a Cisco 2960 switch.

This lab demonstrates Ethernet connectivity, IPv4 addressing, ARP, MAC address learning, and ICMP communication.

## Network Topology

PC1 --- SW1 --- PC2

Devices:
- 1 Cisco Catalyst 2960 switch
- 2 PCs
- 2 Ethernet straight-through cables

## IP Addressing

| Device | Interface | IPv4 Address | Subnet Mask |
|---|---|---|---|
| PC1 | FastEthernet0 | 192.168.1.10 | 255.255.255.0 |
| PC2 | FastEthernet0 | 192.168.1.20 | 255.255.255.0 |

Network: `192.168.1.0/24`

No default gateway is required because both PCs are in the same subnet.

## Configuration

1. Connected PC1 to SW1 FastEthernet0/1.
2. Connected PC2 to SW1 FastEthernet0/2.
3. Assigned static IPv4 addresses to both PCs.
4. Configured the switch hostname as SW1.
5. Verified physical connectivity and interface status.

## Verification

Commands used:

- `ping 192.168.1.20`
- `show mac address-table`
- `show interfaces status`

### Results

- ICMP replies: 4 of 4
- Packet loss: 0%
- Both switch ports: connected
- Two dynamic MAC address entries learned

| MAC Address | Switch Port | VLAN |
|---|---|---|
| 00E0.8F78.C635 | Fa0/1 | 1 |
| 0003.E45E.CDBD | Fa0/2 | 1 |

## How It Works

PC1 determines that PC2 belongs to the same IPv4 subnet.

If PC1 does not have PC2's MAC address in its ARP cache, it sends an ARP Request as an Ethernet broadcast.

SW1 learns PC1's source MAC address on Fa0/1 and forwards the broadcast through other active ports in the VLAN.

PC2 responds with an ARP Reply containing its MAC address.

SW1 learns PC2's MAC address on Fa0/2. The switch can then forward known unicast frames through the appropriate interface.

## Troubleshooting and Lessons Learned

The network passed connectivity testing without requiring a router.

This lab helped me understand:

- The difference between MAC and IPv4 addresses.
- How switches learn source MAC addresses.
- The function of ARP Request and ARP Reply.
- The difference between broadcast and unicast traffic.
- How to verify a LAN using Cisco IOS commands.

## Evidence

Packet Tracer topology, ping results, switch MAC address table, and Simulation Mode captures are included in this project's evidence folder.

## Tools

Cisco Packet Tracer

## Reference

Jeremy's IT Lab — CCNA 200-301, Network Fundamentals and Ethernet LAN Switching.
