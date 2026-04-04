-> Basic Scanning (Surface Level)
1. Ping Scan
nmap -sn target
Checks which hosts are online
No port scanning
2. TCP Connect Scan
nmap -sT target
Full TCP connection
Easily detectable
3. Port Scan
nmap -p 80,443 target
Scans specific ports
-> Intermediate Scanning (Information Gathering)
4. SYN Scan (Stealth)
nmap -sS target
Half-open scan
Faster and stealthier
5. UDP Scan
nmap -sU target
Scans UDP services
6. Service Version Detection
nmap -sV target
Detects service versions
7. OS Detection
nmap -O target
Identifies operating system
-> Advanced Scanning (Deep Recon)
8. Aggressive Scan
nmap -A target
Enables OS detection, version detection, scripts, traceroute
9. Script Scanning (NSE)
nmap --script=default target
Runs Nmap scripts
10. Default Scripts
nmap -sC target
Runs safe default scripts
-> Stealth & Evasion Techniques
11. Packet Fragmentation
nmap -f target
Breaks packets into smaller pieces
12. Decoy Scan
nmap -D RND:10 target
Uses random decoys to hide your IP
13. Spoof IP
nmap -S <fake_ip> target
Spoofs source IP (advanced use)
14. Timing Control
nmap -T4 target
Adjust scan speed (T0–T5)
->  Expert Techniques
15. Firewall Evasion
nmap --source-port 53 target
Bypasses firewall rules
16. Traceroute
nmap --traceroute target
Maps network path
17. Idle Scan (Zombie Scan)
nmap -sI zombie_host target
Ultra stealth scan using another host
-> Example Combined Scan
nmap -sS -sV -O -T4 target.com
SYN scan (stealth)
Version detection
OS detection
Faster timing
Author: Azariya

