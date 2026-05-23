# Day 10 — Practical Assessment Notes

## Penetration Testing Challenge

**Date:** April 19, 2026

This lab focused on performing a complete penetration testing workflow starting with only a target IP address. The objective was to enumerate services, identify weaknesses, analyze captured data, and recover hidden flags using standard security tools and investigation techniques.

**Target Host:** `3.227.254.50`

---

## Initial Approach

The workflow followed a standard penetration testing process:

```text id="ngmx2r"
Reconnaissance → Enumeration → Vulnerability Analysis → Exploitation → Evidence Collection
```

The main goal during the early phase was to identify exposed services and collect as much information as possible before interacting deeply with the target.

---

## 1. Reconnaissance and Service Enumeration

### Nmap Service Detection

```bash id="f0g9ht"
nmap -sV -Pn 3.227.254.50
```

The scan identified several accessible services:

| Port | Service | Version       |
| ---- | ------- | ------------- |
| 21   | FTP     | vsftpd 3.0.5  |
| 22   | SSH     | OpenSSH 8.7   |
| 80   | HTTP    | Apache 2.4.41 |
| 1022 | SSH     | OpenSSH 8.2p1 |

The host was running multiple remote-access services alongside a web server, which suggested that both network-level and web-based enumeration would be useful.

### Vulnerability Scanning

```bash id="imdbhi"
nmap --script vuln -Pn 3.227.254.50
```

The vulnerability scan revealed:

* accessible `robots.txt`
* outdated software versions
* several publicly known CVEs affecting Apache, OpenSSH, and vsftpd

This reinforced the importance of checking service versions carefully during enumeration.

---

## 2. Web Enumeration

### robots.txt Discovery

```bash id="p3hjve"
curl http://3.227.254.50/robots.txt
```

A hidden path was disclosed:

```text id="zn3slo"
Disallow: /messages/user
```

Visiting the directory exposed an internal message discussing weak password practices.

### Information Disclosure

The message referenced a user repeatedly using predictable password patterns involving the word “password” combined with a year value.

This eventually led to valid FTP credentials.

---

## 3. FTP Enumeration

### FTP Login

```bash id="d2r8ca"
ftp 3.227.254.50
```

After authentication, several files and directories became accessible.

Important findings included:

* hidden files
* image files
* packet captures
* log files

This stage highlighted how improperly secured FTP storage can expose sensitive internal data.

---

## 4. File and Metadata Analysis

Several investigation methods were used on downloaded files.

### Hidden Base64 Content

A hidden text file contained Base64 encoded data which revealed one of the flags after decoding.

```bash id="w91pdu"
base64 -d
```

### Metadata Inspection

```bash id="j8t0yp"
exiftool Challenge.jpeg
```

Metadata analysis revealed hidden information embedded inside image comments.

### Steganography

```bash id="xfzqfr"
steghide extract -sf Challenge.jpeg
```

Additional hidden content was extracted from the image using a password obtained earlier during enumeration.

### Embedded Archive Discovery

```bash id="m6bhjp"
binwalk -e Challenge.jpeg
```

An archived file was hidden inside the image itself and extracted successfully.

This demonstrated how multiple layers of hidden data can exist inside a single file.

---

## 5. Packet Capture Analysis

Packet captures provided several important findings during traffic inspection.

### ICMP Traffic

Using Wireshark, ICMP packets were reviewed manually. One packet contained hidden data embedded inside the payload section.

### DNS Analysis

DNS requests revealed fragmented strings spread across multiple subdomains.

This demonstrated how DNS tunneling techniques can be used to transfer hidden information.

### HTTP Traffic Inspection

Reviewing TCP streams exposed credentials transmitted in plaintext over HTTP.

This reinforced why encrypted protocols such as HTTPS are necessary in real environments.

---

## 6. Log Analysis

SSH authentication logs were inspected to identify suspicious activity.

```bash id="ou3xmk"
grep "Accepted" SSH.log
```

The logs showed repeated failed login attempts followed by a successful authentication event.

Encoded data was hidden directly inside the log entry and later decoded.

---

## Key Concepts Reviewed

### Enumeration Is Critical

Small findings during early reconnaissance often lead to larger discoveries later in the assessment.

### Weak Passwords Remain a Major Risk

Simple password patterns are highly predictable and can easily lead to unauthorized access.

### File Analysis Requires Multiple Techniques

Images and archived files may contain:

* metadata
* hidden archives
* encoded content
* steganographic payloads

### Network Traffic Can Reveal Sensitive Data

Protocols without encryption expose credentials and internal communication directly to attackers monitoring traffic.

### Logs Provide Valuable Evidence

Authentication logs can help identify:

* brute-force activity
* successful compromise attempts
* suspicious user behavior

---

## Tools Used

| Tool       | Purpose                             |
| ---------- | ----------------------------------- |
| Nmap       | Port scanning and service detection |
| ffuf       | Directory fuzzing                   |
| Wireshark  | Packet analysis                     |
| exiftool   | Metadata inspection                 |
| steghide   | Steganography extraction            |
| binwalk    | Embedded file extraction            |
| grep       | Log analysis                        |
| curl       | Web interaction                     |
| FTP client | Remote file access                  |

---

## Final Reflection

This exercise reinforced the importance of structured enumeration and careful analysis. Most of the discoveries were not the result of advanced exploitation, but rather attention to detail during investigation.

The challenge also demonstrated how information can be hidden across:

* network traffic
* metadata
* encoded files
* logs
* archived content

