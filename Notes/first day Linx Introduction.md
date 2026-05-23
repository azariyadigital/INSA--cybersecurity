Day 01. Linux Introduction & Orientation

Date: Saturday, March 7 2026

What is Linux?

Linux is an operating system. It has versions.

Examples: Kali Linux, Parrot OS, Ubuntu, Arch Linux

In Linux everything is a file.

Linux File System Hierarchy:

The file system starts from root.

Every directory comes from here.

Directory Description

/ Root. The top of the filesystem

/bin Essential user binaries. For example ls, cp, cat.

/sbin System binaries. Used by admins. For example reboot, ifconfig.

/boot Bootloader and kernel files. GRUB lives here.

/dev Device files. Hardware is represented as files here.

/etc System- configuration files.

/home Personal directories for each user.

/lib Shared libraries needed by /bin and /sbin.

/media Auto-mount point for drives. For example USB, CD.

/mnt Manual mount point.

/opt Optional/third-party software packages.

/tmp Temporary files. They are cleared on reboot.

/usr User utilities, applications and secondary hierarchy.

/var Variable data. Logs, databases, mail spools.

/var/log System and application logs.

/root Home directory for the root superuser.

Red vs Blue Team

Red Team – Attackers. They simulate attacks. They exploit vulnerabilities. They think like hackers. They test system weaknesses. Their goal is to breach defenses.

Blue Team – Defenders. They protect systems. They monitor networks and computers. They analyze threats and plan defenses. They respond to attacks. They patch vulnerabilities.

Takeaways from. Day 1

Linux is a kernel. It is not an OS. Distributions are built on top of it.

The file system hierarchy is the foundation of everything in security. You should know where files live.

/tmp is the attackers stop. /Var/log is the defenders stop.

Bandit is the way to build terminal muscle memory fast.

Homework given on that day:

- Install Kali Linux via VirtualBox.

- Open Git and GitHub account.

- Push setup progress, to GitHub.

- Start Bandit. Complete levels 0–5.
 
