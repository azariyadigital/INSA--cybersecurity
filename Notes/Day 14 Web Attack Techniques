# Day 14 — Web Attack Techniques & VulnBank Practice

**Date:** Sunday May 03, 2026

Focused on web attack techniques, OPSEC basics, access control issues, injection flaws, and file upload vulnerabilities through practical exercises in VulnBank.

---

## OPSEC (Operational Security)

OPSEC is about reducing the amount of information a target can collect about you during reconnaissance or testing.

Things that commonly leak information:

| Leak             | What it exposes               |
| ---------------- | ----------------------------- |
| Real IP address  | ISP, approximate location     |
| User-Agent       | Browser, OS, automation tools |
| DNS requests     | Domains being investigated    |
| Timing patterns  | Automation or active scanning |
| TLS fingerprints | Specific tooling/frameworks   |

### Good practices

* Use a VPN or isolated VM during testing
* Avoid scanning from a personal machine or network
* Rotate User-Agent strings during recon
* Reduce scan speed to avoid obvious signatures
* Separate personal accounts from testing accounts

---

## Proxies

A proxy acts as an intermediary between the client and the target.

```text
Client → Proxy → Target
```

### Burp Suite Proxy

Burp intercepts HTTP/HTTPS traffic and allows:

* Viewing requests/responses
* Modifying requests before forwarding
* Replaying traffic
* Testing parameters manually

Default listener:

```text
127.0.0.1:8080
```

### HTTPS interception

To inspect HTTPS traffic:

1. Configure browser proxy to Burp
2. Install Burp CA certificate
3. Burp decrypts and re-encrypts traffic for inspection

---

## proxychains & Tor

### proxychains

Routes terminal-based tools through a proxy.

Example:

```bash
proxychains curl http://example.com
proxychains nmap -sT target.com
```

Configuration:

```bash
/etc/proxychains.conf
```

Example proxy:

```text
socks5 127.0.0.1 9050
```

---

### Tor

Tor routes traffic through multiple relays for anonymity.

```text
You → Guard → Middle → Exit → Target
```

Key points:

* Uses layered encryption
* Exit node communicates with the destination
* No single relay knows both source and destination

Basic setup:

```bash
sudo systemctl start tor
proxychains curl https://ifconfig.me
```

Limitations:

* Slow for heavy scanning
* Many sites block Tor exits
* Does not protect against account-based identification

---

## Access Control

### Vertical Privilege Escalation

Accessing higher-privileged functionality as a low-privileged user.

Example:

```text
Normal user → /admin
```

Testing approach:

* Capture admin requests
* Replay them as a normal user
* Observe whether access checks exist

Common targets:

* Admin panels
* Role parameters
* JWT claims
* Hidden admin APIs

---

### Horizontal Privilege Escalation

Accessing another user's data at the same privilege level.

Example:

```text
/account?id=1001 → change to 1002
```

Typical indicators:

* Numeric IDs
* UUIDs leaked in responses
* Account IDs in cookies or JSON bodies

---

## SQL Injection (SQLi)

Occurs when user input is inserted into SQL queries without proper sanitization.

### Example

```sql
' OR '1'='1
```

Possible outcomes:

* Authentication bypass
* Data extraction
* Database manipulation

### Common tests

```sql
'
' OR '1'='1
' OR SLEEP(5)--
```

### SQLi categories

| Type             | Description                          |
| ---------------- | ------------------------------------ |
| Error-based      | DB errors reveal information         |
| Union-based      | Uses UNION SELECT to extract data    |
| Boolean blind    | Response changes indicate true/false |
| Time-based blind | Delays indicate successful execution |

---

## XSS (Cross-Site Scripting)

XSS occurs when untrusted input is rendered as executable JavaScript in the browser.

### Basic payloads

```html
<script>alert(1)</script>
<img src=x onerror=alert(1)>
```

### Types

| Type      | Description                     |
| --------- | ------------------------------- |
| Reflected | Payload reflected immediately   |
| Stored    | Payload saved server-side       |
| DOM-based | Executed through client-side JS |

### Impact

* Session theft
* Account takeover
* Forced actions
* Redirects/phishing

---

## File Upload Vulnerabilities

Improper file validation can lead to remote code execution.

### Common attack vectors

#### Web shell upload

```php
<?php system($_GET['cmd']); ?>
```

#### Extension bypass

```text
shell.php5
shell.phtml
shell.PHP
```

#### MIME spoofing

```text
Content-Type: image/jpeg
```

#### SVG-based XSS

```xml
<svg>
<script>alert(1)</script>
</svg>
```

### Secure handling

* Validate file type server-side
* Rename uploads
* Store uploads outside web root
* Disable execution permissions

---

## Mouse Hover Attacks

Some attacks trigger on hover rather than click.

Example concepts:

* CSS-triggered requests
* Hidden overlays
* Drag-and-drop abuse

These attacks can bypass click-based assumptions and may work without direct interaction.

---

## VulnBank Practical Testing

### SQL Injection

Tested login fields with:

```sql
admin'--
```

### IDOR

Modified account identifiers:

```text
/api/transactions?account=1001
```

### JWT privilege escalation

Modified admin-related claims in tokens during testing.

### XSS

Injected payloads into reflected input fields.

### File upload testing

Tested:

* `.php`
* `.php5`
* `.phtml`

### Mass assignment

Injected extra JSON fields:

```json
{
  "isAdmin": true
}
```

---

## Main Takeaways

* OPSEC matters even during simple recon
* Proxies are essential for understanding web traffic
* Access control flaws often come from trusting client input
* SQLi and XSS still appear frequently in insecure applications
* File upload functionality is a major attack surface
* Enumeration and request manipulation are key parts of web testing
* Always verify authorization server-side, not client-side

