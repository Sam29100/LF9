# LF9
#Introduction to LF9

The company Streamline, headquartered in Hamburg, has three additional locations: Lübeck, Berlin, and Munich. All four branches are interconnected and currently use IPv4 with NAT. However, this setup has caused ongoing issues, which are only mitigated through various workaround protocols.

The management has now commissioned B\@ltic Networks to redesign the site networking using IPv6 exclusively.

















#IPv6 Adressing Scheme
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
