Day 01 — Linux Introduction & Orientation

Date: Saturday, March 7, 2026
 Program Orientation
    Attendance is tracked every Saturday and Sunday
    Progress is monitored via GitHub — notes, assignments, and challenges are pushed regularly.
    Transport is self-arranged.
 What is Linux?🐧

 Linux is an oprating system which include a lot of versions like:
    Examples: Kali Linux, Parrot OS, Ubuntu, Arch Linux
    -In Linux, everything is a file.

📂 Linux File System Hierarchy:

The file system starts from root. Every directory is from here.
Directory 	Description
/ 	Root — the top of the entire filesystem
/bin 	Essential user binaries (e.g., ls, cp, cat)
/sbin 	System binaries — used by admins (e.g., reboot, ifconfig)
/boot 	Bootloader and kernel files (GRUB lives here)
/dev 	Device files — hardware is represented as files here
/etc 	System-wide configuration files
/home 	Personal directories for each user
/lib 	Shared libraries needed by /bin and /sbin
/media 	Auto-mount point for removable drives (USB, CD)
/mnt 	Temporary manual mount point
/opt 	Optional/third-party software packages
/tmp 	Temporary files — cleared on reboot
/usr 	User utilities, applications, and secondary hierarchy
/var 	Variable data — logs, databases, mail spools
/var/log 	System and application logs
/root 	Home directory for the root superuser
- Red vs Blue Team
🔴 Red Team – Attackers / Offensive
⚔️ – Simulate attacks
💣 – Exploit vulnerabilities
🕵️‍♂️ – Think like hackers
🧨 – Test system weaknesses
🎯 – Goal: breach defenses
🔵 Blue Team – Defenders / Defensive
🛡️ – Protect systems
🖥️ – Monitor networks & computers
🧠 – Analyze threats & plan defenses
🚨 – Respond to attacks
🔒 – Patch vulnerabilities
Takeaways from  — Day 1

    Linux is a kernel, not an OS. Distributions are built on top of it.
    The file system hierarchy is the foundation of everything in security — know where files live.
    /tmp is the attacker's first stop; /var/log is the defender's first stop.
    Bandit is the best way to build terminal muscle memory fast.

 Homework given on that day:

    -Install Kali Linux via VirtualBox
    - open Git and GitHub account
    -Push setup progress to GitHub.
    - Start Bandit (levels 0–5)
 
