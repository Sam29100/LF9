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
3. Security Hardening
  - 3.1. SSH & Password Policies
4. Automation & Optimization
  - 4.1. SLAAC & DHCPv6 for Clients
  - 4.2. OSPFv3 for Dynamic Routing





2. Network Simulation

2.1. Network Topology

Core Devices:
  - Routers: Cisco 4331 (RT-HH-01, RT-HL-01, RT-B-01, RT-M-01).
  - Switches: Cisco 3650 L3 (SW-HH-01, SW-HL-01, SW-B-01, SW-M-01).

Transport Links:
  - Bandwidth: 2000 Kbps per link.
  - Serial connections between routers (e.g., RT-HH-01 ↔ RT-HL-01).
  
2.1.2. IPv6 Addressing Scheme

| Standort    | VLAN | Netzwerkzweck      | Subnet                   | Gateway                  |
|-------------|------|---------------------|---------------------------|---------------------------|
| Hamburg     | 10   | Client Network 1    | 2001:db8:1:10::/64        | 2001:db8:1:10::1          |
| Hamburg     | 20   | Client Network 2    | 2001:db8:1:20::/64        | 2001:db8:1:20::1          |
| Lübeck      | 30   | Client Network 3    | 2001:db8:2:30::/64        | 2001:db8:2:30::1          |
| Lübeck      | 40   | Client Network 4    | 2001:db8:2:40::/64        | 2001:db8:2:40::1          |
| Berlin      | 50   | Webserver           | 2001:db8:3:50::/64        | 2001:db8:3:50::1          |
| München     | 60   | Webserver           | 2001:db8:4:60::/64        | 2001:db8:4:60::1          |


2.1.3. VLAN Segmentation

- B & M: Single VLAN for servers.

  - Berlin (B): VLAN 50 (Webserver).
  - Munich (M): VLAN 60 (Webserver).

- HH & HL: Two VLANs per site (Clients + Management).

  - Hamburg (HH): VLAN 10 (Clients), VLAN 20 (Management).
  - Lübeck (HL): VLAN 30 (Clients), VLAN 40 (Servers).


2.1.4. Static Routing

- Transport Networks (ULA): fd00:X::/64 (X = connection ID).

2.1.5. IPAM-Table (IPv6)

| Device    | Interface/VLAN     | IPv6 Address         | Subnet Mask | Type        | Gateway | Purpose                        |
|-----------|---------------------|-----------------------|-------------|-------------|---------|--------------------------------|
| SW-HH-01  | VLAN 10             | N/A (L2 Switch)       | N/A         | N/A         | 2001:DB8:1:10::1 | Client Access (VLAN 10)         |
|           | VLAN 20             | N/A (L2 Switch)       | N/A         | N/A         | 2001:DB8:1:20::1 | Client Access (VLAN 20)         |
| SW-HL-01  | VLAN 30             | N/A (L2 Switch)       | N/A         | N/A         | 2001:DB8:2:30::1 | Client Access (VLAN 30)         |
|           | VLAN 40             | N/A (L2 Switch)       | N/A         | N/A         | 2001:DB8:2:40::1 | Server Access (VLAN 40)         |
| SW-B-01   | VLAN 50             | N/A (L2 Switch)       | N/A         | N/A         | 2001:DB8:3:50::1 | Webserver Access (VLAN 50)      |
| SW-M-01   | VLAN 60             | N/A (L2 Switch)       | N/A         | N/A         | 2001:DB8:4:60::1 | Webserver Access (VLAN 60)      |
| RT-HH-01  | Gig0/0/0.10         | 2001:DB8:1:10::1      | /64         | Static      | N/A     | VLAN 10 (Client Net 1)         |
|           | Gig0/0/0.20         | 2001:DB8:1:20::1      | /64         | Static      | N/A     | VLAN 20 (Client Net 2)         |
|           | Serial0/1/0         | FD00:1::1             | /127        | Static      | N/A     | HH ↔ HL Transport (ULA)        |
|           | Serial0/1/1         | FD00:2::1             | /127        | Static      | N/A     | HH ↔ B Transport (ULA)         |
|           | Serial0/2/0         | FD00:3::1             | /127        | Static      | N/A     | HH ↔ M Transport (ULA)         |
| RT-HL-01  | Gig0/0/0.30         | 2001:DB8:2:30::1      | /64         | Static      | N/A     | VLAN 30 (Client Net 3)         |
|           | Gig0/0/0.40         | 2001:DB8:2:40::1      | /64         | Static      | N/A     | VLAN 40 (Server Net)           |
|           | Serial0/1/0         | FD00:1::2             | /127        | Static      | N/A     | HL ↔ HH Transport (ULA)        |
|           | Serial0/1/1         | FD00:4::1             | /127        | Static      | N/A     | HL ↔ B Transport (ULA)         |
|           | Serial0/2/0         | FD00:6::2             | /127        | Static      | N/A     | HL ↔ M Transport (ULA)         |
| RT-B-01   | Gig0/0/0            | 2001:DB8:3:50::1      | /64         | Static      | N/A     | VLAN 50 (Webserver)            |
|           | Serial0/1/0         | FD00:2::2             | /127        | Static      | N/A     | B ↔ HH Transport (ULA)         |
|           | Serial0/1/1         | FD00:4::2             | /127        | Static      | N/A     | B ↔ HL Transport (ULA)         |
|           | Serial0/2/0         | FD00:5::1             | /127        | Static      | N/A     | B ↔ M Transport (ULA)          |
| RT-M-01   | Gig0/0/0            | 2001:DB8:4:60::1      | /64         | Static      | N/A     | VLAN 60 (Webserver)            |
|           | Serial0/1/0         | FD00:3::2             | /127        | Static      | N/A     | M ↔ HH Transport (ULA)         |
|           | Serial0/1/1         | FD00:5::2             | /127        | Static      | N/A     | M ↔ B Transport (ULA)          |
|           | Serial0/2/0         | FD00:6::1             | /127        | Static      | N/A     | M ↔ HL Transport (ULA)         |






2.2.1. IPv6 Implementation

- Global Unicast for Clients/Servers:
Host Addressing: 2001:db8:<Site>:<VLAN>:<MAC-based-ID>/64
- ULA for Router Links:
fd00:<Link-ID>::<DCE=1/DTE=2>/127

2.2.2. OSPFv3 Dynamic Routing
- Router IDs:

  - RT-HH-01: 10.0.1.1
  - RT-HL-01: 10.0.2.1
  - RT-B-01: 10.0.3.1
  - RT-M-01: 10.0.4.1

- OSPFv3 Configuration:

  - interface GigabitEthernet0/0/0
  - ipv6 ospf 1 area 0

3. Security Hardening

3.1. SSH & Password Policies

- SSH Setup:

username admin secret P@ssw0rd

ip domain-name streamline.de

crypto key generate rsa

line vty 0 4

transport input ssh

- Banner:
banner motd #Unauthorized access prohibited!#


4. Automation & Optimization


4.1. SLAAC & DHCPv6 for Clients

- Clients auto-configure via SLAAC.
- DHCPv6 for DNS & NTP:

ipv6 dhcp pool VLAN10

dns-server 2001:db8:1:10::100

domain-name streamline.de

4.2. OSPFv3 for Dynamic Routing

- Enables automatic route updates.
- Config Example:

ipv6 router ospf 1




https://docs.google.com/document/d/1Th2YHLg91TuQ-xBZBypIEwJU1t4ofukxBOwxhVeOy_4/edit?usp=sharing


router-id 10.0.1.1
