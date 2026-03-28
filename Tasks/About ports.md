30 Common Ports, Services & Attacks — Personal Notes

Name: Azariya chombe
Program: INSA Cyber Talent Weekend Program
Track: Cybersecurity
Date: March 2026

What is a Port?

A port is a number that identifies a specific service running on a device.

IP address → identifies the device
Port → identifies the service on that device

Simple idea:
IP is like the building, port is like the room inside it.

Port Ranges
0 – 1023     → Well-known ports (system services)
1024 – 49151 → Registered ports (applications)
49152 – 65535 → Dynamic ports (temporary use)
Common Ports (Simplified Table)
Port	Protocol	Service	Use	Risk
20/21	TCP	FTP	File transfer	No encryption, brute force
22	TCP	SSH	Secure remote access	Brute force attacks
23	TCP	Telnet	Remote access	Sends data in plain text
25	TCP	SMTP	Sending email	Spam, spoofing
53	TCP/UDP	DNS	Domain resolution	DNS spoofing
67/68	UDP	DHCP	Assign IP addresses	Rogue DHCP
69	UDP	TFTP	Simple file transfer	No authentication
80	TCP	HTTP	Web traffic	XSS, SQL injection
110	TCP	POP3	Receive email	Credentials exposed
123	UDP	NTP	Time sync	DDoS amplification
135	TCP	RPC	Windows services	Exploits, worms
139	TCP	NetBIOS	File sharing	Enumeration attacks
143	TCP	IMAP	Email sync	Brute force
161	UDP	SNMP	Device management	Weak credentials
389	TCP	LDAP	Directory services	Injection
443	TCP	HTTPS	Secure web	SSL downgrade attacks
445	TCP	SMB	Windows sharing	Ransomware exploits
514	UDP	Syslog	Logging	Log spoofing
993	TCP	IMAPS	Secure email	Weak TLS
995	TCP	POP3S	Secure email	Weak TLS
1433	TCP	MSSQL	Database	SQL injection
1723	TCP	PPTP	VPN	Weak encryption
3306	TCP	MySQL	Database	Exposed DB risk
3389	TCP	RDP	Remote desktop	Brute force
5900	TCP	VNC	Remote desktop	No encryption
8080	TCP	HTTP Alt	Web/dev	Same as HTTP risks
High-Risk Ports

These should not be exposed to the internet:

23 (Telnet) → no encryption
445 (SMB) → used in major ransomware attacks
3389 (RDP) → common brute force target
3306 (MySQL) → databases should stay private
5900 (VNC) → often poorly secured
Ports by Use
Remote Access
22   SSH   → secure
23   Telnet → insecure
3389 RDP   → Windows remote access
5900 VNC   → graphical remote access
Web
80   HTTP
443  HTTPS
8080 Alternative web port
Email
25   SMTP
110  POP3
143  IMAP
993  IMAPS
995  POP3S
File Transfer
20/21 FTP
69   TFTP
445  SMB
Databases
1433 MSSQL
3306 MySQL
Network Services
53   DNS
67   DHCP
123  NTP
161  SNMP
Red Team View (Attacker Focus)

Common targets:

21 → FTP misconfigurations
22 → weak SSH passwords
23 → easy credential capture
80/8080 → web vulnerabilities
445 → SMB exploits
3306 → exposed databases
3389 → RDP brute force
5900 → weak VNC access
Blue Team View (Defense)
Close unused ports
Avoid exposing sensitive ports to the internet
Monitor login attempts on 22 and 3389
Use firewalls to restrict access
Watch unusual ports like 4444 or 1337
Port Scanning Basics
# Scan common ports
nmap 192.168.1.1

# Scan all ports
nmap -p- 192.168.1.1

# Scan selected ports
nmap -p 22,80,443 192.168.1.1

# Detect services
nmap -sV 192.168.1.1

# Show open ports locally
ss -tuln
