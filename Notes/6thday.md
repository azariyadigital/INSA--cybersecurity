Day 06 — OSI Layers, Data Wrapping & Windows Investigation

Date: March 29, 2026

🌅 Morning Session — Understanding the OSI Model
🔹 Data Flow: Encapsulation vs Decapsulation

When data travels across a network, it doesn’t just jump from point A to B. It gets dressed up layer by layer like a traveler adding gear before a journey.

📦 Encapsulation (Sending Data — Top → Bottom)

Each OSI layer attaches its own information (called a header) before passing the data down.

Application   → [ Data ]
Presentation  → [ Data ]
Session       → [ Data ]
Transport     → [ TCP Header | Data ]        → Segment
Network       → [ IP Header | TCP | Data ]   → Packet
Data Link     → [ MAC | IP | TCP | Data | FCS ] → Frame
Physical      → 010101010101010...           → Bits

💡 This step-by-step wrapping process is called encapsulation.

📤 Decapsulation (Receiving Data — Bottom → Top)

On the receiving side, each layer removes its own header and passes the rest upward.

Physical      → receives bits
Data Link     → removes MAC info
Network       → removes IP header
Transport     → removes TCP header
Session       → manages connection
Presentation  → decodes/decrypts
Application   → delivers data to user

💡 This reverse process is decapsulation.

🔐 Why This Matters in Cybersecurity

Every layer is like a checkpoint, and attackers love checkpoints.

Inspection → Firewalls analyze headers (IP, TCP)
Spoofing → Fake IP addresses can be inserted
Analysis → Tools like Wireshark reveal packet details
Attacks → Each layer has unique vulnerabilities
🧠 What’s Inside the Headers?
🌐 IP Header (Layer 3)

Contains routing information:

Source IP → origin of packet
Destination IP → target location
TTL (Time To Live) → limits how far packet travels
Protocol → indicates TCP, UDP, ICMP, etc.
🚦 TCP Header (Layer 4)

Controls communication between devices:

Source Port → sender application
Destination Port → receiving service
Sequence Number → keeps packet order correct
Flags → control signals like:
SYN (start)
ACK (acknowledge)
FIN (finish)
RST (reset)
📄 Payload (Actual Data)

This is the real content being sent:

Web pages
Messages
Files

Everything inside the headers = payload.

⚔️ OSI Layer Attacks Overview
Layer	Name	Attack Type	What Happens
7	Application	SQL Injection	Malicious database queries
7	Application	XSS	Inject scripts into websites
7	Application	Phishing	Fake sites steal login info
6	Presentation	SSL Stripping	Downgrade HTTPS to HTTP
5	Session	Session Hijacking	Steal user session
4	Transport	SYN Flood	Overload server connections
4	Transport	Port Scanning	Discover open ports
3	Network	IP Spoofing	Fake source IP
3	Network	ICMP Flood	Ping-based DoS attack
2	Data Link	ARP Poisoning	Redirect traffic
2	Data Link	MAC Spoofing	Fake MAC address
1	Physical	Cable Tapping	Intercept signals
1	Physical	Jamming	Disrupt wireless signals
🌍 DNS Resolution (How Domains Become IPs)

When you type a website, a chain reaction begins:

Browser Cache
Checks if it already knows the IP.
OS Cache
Looks in local records like /etc/hosts.
Router
Checks its stored DNS results.
ISP DNS Server
If not cached, query goes to ISP.
Root DNS Server
Directs to the correct domain extension server (.com, .org).
TLD Server
Points to the domain’s official DNS server.
Authoritative DNS Server
Returns the exact IP address.
Response Returns
Data travels back and gets cached along the way.
⚡ DNS Caching
Speeds up browsing
Reduces repeated queries

TTL (Time To Live):

Defines how long data stays cached
Range: ~300 to 86400 seconds
⚠️ DNS-Based Attacks
Cache Poisoning → fake IP stored in cache
DNS Spoofing → attacker sends fake response
DNS Exfiltration → data hidden inside DNS queries
🌆 Afternoon Session — Windows Network Analysis
🔍 Netstat Command

Used to view active connections and ports.

Commands:

netstat -an → show all connections
netstat -b → show program using connection
netstat -ano → include process ID
📊 Understanding States
LISTENING → waiting for connection
ESTABLISHED → active communication
TIME_WAIT → closing connection
CLOSE_WAIT → connection closed remotely
🛡️ Why Netstat is Important
Detect unknown connections
Identify suspicious ports
Track malware communication
Map programs to network activity
📜 Windows Event Logs

Stored in: Event Viewer → Windows Logs

Categories:
System → OS and hardware events
Security → login and access logs
Application → app errors
Setup → installation logs
Forwarded Events → logs from other systems
🚨 Important Security Event IDs
4624 → successful login
4625 → failed login
4688 → process created
4720 → new user account
4732 → added to admin group
1102 → audit logs cleared ⚠️
🔥 Critical Insight

Even if logs are deleted:

➡️ Event ID 1102 is still recorded
➡️ Meaning: the system logs the act of hiding logs

A digital paradox: erasing evidence leaves evidence.

🔐 SSH on Windows

Windows now supports SSH (like Linux).

ssh username@ip_address
Uses port 22
Same commands as Linux
Used for remote access

⚠️ Also used by attackers for lateral movement inside networks.

🧰 Useful Windows Networking Commands
ipconfig → show IP
ipconfig /all → detailed info
ipconfig /flushdns → clear DNS cache
ping → test connection
tracert → track route
nslookup → DNS query
arp -a → view MAC-IP mapping
🆚 Linux vs Windows Commands
Task	Linux	Windows
Show IP	ip addr	ipconfig
Connections	ss -tuln	netstat -an
DNS lookup	dig / nslookup	nslookup
Ping	ping	ping
Trace route	traceroute	tracert
Processes	ps aux	Task Manager
Remote login	ssh	ssh
🎯 Key Points to Remember
Data gets wrapped going down, unwrapped going up
Headers are valuable targets for attackers
DNS caching improves speed but introduces risk
Event ID 1102 reveals log tampering
Netstat is essential for quick investigation
SSH works the same across Windows and Linux
✅ Tasks
Finish the Windows Event Log challenge
Review the provided reading
Practice using nmap
