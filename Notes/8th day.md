Day 08 — Network Recall & Nmap Scripting Secrets

Date: April 6, 2026

🌐 Session Focus

This session was mainly a reinforcement run — going back over networking concepts and Nmap behavior.
But one fresh layer was added: understanding how Nmap scripts actually work behind the scenes.

🧠 Inside Nmap’s Brain — Scripting Engine (NSE)
🔹 Script Language

Nmap scripts are written using Lua, and every script ends with .nse.

Nmap includes its own built-in Lua interpreter, which allows scripts to run directly inside the scanner.

# View available scripts
ls /usr/share/nmap/scripts/

# Run a specific script
sudo nmap --script=http-title target

# Run default scripts
sudo nmap -sC target

# Run vulnerability scripts
sudo nmap --script=vuln target
⚙️ Why Lua Was Chosen

Nmap didn’t pick Lua randomly. It fits like a key in a lock:

🧩 Lightweight integration → doesn’t bloat Nmap
⚡ Efficient multitasking → uses coroutines instead of heavy threads
📘 Simple syntax → easy to read and write scripts
🧱 Structure of an NSE Script

Every script follows a predictable pattern:

-- Description
description = [[
  Explains what the script checks
]]

-- Rule (when to run)
portrule = function(host, port)
  return port.number == 80
end

-- Action (what to do)
action = function(host, port)
  return "Result output"
end
🔌 Built-in NSE Libraries

These libraries connect scripts to real network interactions:

http → send web requests
smb → interact with Windows shares
ssl → inspect encrypted connections
dns → resolve domains
ftp → communicate with FTP servers
ssh → interact with SSH services
mysql → database probing
brute → credential attacks
🗂️ Script Categories (Refresher)
default → safe and useful scripts
safe → no risk to target
vuln → vulnerability detection
auth → authentication testing
brute → password guessing
discovery → information gathering
intrusive → may affect target
malware → detect backdoors
exploit → actively abuse vulnerabilities
🧪 Practical Script Usage
# Check EternalBlue vulnerability
sudo nmap --script=smb-vuln-ms17-010 target

# Discover web directories
sudo nmap --script=http-enum target

# Inspect SSL certificate
sudo nmap --script=ssl-cert -p 443 target

# Attempt SSH brute force
sudo nmap --script=ssh-brute target

# Check FTP anonymous login
sudo nmap --script=ftp-anon -p 21 target

# Run multiple scripts
sudo nmap --script=http-title,http-headers target
✍️ Example: Custom Script
-- banner_grabber.nse

description = "Extracts service banner"

portrule = function(host, port)
  return port.state == "open"
end

action = function(host, port)
  local socket = nmap.new_socket()
  socket:connect(host.ip, port.number)
  local status, response = socket:receive()
  socket:close()

  if status then
    return "Banner: " .. response
  end
end
🔁 Core Revision — Nmap Basics
🚪 Port States
open → service responds (SYN-ACK)
closed → no service (RST-ACK)
filtered → blocked by firewall
open|filtered → unclear response
🕵️ Stealth Techniques Recap
-sS              # SYN scan (half-open)
-T0 / -T1        # slow timing
-f               # fragment packets
-D RND:10        # decoy IPs
--source-port 53 # fake DNS source
-sI zombie       # idle scan (no direct contact)
🧬 Mapping Nmap to OSI Layers
Layer 7 (Application) → NSE scripts (HTTP, SMB, FTP)
Layer 4 (Transport) → TCP/UDP scanning
Layer 3 (Network) → IP manipulation, TTL tricks
Layer 2 (Data Link) → packet crafting tools
🎯 Key Ideas to Lock In
NSE scripts are Lua-based and lightweight
Coroutines allow efficient parallel execution
Each script has: description + rule + action
Libraries connect scripts to real protocols
-sC = default scripts set
📌 Ongoing Tasks
Continue building NetFP tool
Study how C2 traffic appears in packet captures
Understand detection patterns used by defenders
