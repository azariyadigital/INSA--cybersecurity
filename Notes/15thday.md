

## 1. IDOR / BOLA is fundamentally an authorization problem

A lot of beginners think:

> “The vulnerability is the predictable ID.”

But the predictable ID is only the *symptom*.

The real issue is:

```text
Server receives object reference
→ does not verify ownership/authorization
→ returns object anyway
```

Even a UUID is vulnerable if authorization checks are missing.

Bad:

```python
invoice = db.get_invoice(invoice_id)
return invoice
```

Correct:

```python
invoice = db.get_invoice(invoice_id)

if invoice.owner_id != current_user.id:
    return 403
```

That distinction matters in interviews and real pentests.

---

# SQLi vs XSS — Most Important Difference

These are both “injection,” but they target completely different interpreters.

| Vulnerability | Injecting Into  | Executes Where   |
| ------------- | --------------- | ---------------- |
| SQL Injection | SQL query       | Database server  |
| XSS           | HTML/JavaScript | Victim’s browser |

A useful mental model:

```text
SQLi  → attack the backend/database
XSS   → attack the frontend/user
```

---

# SameSite vs HttpOnly — Easy Exam Question

A super common confusion:

| Attribute | Stops                                        |
| --------- | -------------------------------------------- |
| HttpOnly  | JavaScript reading cookies                   |
| Secure    | Cookie theft over HTTP                       |
| SameSite  | Cross-site request sending (CSRF mitigation) |

Key insight:

> SameSite does NOT stop XSS from reading non-HttpOnly cookies.

Because once XSS runs on the origin:

```text
the browser considers it same-site
```

So:

```javascript
document.cookie
```

still works unless:

```http
HttpOnly
```

is set.

That distinction is critical.

---

# Parameter Pollution Is More Dangerous Than It Looks

This becomes extremely important in modern architectures:

```text
Browser
↓
CDN
↓
WAF
↓
Load balancer
↓
Framework
↓
App
```

Different layers may parse:

```text
role=user&role=admin
```

differently.

Example:

* WAF validates first param (`user`)
* Backend uses last param (`admin`)

That parser mismatch is the actual exploit primitive.

---

# Second-Order SQLi Is a Senior-Level Concept

A lot of people understand:

```text
Input → query → immediate injection
```

Second-order SQLi is harder because:

```text
Input → stored safely
↓
later reused unsafely
↓
injection occurs somewhere else
```

That mirrors many real-world enterprise bugs.

---

# SSRF Is One of the Most Important Modern Vulns

SSRF matters because cloud infrastructure changed everything.

Before cloud:

```text
Internal network exposure
```

Now:

```text
Cloud metadata services
IAM credentials
Internal APIs
Kubernetes control planes
```

Classic AWS target:

```text
169.254.169.254
```

This is why SSRF is considered extremely high severity today.

---

# XSS Payload Selection Depends on Context

This is one of the biggest practical lessons.

You don’t just throw:

```html
<script>alert(1)</script>
```

You first determine context:

| Context   | Payload Style    |
| --------- | ---------------- |
| HTML body | `<script>`       |
| Attribute | `" onmouseover=` |
| JS string | `';alert(1)//`   |
| SVG/XML   | `<svg onload=`   |

Real XSS work is mostly:

```text
context analysis
```

not payload memorization.

---

# Why HttpOnly Is So Important

Without HttpOnly:

```javascript
document.cookie
```

→ session theft
→ account takeover

With HttpOnly:

* XSS still exists
* attacker can still perform actions as victim
* BUT direct session cookie theft becomes much harder

This dramatically reduces impact severity.

---

# MSSQL xp_cmdshell = SQLi → RCE

This is one of the classic escalation paths:

```text
SQL Injection
↓
DB admin privileges
↓
xp_cmdshell
↓
Operating system command execution
↓
Full server compromise
```

That’s why:

```text
least privilege DB accounts
```

matter so much.

---

# Extremely Important Practical Skill

Your notes now consistently separate:

```text
Vulnerability
↓
Root cause
↓
Exploit method
↓
Impact
↓
Mitigation
```


