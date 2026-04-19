Day 07 — Packet Hunting, Hidden Control & Nmap Mastery

Date: April 4, 2026

🌅 Morning Lab — From Visual Sniffing to Terminal Power
🧪 Why GUI Packet Tools Fall Short

Previously, packet analysis was done using a graphical tool. It works… but it drags its feet when things get serious.

Main limitations:

🐢 Slow startup & filtering — delays even for simple actions
📦 Heavy files struggle — large captures become painful
🔌 No automation — can’t chain with other tools
🖥️ Needs GUI — useless on servers without interface
🤖 Manual-only workflow — no scripting flexibility
⚡ Enter Tshark — Command-Line Packet Beast

Think of it as Wireshark stripped down to pure speed and control.

Same analysis engine
No graphical interface
Built for automation and remote work
🛠️ Core Usage
# Capture traffic
tshark -i eth0

# Open saved capture
tshark -r file.pcap

# Apply filters
tshark -r file.pcap -Y "http"
tshark -r file.pcap -Y "dns"
tshark -r file.pcap -Y "tcp.port == 80"

# Extract specific data fields
tshark -r file.pcap -T fields -e ip.src -e ip.dst -e tcp.dstport

# Count matching packets
tshark -r file.pcap -Y "http" | wc -l
🔗 Combining with Linux Tools (Real Power)

This is where things go from “tool” → “weapon”

# Most active IPs
tshark -r file.pcap -T fields -e ip.src | sort | uniq -c | sort -rn | head

# DNS queries only
tshark -r file.pcap -Y "dns.flags.response == 0" -T fields -e dns.qry.name | sort -u

# HTTP GET requests
tshark -r file.pcap -Y "http.request.method == GET" -T fields -e http.host -e http.request.uri

# FTP credentials (cleartext)
tshark -r file.pcap -Y "ftp" -T fields -e ftp.request.command -e ftp.request.arg
⚔️ Tshark vs GUI Tools
GUI → easier visualization, protocol breakdown
CLI → faster, scriptable, scalable, remote-friendly
🧠 Command & Control (C2) — The Hidden Puppet System

C2 is how attackers control compromised machines without being physically present.

🕸️ Structure
Attacker → C2 Server → Infected Machines (Agents)
🔁 How It Operates
Target machine gets infected
Malware connects outward to C2
Attacker sends commands
C2 distributes instructions
Infected systems respond back
🎭 Why Outbound Traffic?

Firewalls are strict with incoming traffic but relaxed with outgoing.

So attackers flip the logic:
➡️ victim initiates connection
➡️ looks like normal browsing

This repeated check-in behavior is called beaconing.

🧬 Common Communication Channels
HTTPS (port 443) → blends with web traffic
DNS → disguised as domain queries
ICMP → hidden inside ping data
🧰 Known C2 Frameworks
Metasploit
Cobalt Strike
Empire
Sliver
Havoc
🌆 Afternoon Lab — Deep Dive into Nmap
🚪 Port States Explained

Each scanned port responds in a specific way:

🟢 Open

Service is active and accepting connections
→ Response: SYN-ACK

🔴 Closed

No service running
→ Response: RST-ACK

🟡 Filtered

Blocked by firewall or network control
→ No response or ICMP error

⚪ Open|Filtered

No clear response
→ common in UDP scans

🔵 Unfiltered

Accessible but unclear status
→ appears in ACK scans

📡 Host Discovery — How Targets Are Found

Before scanning ports, Nmap checks if the host exists.

It sends:

ICMP ping
TCP SYN (443)
TCP ACK (80)
ICMP timestamp

Any reply = host is alive.

🚫 If ICMP Is Blocked
sudo nmap -Pn target

Forces scan without checking if host is alive.

🕵️ Firewall Evasion Techniques
🧩 Fragmentation

Break packets into tiny pieces to confuse inspection systems:

sudo nmap -f target
sudo nmap --mtu 16 target
🎭 Decoy Scanning

Hide among fake IPs:

sudo nmap -D RND:5 target
🎯 Fake Source Port

Pretend traffic is from trusted service:

sudo nmap --source-port 53 target
🧠 Passive vs Active Recon
🕶️ Passive (Invisible)

No direct interaction with target:

WHOIS
DNS queries
search engines
public data sources
⚡ Active (Direct Contact)
# Find live hosts
nmap -sn network

# Scan ports
nmap -sS target

# Detailed scan
nmap -sV -O target
🧟 Idle Scan (Zombie Technique)

Use another machine to scan for you.

Your system never directly touches the target.

➡️ target logs show zombie IP
➡️ you stay hidden

📦 Advanced Protocol Scanning
SCTP scans → telecom environments
IP protocol scan → detect supported protocols
ACK scan → map firewall rules
📊 Output Handling
Save Results
nmap -oN scan.txt
nmap -oX scan.xml
nmap -oA full_scan
Work with Data
XML → automation & parsing
grep/awk → quick filtering
convert → HTML or JSON reports
🌐 Passive Intelligence Tools
🔎 Shodan

Search engine for exposed devices:

find open ports
detect vulnerable services
discover infrastructure
🛰️ smap

Uses Shodan data to simulate scans
→ no packets sent
→ completely stealth

🎯 Core Lessons from Today
Terminal tools outperform GUI in speed and automation
C2 relies on outbound communication to stay hidden
Port states depend on exact network responses
ICMP is key to discovering live systems
Fragmentation and decoys help bypass defenses
Passive recon leaves zero trace
Zombie scans remove your footprint completely
XML output is best for automation and reporting
🧩 Tasks
Build a simple port scanner in Python
Study how C2 traffic looks in packet captures
Analyze how defenders detect beaconing
Upload your work to GitHub
