Subnetting & Subnet Mask — Personal Notes

Name: Azariya chombe
Date: March 2026

What is a Subnet Mask?

A subnet mask is a 32-bit number that splits an IP address into:

Network part → identifies the network
Host part → identifies the device

Every device needs:

IP address
Subnet mask

Without a mask, a device cannot tell which part is the network and which part is the host.

How It Works — AND Operation

To find the network address:

IP:   192.168.1.25
      11000000.10101000.00000001.00011001

Mask: 255.255.255.0
      11111111.11111111.11111111.00000000

AND:  11000000.10101000.00000001.00000000
      = 192.168.1.0  ← Network Address

Rule:

1 AND 1 = 1
1 AND 0 = 0
0 AND 0 = 0
Default Masks by Class
Class	IP Range	Default Mask	CIDR
A	1 – 126	255.0.0.0	/8
B	128 – 191	255.255.0.0	/16
C	192 – 223	255.255.255.0	/24
CIDR Notation

Instead of writing the full mask:

/number → number of 1s in the mask

Example:

255.255.255.0 = 11111111.11111111.11111111.00000000
Number of ones = 24 → /24
What is Subnetting?

Dividing a big network into smaller networks.

Benefits:

→ Organized networks
→ Less broadcast traffic
→ Better security
→ Efficient IP usage
→ Isolate teams/departments
Subnetting Formula
Subnets = 2^s
Hosts per subnet = 2^h - 2

s = bits borrowed from host portion
h = remaining host bits
-2 = remove network & broadcast addresses
Subnetting Example — 192.168.1.0/24

Original: /24 → 8 host bits → 254 hosts → 1 network

Borrow 2 bits → /26:

Subnets = 2^2 = 4
Hosts   = 2^6 - 2 = 62 per subnet
Subnet	Network	First Host	Last Host	Broadcast
1	192.168.1.0	192.168.1.1	192.168.1.62	192.168.1.63
2	192.168.1.64	192.168.1.65	192.168.1.126	192.168.1.127
3	192.168.1.128	192.168.1.129	192.168.1.190	192.168.1.191
4	192.168.1.192	192.168.1.193	192.168.1.254	192.168.1.255
CIDR Cheat Sheet
CIDR	Subnet Mask	Hosts	Subnets from /24
/24	255.255.255.0	254	1
/25	255.255.255.128	126	2
/26	255.255.255.192	62	4
/27	255.255.255.224	30	8
/28	255.255.255.240	14	16
/29	255.255.255.248	6	32
/30	255.255.255.252	2	64
/32	255.255.255.255	1	single host
Special Addresses in a Subnet
First address  → Network (not usable)
Last address   → Broadcast (not usable)
Addresses in between → Usable hosts

Example /24: 192.168.1.0/24
192.168.1.0   → Network
192.168.1.1   → First host
192.168.1.254 → Last host
192.168.1.255 → Broadcast
Real World Example — INSA Network
Network: 10.0.0.0/8
Subnet per team:

Cyber team → 10.1.0.0/24
Dev team   → 10.2.0.0/24
Admin      → 10.3.0.0/24
Servers    → 10.4.0.0/24

Benefits:
→ Teams isolated
→ Cyber team cannot directly reach Dev team
→ Security via segmentation
→ Easier traffic monitoring
Security Use Cases
DMZ subnet     → public servers /28
LAN subnet     → internal users /24
Server subnet  → isolated /26
Guest WiFi     → isolated /25

Result: An attacker on Guest WiFi cannot reach Server subnet.
Step by Step Subnetting

Example: 192.168.10.0/24, need 4 subnets

1️⃣ Bits to borrow:

2^s >= 4 → s=2 bits

2️⃣ New prefix:

/24 + 2 = /26

3️⃣ New mask:

/26 = 255.255.255.192

4️⃣ Hosts per subnet:

2^6 - 2 = 62 hosts

5️⃣ Subnet increment:

256 - 192 = 64
Subnets jump by 64

6️⃣ Subnet list:

192.168.10.0   - 192.168.10.63
192.168.10.64  - 192.168.10.127
192.168.10.128 - 192.168.10.191
192.168.10.192 - 192.168.10.255
Useful Commands
# Show IP and subnet
ip addr

# Calculate subnet info
ipcalc 192.168.1.0/24

# Install ipcalc
sudo apt install ipcalc    # Debian/Ubuntu/Kali
sudo pacman -S ipcalc      # Arch
