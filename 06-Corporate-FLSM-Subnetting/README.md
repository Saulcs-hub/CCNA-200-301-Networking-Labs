Lab 07 — Corporate Network: FLSM Subnetting & Static Routing
Cisco Packet Tracer implementation of four equal-sized corporate LAN subnets using 172.20.40.0/24, two routers, and a dedicated 10.10.12.0/30 transit link.
Learning objectives
- Calculate and assign four /26 IPv4 subnet blocks with fixed-length subnet masks.
- Configure and validate routed router interfaces and PC default gateways.
- Apply static and default routes with longest-prefix matching.
- Validate inter-LAN connectivity and document design constraints.
Addressing
Department	Subnet	Router gateway	Sample hosts
Engineering	172.20.40.0/26	172.20.40.1	.10, .20
Administration	172.20.40.64/26	172.20.40.65	.70, .80
Commercial	172.20.40.128/26	172.20.40.129	.140, .150
Operations	172.20.40.192/26	172.20.40.193	.200, .210


Transit: R1 G0/1 = 10.10.12.1/30; R2 G0/2 = 10.10.12.2/30.
Routing
R1:
ip route 172.20.40.64 255.255.255.192 10.10.12.2
ip route 0.0.0.0 0.0.0.0 10.10.12.2
R2:
ip route 172.20.40.0 255.255.255.192 10.10.12.1
ip route 0.0.0.0 0.0.0.0 10.10.12.1
Evidence and validation
See [the complete PDF report](CCNA_Lab07_FLSM_Corporate_Network_Report.pdf) for the topology, final IOS configuration, routing tables, validation pings, and eight original Packet Tracer screenshots.

Some initial ping packets were lost during tests; later replies confirm inter-subnet reachability. The screenshots alone do not isolate the cause of initial timeouts.
Design note
The four /26 LAN blocks consume the entire assigned /24, so a separate RFC 1918 /30 was allocated for router transit. Two routers provide sufficient routed interfaces. Reciprocal default routes are acceptable for the demonstrated destinations but can cause loops to unknown destinations; this is a contained teaching lab.
Files
- README.md — design and implementation overview
- CCNA_Lab07_FLSM_Corporate_Network_Report.pdf — professional report with all screenshots
- Lab07-Corporate-FLSM.pkt — add the final Packet Tracer project from your computer
Tools
Cisco Packet Tracer; Cisco IOS commands show ip interface brief, show ip route, ping, and copy running-config startup-config.
