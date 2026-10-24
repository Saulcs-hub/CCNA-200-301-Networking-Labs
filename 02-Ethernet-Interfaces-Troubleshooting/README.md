# Lab 02 — Ethernet Interfaces & Troubleshooting

**CCNA 200-301 | Cisco Packet Tracer**

## Objective

Build a Local Area Network with two Cisco Layer 2 switches and two PCs, verify Ethernet interface status, simulate an administrative interface failure, and restore connectivity using Cisco IOS commands.

## Network Topology

PC1 --- SW1 --- SW2 --- PC2

**Devices:**
- 2 Cisco Catalyst 2960 switches
- 2 PCs
- Copper straight-through cables for PC-to-switch connections
- Copper cross-over cable between SW1 and SW2

## IP Addressing

| Device | IPv4 Address | Subnet Mask |
|---|---|---|
| PC1 | 192.168.1.10 | 255.255.255.0 |
| PC2 | 192.168.1.20 | 255.255.255.0 |

Both devices are in the same IPv4 subnet.

## Initial Verification

On SW1, I verified the interfaces using:

- `show interfaces status`
- `show interfaces fastEthernet 0/24`
- `show ip interface brief`

Before introducing the failure, the switch interfaces were operational.

FastEthernet0/24 was connected to SW2.

## Failure Simulation

I intentionally disabled the link between SW1 and SW2 using:

```text
enable
configure terminal
interface fastEthernet 0/24
shutdown
```

The switch reported that FastEthernet0/24 had changed to an administratively down state.

## Troubleshooting

I examined the interface using:

```text
show interfaces status
show interfaces fastEthernet 0/24
show ip interface brief
```

The interface appeared as disabled or administratively down, with the line protocol down.

An ICMP connectivity test from PC1 to PC2 failed:

**4 packets transmitted, 0 received, 100% packet loss.**

### Root Cause

The failure was caused by an administrative shutdown of FastEthernet0/24 on SW1.

This interface was the only connection between SW1 and SW2, so disabling it interrupted Layer 2 connectivity between the two PCs.

## Resolution

I restored the interface with:

```text
configure terminal
interface fastEthernet 0/24
no shutdown
```

Cisco IOS reported that the interface and line protocol returned to the up state.

A final ICMP test should be performed to confirm end-to-end connectivity.

## Lessons Learned

This lab helped me understand:

- Default administrative interface behavior on Cisco switches.
- The difference between `shutdown` and `no shutdown`.
- How to distinguish operational and administrative interface states.
- How to interpret Cisco IOS interface status commands.
- How a Layer 1/Layer 2 link failure affects communication between devices.
- How to identify and resolve a simulated network outage.

## Tools

Cisco Packet Tracer

## Reference

Jeremy's IT Lab — CCNA 200-301, Interfaces and Cables; Switch Interfaces.
