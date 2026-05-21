# Day 13 — Attack Surface & Web Security Foundations

### Recon, Enumeration, Authentication Flaws, IDOR, Hashing, and Brute Force

**Date:** May 02, 2026

---

## Encryption vs Hashing

### Encryption

Encryption is a reversible process used to protect data confidentiality. The original data can be recovered using the correct key.

| Type       | Description                                 | Examples           |
| ---------- | ------------------------------------------- | ------------------ |
| Symmetric  | Same key used for encryption and decryption | AES, DES, Blowfish |
| Asymmetric | Public key encrypts, private key decrypts   | RSA, ECC           |

Encryption is commonly used for:

* HTTPS/TLS communication
* file encryption
* protecting stored sensitive information

The security of encrypted data depends entirely on protecting the encryption keys.

---

### Hashing

Hashing is a one-way transformation that converts input data into a fixed-length output called a digest.

Example:

```text id="q3mxvn"
password → MD5 → 5f4dcc3b5aa765d61d8327deb882cf99
```

Unlike encryption, hashes are not meant to be reversed.

| Algorithm | Status                                     |
| --------- | ------------------------------------------ |
| MD5       | Broken and insecure                        |
| SHA-1     | Deprecated                                 |
| SHA-256   | Secure for general use                     |
| bcrypt    | Designed specifically for password storage |

Password hashing algorithms such as bcrypt are intentionally slow to make brute-force attacks more difficult.

### Salting

A salt is a random value added before hashing:

```text id="u7yqsm"
hash(password + salt)
```

Salting prevents identical passwords from producing identical hashes and helps defend against rainbow table attacks.

---

## Burp Suite Extensions

Burp Suite supports extensions through the BApp Store.

### Commonly Used Extensions

| Extension      | Purpose                        |
| -------------- | ------------------------------ |
| AuthMatrix     | Authorization testing          |
| IDOR Scanner   | Detect IDOR issues             |
| Param Miner    | Discover hidden parameters     |
| JWT Editor     | Edit and analyze JWT tokens    |
| Turbo Intruder | Faster fuzzing and brute force |
| Active Scan++  | Additional scan checks         |
| Logger++       | Advanced logging               |

Extensions can improve workflow efficiency during web application testing.

Installation path:

```text id="e8zwab"
Burp → Extensions → BApp Store
```

---

## Authorization Concepts

Authorization determines what an authenticated user is allowed to access.

### Vertical Privilege Escalation

A lower-privileged user accesses higher-level functionality.

Example:

```text id="c4kpsm"
normal user → /admin/panel
```

---

### Horizontal Privilege Escalation

A user accesses another user's resources while remaining at the same privilege level.

Example:

```text id="y9dftr"
/profile?id=102
```

Changing identifiers without proper access validation is a common source of access control vulnerabilities.

---

## IDOR — Insecure Direct Object Reference

IDOR occurs when applications expose internal object identifiers without verifying ownership or permissions.

### Example

```text id="n5rjkt"
GET /api/invoice?id=1048
```

If changing the ID reveals another user's invoice, the application is vulnerable.

---

### Common IDOR Locations

| Location           | Example               |
| ------------------ | --------------------- |
| URL parameters     | `?id=`                |
| POST body          | JSON account IDs      |
| Cookies            | `user_id=`            |
| Headers            | `X-User-ID`           |
| Hidden form fields | hidden UID values     |
| File paths         | predictable filenames |

Applications should always validate access server-side rather than trusting user-supplied identifiers.

---

## UID and GID

### UID

A UID (User ID) uniquely identifies a Linux user account.

### GID

A GID (Group ID) identifies a Linux group.

Useful commands:

```bash id="m8cvrq"
id
```

```bash id="b2ghxp"
cat /etc/passwd
```

### Security Consideration

Sequential UIDs are predictable and should not be exposed directly in web applications as authorization identifiers.

Safer alternatives include:

* UUIDs
* secure session tokens
* properly signed authentication tokens

---

## Brute Force Tools

### Crunch

Generates custom wordlists.

Example:

```bash id="h1wxpd"
crunch 6 8 abcdefghijklmnopqrstuvwxyz0123456789
```

---

### John the Ripper

Used for password hash cracking.

Example:

```bash id="t7vkza"
john --wordlist=rockyou.txt hash.txt
```

---

### Hashcat

GPU-accelerated password cracking tool.

Example:

```bash id="r5mqye"
hashcat -m 0 hash.txt rockyou.txt
```

Hashcat supports multiple attack modes including dictionary, brute force, and hybrid attacks.

---

### Burp Suite Intruder

Used for automated HTTP request fuzzing and credential testing.

Basic workflow:

1. Capture request
2. Send to Intruder
3. Mark payload positions
4. Load payloads
5. Start attack
6. Analyze response differences

Response length and status code differences are often key indicators.

---

## Web Reconnaissance and Enumeration

### Recon Workflow

```text id="f4ybnu"
Passive Recon → Active Recon → Enumeration → Attack Surface Mapping
```

---

### Passive Recon

No direct interaction with the target.

Examples:

* WHOIS lookups
* certificate transparency logs
* search engine indexing
* subdomain enumeration through public sources

---

### Active Recon

Direct interaction with the target system.

Examples:

* Nmap scanning
* directory brute forcing
* parameter fuzzing

---

## Attack Surface Areas

| Area         | Focus                  |
| ------------ | ---------------------- |
| Subdomains   | dev, admin, staging    |
| Directories  | hidden paths           |
| APIs         | versioned endpoints    |
| Backup files | `.bak`, `.zip`, `.old` |
| Admin panels | `/admin`, `/dashboard` |
| robots.txt   | hidden path discovery  |

---

## robots.txt

Example:

```text id="x8lbwd"
Disallow: /admin/
Disallow: /backup/
```

`robots.txt` is not a security feature. It only instructs search engines and may unintentionally expose sensitive paths.

---

## Enumeration Tools

### Gobuster

Directory and file enumeration tool.

Example:

```bash id="p9zvko"
gobuster dir -u http://target.local -w common.txt
```

Useful status codes:

* 200 → exists
* 301/302 → redirect
* 403 → forbidden but present

---

### Sublist3r

Passive subdomain enumeration tool.

Example:

```bash id="j2wnfr"
sublist3r -d example.com
```

---

### ffuf

Flexible fuzzing tool.

Example:

```bash id="d6tpml"
ffuf -u http://target.local/FUZZ -w common.txt
```

Supports:

* directory fuzzing
* parameter discovery
* extension fuzzing
* virtual host fuzzing

---

## Ethical Hacking Workflow

```text id="g7rkav"
1. Scope Definition
2. Passive Recon
3. Active Recon
4. Vulnerability Identification
5. Exploitation
6. Post-Exploitation
7. Reporting
```

A structured methodology helps ensure testing remains organized, repeatable, and within authorization boundaries.

---

## CTF Exercise — Backup Directory Exposure

### Main Findings

* Directory listing enabled on `/backup/`
* Plaintext credentials exposed
* SSH access available
* Privilege escalation through sudo misconfiguration

### Important Lessons

* Backup directories should never be publicly accessible
* Credentials should never be stored in plaintext
* Misconfigured sudo permissions can lead to privilege escalation
* Tools like `awk`, `vim`, and `find` can become dangerous when executable as root

---

## CTF Exercise — API Enumeration and Hash Cracking

### Key Concepts Practiced

* API endpoint enumeration
* Base64 decoding
* MD5 hash cracking
* Session token usage
* Correlating information from multiple endpoints

### Main Lessons

* Sensitive information should never be fragmented across public endpoints
* MD5 is not suitable for password storage
* Enumeration and correlation are critical during reconnaissance
* Session management should be handled securely server-side

---

## Final Notes

### Important Security Concepts

| Topic                 | Key Point                        |
| --------------------- | -------------------------------- |
| Encryption            | Reversible with keys             |
| Hashing               | One-way transformation           |
| Salting               | Prevents rainbow table attacks   |
| IDOR                  | Missing authorization validation |
| Vertical escalation   | User → admin                     |
| Horizontal escalation | User → another user's data       |

---

## Common Recon and Brute Force Tools

| Tool          | Purpose                     |
| ------------- | --------------------------- |
| Gobuster      | Directory enumeration       |
| ffuf          | Flexible fuzzing            |
| Sublist3r     | Passive subdomain discovery |
| John          | Password cracking           |
| Hashcat       | GPU-based cracking          |
| Burp Intruder | HTTP brute force testing    |

---

## Key Takeaways

* Reconnaissance is the foundation of web security testing
* Access control flaws are among the most critical vulnerabilities
* Enumeration often reveals hidden functionality or sensitive information
* Weak hashing algorithms remain a major security problem
* Proper authorization checks must always be enforced server-side
* Structured workflows improve both efficiency and documentation quality

