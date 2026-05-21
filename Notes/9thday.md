# Day 9 — Theory Exam Notes

**Date:** April 18, 2026

## Topics That Needed More Attention

During the theory exam, a few topics required deeper understanding and faster recall. These are the areas I reviewed afterward along with the concepts that became clearer during practice.

---

## 1. Subnetting and Usable Hosts

Subnetting was one of the more challenging sections during the exam, mainly because small mistakes in calculation can easily happen under time pressure.

### Formula

```text
Usable Hosts = 2^(32 - prefix) - 2
```

The subtraction of 2 accounts for:

* the network address
* the broadcast address

### Common CIDR Values

| CIDR | Total Addresses | Usable Hosts |
| ---- | --------------- | ------------ |
| /8   | 16,777,216      | 16,777,214   |
| /16  | 65,536          | 65,534       |
| /24  | 256             | 254          |
| /26  | 64              | 62           |
| /28  | 16              | 14           |
| /30  | 4               | 2            |

### Quick Calculations

* `/26` → `32 - 26 = 6` → `2^6 = 64` → `64 - 2 = 62`
* `/28` → `32 - 28 = 4` → `2^4 = 16` → `16 - 2 = 14`
* `/30` → `32 - 30 = 2` → `2^2 = 4` → `4 - 2 = 2`

A `/30` subnet only provides two usable hosts, which makes it useful for point-to-point communication links.

---

## 2. Nmap Commands and Scan Techniques

Some Nmap commands were easy to confuse because several flags perform related tasks. Reviewing the purpose of each option helped organize them more clearly.

### Decoy Scans

Decoy scanning hides the real scanning source by generating traffic from additional IP addresses.

```bash
nmap -D RND:10 <TARGET>
nmap -D 192.168.1.1,ME <TARGET>
```

The target system receives traffic from multiple addresses, making the actual scanner harder to identify.

### Firewall Evasion and Service Detection

```bash
nmap -sS -sV -f --mtu 8 -T2 <TARGET>
```

| Flag      | Purpose                 |
| --------- | ----------------------- |
| `-sS`     | SYN stealth scan        |
| `-sV`     | Detect service versions |
| `-f`      | Fragment packets        |
| `--mtu 8` | Specify packet size     |
| `-T2`     | Slower scan timing      |

Fragmentation and smaller MTU values may help reduce detection by some filtering systems.

### Handling ICMP Blocking

```bash
nmap -Pn <TARGET>
nmap -PS80,443 <TARGET>
nmap -PA <TARGET>
```

These options allow scanning even when ICMP requests are blocked or ignored by the target.

### Full Port Scan

```bash
nmap -sS -T4 -p- <TARGET>
```

This performs a SYN scan against all ports using faster timing.

### Aggressive Scan With Output

```bash
nmap -A -Pn -T4 <TARGET> -oN output.txt
```

This combines service detection, OS detection, script scanning, and output logging.

---

## 3. Vim Commands and Shell Execution

One command that was easy to forget during the exam was how to execute system commands directly from Vim.

| Action              | Command     |
| ------------------- | ----------- |
| Insert mode         | `i`         |
| Exit insert mode    | `Esc`       |
| Save and quit       | `:wq`       |
| Quit without saving | `:q!`       |
| Run system command  | `:!command` |

### Examples

```vim
:!ls
:!whoami
:!bash
```

The `:!` prefix allows shell commands to run from inside Vim without closing the editor.

---

## Summary

Main concepts reviewed from the exam:

* Subnetting calculations using `2^(32-prefix) - 2`
* Decoy scanning using the `-D` flag
* Firewall evasion with fragmentation and MTU settings
* Using `-Pn` when ICMP is blocked
* Running shell commands from Vim using `:!command`

