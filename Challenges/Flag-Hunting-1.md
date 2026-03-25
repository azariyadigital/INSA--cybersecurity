Challenge: Payload Flag Hunting #1

Date: March 8, 2026
Program: INSA Cyber Talent Program
Field: Cybersecurity
Flags Found: 2/3
About the Challenge:

A Mentor provided a payload (executable file). The challenge was to:

    Run the payload
    Observe its behavior
    Find 3 hidden flags that the payload dropped across the Linux system

No hints were given on where the flags were hidden.
-Methodology

Before running any unknown file, I made a mental checklist:

# Check what type of file it is
file payload

# Look for readable text before running
strings payload

# Check permissions — can we even run it?
ls -la payload

# If needed, give execute permission
chmod +x payload

Then run it:

./payload

After execution,  what i have done was to search the most likely attacker-used directories first.
- Flag 1 — /tmp Directory

i found it.
Tool Used: ls -la, cat
Environment: Termux (phone) — no laptop available
Reasoning

/tmp is the first place an attacker drops files because:

    It is world-writable — any user can write here
    Files in /tmp often blend in and get ignored
    The Sticky Bit (t) prevents other users from deleting each other's files

Steps

# After running the payload, check /tmp immediately
ls -la /tmp

# Output included a suspicious file:
# -rw-r--r-- 1 root root 42 Mar 08 admin....txt

cat /tmp/admin....txt

Result

FLAG: INSA{[redacted]}

After executing any unknown binary, check /tmp first. Time-sort with ls -lt /tmp to see what was most recently created.
- Flag 2 — /var/log Directory

Status: i found it
Tool Used: ls -lt /var/log, grep
Reasoning
Malware often creates fake log files in /var/log to blend in with legitimate system logs. It's a more advanced hiding spot than /tmp because defenders spend time here and might overlook a new file that "looks like a log."
Steps

# Sort by time to find most recently modified file
ls -lt /var/log | head -20

# Or search directly for flag content
grep -r "flag" /var/log
grep -r "INSA{" /var/log

Result

FLAG: INSA{[redacted]}

Lesson Learned

Sort logs by modification time (ls -lt) during incident response.
 Flag 3: i din't found it.
