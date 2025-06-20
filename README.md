# LF9
1. Introduction to LF9

The company Streamline, headquartered in Hamburg, has three additional locations: Lübeck, Berlin, and Munich. All four branches are interconnected and currently use IPv4 with NAT. However, this setup has caused ongoing issues, which are only mitigated through various workaround protocols.

The management has now commissioned B\@ltic Networks to redesign the site networking using IPv6 exclusively.

Table of Contents
1. Introduction
2. Network Simulation
- 2.1. Network Topology
- 2.1.2. IPv6 Addressing Scheme
- 2.1.3. VLAN Segmentation
- 2.1.4. Static Routing Configuration
- 2.1.5 IPAM-Table (IPv6)
- 2.2.1. IPv6 Implementation
- 2.2.2. OSPFv3 Dynamic Routing






2. Network Simulation

2.1. Network Topology

Core Devices:
- Routers: Cisco 4331 (RT-HH-01, RT-HL-01, RT-B-01, RT-M-01).
- Switches: Cisco 3650 L3 (SW-HH-01, SW-HL-01, SW-B-01, SW-M-01).

Transport Links:
- Bandwidth: 2000 Kbps per link.
- Serial connections between routers (e.g., RT-HH-01 ↔ RT-HL-01).
  
2.1.2. IPv6 Addressing Scheme

IPv6 Adressing Scheme
-Provider Independent Space: 2001:db8::/64
-Site Subnets:

Hamburg, Client Network 1
(HH)
Vlan: 10,
Subnet: 2001:db8:1:10::/64,
Gateway: 2001:db8:1:10::1

Hamburg, Client Network 2
(HH)
Vlan: 20,
Subnet: 2001:db8:1:20::/64,
Gateway: 2001:db8:1:20::1

Lübeck, Client Network 3
(HL)
Vlan: 30,
Subnet: 2001:db8:2:30::/64,
Gateway: 2001:db8:2:30::1

Lübeck (HL), Server Network
Vlan: 40
Subnet: 2001:db8:2:40::/64 
Gateway: 2001:db8:2:40::1

Berlin, Webserver
(B)
Vlan: 50,
Subnet: 2001:db8:3:50::/64, 
Gateway: 2001:db8:3:50::1

Munich, Webserver
(M)
Vlan: 60,
Subnet: 2001:db8:4:60::/64,
Gateway: 2001:db8:4:60::1

2.1.3. VLAN Segmentation

- B & M: Single VLAN for servers.
- HH & HL: Two VLANs per site (Clients + Management).

- Hamburg (HH): VLAN 10 (Clients), VLAN 20 (Management).
- Lübeck (HL): VLAN 30 (Clients), VLAN 40 (Servers).
- Berlin (B): VLAN 50 (Webserver).
- Munich (M): VLAN 60 (Webserver).

2.1.4. Static Routing

- Transport Networks (ULA): fd00:X::/64 (X = connection ID).

2.2.1. IPv6 Implementation

- Global Unicast for Clients/Servers:
Host Addressing: 2001:db8:<Site>:<VLAN>:<MAC-based-ID>/64
- ULA for Router Links:
fd00:<Link-ID>::<DCE=1/DTE=2>/127

