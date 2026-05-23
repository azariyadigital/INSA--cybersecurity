Day 04 — Networking Basics

Date: March 22, 2026

What is a Network?

A network is a collection of devices connected to share resources and communicate.

Examples:

Two computers connected by a cable
All devices on your home WiFi
The entire internet
Types of Networks

LAN (Local Area Network)

Small area: home, office, school
Fast, privately owned
Example: your home WiFi

MAN (Metropolitan Area Network)

City-wide, connects multiple LANs
Example: university campus network

WAN (Wide Area Network)

Global, connects cities and countries
Example: the Internet
IP Address (Internet Protocol Address)
Like a home address for devices on a network
Needed for communication

IPv4: 32-bit, ~4 billion addresses, e.g., 192.168.1.1
IPv6: 128-bit, 340 undecillion addresses, e.g., 2001:db8::1

Types of IP:

Private: Only works inside LAN (192.168.x.x, 10.x.x.x, 172.16.x.x)
Public: Assigned by ISP, globally unique
IP Address Classes
Class	Range	Usage	Subnet Mask	Hosts
A	1.0.0.0 – 126.255.255.255	Large networks	255.0.0.0 (/8)	16,777,214
B	128.0.0.0 – 191.255.255.255	Medium networks	255.255.0.0 (/16)	65,534
C	192.0.0.0 – 223.255.255.255	Small networks	255.255.255.0 (/24)	254
D	224.0.0.0 – 239.255.255.255	Multicast	N/A	N/A
E	240.0.0.0 – 255.255.255.255	Research	N/A	N/A

Private ranges:

Class A → 10.0.0.0
Class B → 172.16.0.0
Class C → 192.168.0.0
Decimal ↔ Binary Conversion

Each octet = 8 bits: 128 64 32 16 8 4 2 1

Method 1 – Subtraction:

192 → 11000000

Method 2 – Divide by 2:

192 ÷ 2 repeatedly → remainders → 11000000

Memorize:

128 = 10000000
192 = 11000000
224 = 11100000
240 = 11110000
255 = 11111111
0   = 00000000
Network and Host
Network part: identifies network
Host part: identifies device

Subnet mask: tells which is network and which is host
Example:

IP:   192.168.1.25
Mask: 255.255.255.0
Network: 192.168.1
Host:    25

Special addresses:

First → network address (not usable)
Last → broadcast address (not usable)
In-between → usable hosts

Example /24 network:

Network   → 192.168.1.0
First host→ 192.168.1.1
Last host → 192.168.1.254
Broadcast → 192.168.1.255
Hosts     → 254

Hosts formula: 2^(host bits) - 2

Special IP Addresses
0.0.0.0 → Unspecified, DHCP request
127.0.0.0 – 127.255.255.255 → Loopback, traffic stays local
169.254.0.0 – 169.254.255.255 → APIPA (DHCP failed)
255.255.255.255 → Limited broadcast
IPv6
Created because IPv4 ran out
128-bit, 340 undecillion addresses
Format: 8 groups of 4 hex digits, separated by colons
Shortening rules:
Remove leading zeros
Replace consecutive zero groups with ::
Special addresses:
::1 → loopback
:: → unspecified
fe80::/10 → link-local
ff00::/8 → multicast

IPv4 vs IPv6:

Feature	IPv4	IPv6
Bits	32	128
Format	Decimal dots	Hex colons
Addresses	~4 billion	340 undecillion
Security	Optional	Built-in (IPSec)
NAT	Required	Not needed
OSI Model (7 Layers)
Layer	Function	Protocol/Example
7 Application	User interaction	HTTP, HTTPS, FTP, SSH, DNS
6 Presentation	Encryption/compression	TLS/SSL, JPEG, ASCII
5 Session	Connection management	Keep login sessions active
4 Transport	Segments data	TCP, UDP
3 Network	Logical addressing, routing	IP, ICMP, OSPF
2 Data Link	Physical addressing (MAC)	Switches
1 Physical	Raw bits over medium	Cables, hubs, WiFi

Memory trick: All People Seem To Need Data Processing → APSTNDP

Real-world example: sending a Telegram message → bits travel L1 → L7 at server → back up to L7 at receiver

MAC Address
Media Access Control, unique hardware ID (48-bit)
Format: 00:1A:2B:3C:4D:5E
First half → manufacturer (OUI)
Second half → device ID
MAC vs IP:
MAC: Layer 2, inside LAN, hardware
IP: Layer 3, across networks, logical

Check MAC:

ip link show

Security concerns:

MAC spoofing
MAC flooding
ARP poisoning
Ports
Identify services on devices
Like: IP = building, Port = apartment number

Ranges:

0–1023 → well-known
1024–49151 → registered
49152–65535 → dynamic

Common ports:

22 → SSH
23 → Telnet
25 → SMTP
53 → DNS
80 → HTTP
443 → HTTPS
21 → FTP
3306 → MySQL
3389 → RDP

Check open ports:

ss -tuln
nmap 192.168.1.1
nmap -p 22 192.168.1.1
Red Team → scan ports
Blue Team → close unnecessary ports
Key Takeaways — Day 4
Networks connect devices to share resources
IP identifies device, port identifies service
MAC works inside LAN, IP works across the internet
OSI model has 7 layers
Subnetting splits networks for security and organization
IPv6 created because IPv4 ran out

Homeworks:
Study OSI model & typical attacks per layer
Research subnet masks & subnetting
Learn 30 common ports, services, and typical attacks
Find tool that outputs IP class, type, validity, and default subnet
Practice decimal ↔ binary conversion for all IP classes
