# Day 12 — Burp Suite, Enumeration, and Brute Force Concepts

**Date:** April 26, 2026

---

## HTTP Methods

HTTP methods define the action a client wants the server to perform.

| Method  | Purpose                       |
| ------- | ----------------------------- |
| GET     | Retrieve data from the server |
| POST    | Send data to the server       |
| PUT     | Replace an existing resource  |
| PATCH   | Update part of a resource     |
| DELETE  | Remove a resource             |
| HEAD    | Retrieve headers only         |
| OPTIONS | Show allowed methods          |

Different HTTP methods can affect how applications process requests. During testing, changing request methods sometimes reveals inconsistent validation or unintended behavior.

For example:

* an endpoint accepting unexpected methods may indicate weak configuration
* `OPTIONS` requests can help identify allowed actions during reconnaissance

---

## HTTP Status Codes

Status codes describe how the server handled a request.

### Main Categories

| Range | Meaning           |
| ----- | ----------------- |
| 1xx   | Informational     |
| 2xx   | Successful        |
| 3xx   | Redirection       |
| 4xx   | Client-side error |
| 5xx   | Server-side error |

### Common Status Codes

| Code | Description             |
| ---- | ----------------------- |
| 200  | Request successful      |
| 201  | Resource created        |
| 301  | Permanent redirect      |
| 302  | Temporary redirect      |
| 400  | Bad request             |
| 401  | Authentication required |
| 403  | Access forbidden        |
| 404  | Resource not found      |
| 405  | Method not allowed      |
| 429  | Too many requests       |
| 500  | Internal server error   |
| 503  | Service unavailable     |

During brute-force or fuzzing activities, differences in response codes often indicate meaningful changes in server behavior.

For example:

* repeated `200` responses may indicate failed attempts
* a `302` redirect after login attempts may indicate successful authentication

---

## Parameters in Web Requests

Parameters are used to send data between the client and the server.

### Query Parameters

Example:

```text id="v4r7ma"
/search?query=test&page=1
```

These are visible directly in the URL and should never contain sensitive information.

### Body Parameters

Typically used with POST requests:

```text id="w7t9bo"
username=admin&password=test
```

These values are not shown in the URL but are still visible if transmitted without HTTPS.

### Path Parameters

Example:

```text id="m2n6xp"
/users/42/profile
```

Changing path values is commonly used to test access control weaknesses such as IDOR vulnerabilities.

---

## Regular Expressions (Regex)

Regular expressions are patterns used to match or extract text.

They are commonly used in:

* log analysis
* filtering output
* Burp Suite matching rules
* searching captured responses

### Useful Patterns

| Pattern | Purpose           |
| ------- | ----------------- |
| `.`     | Any character     |
| `*`     | Zero or more      |
| `+`     | One or more       |
| `^`     | Start of line     |
| `$`     | End of line       |
| `\d`    | Digit             |
| `\w`    | Word character    |
| `[a-z]` | Lowercase letters |

### Example Usage

```bash id="a9r3cb"
grep "flag" file.txt
```

```bash id="hj9vpf"
grep -oE "BB\{[^}]+\}" response.txt
```

Regex is especially useful during large-scale response analysis where manually reviewing output would be inefficient.

---

## OWASP Top 10 Overview

The OWASP Top 10 identifies major categories of web application security risks.

### Broken Access Control

Occurs when users can access resources or actions they should not be allowed to use.

Example:

* changing user IDs to access another account

### Cryptographic Failures

Sensitive data becomes exposed because encryption is weak or improperly implemented.

Example:

* passwords stored in plaintext
* secrets exposed in frontend JavaScript

### Injection

User input is interpreted as commands or queries by the backend.

Examples:

* SQL injection
* command injection

### Security Misconfiguration

Improper server configuration creates unnecessary exposure.

Examples:

* default credentials
* verbose error messages
* unused features left enabled

### Vulnerable Components

Applications using outdated software may inherit publicly known vulnerabilities.

### Authentication Failures

Weak authentication controls allow unauthorized access.

Examples:

* weak passwords
* lack of rate limiting
* insecure session handling

### Logging and Monitoring Failures

Insufficient logging makes attacks difficult to detect or investigate.

### SSRF

The server fetches attacker-controlled URLs, potentially exposing internal systems or cloud metadata.

---

## Authentication and Access Control

### Authentication vs Authorization

| Concept        | Meaning               |
| -------------- | --------------------- |
| Authentication | Verifying identity    |
| Authorization  | Verifying permissions |

A user may be authenticated successfully but still lack permission to access certain resources.

---

## Common Authentication Weaknesses

### Weak Password Policies

Simple or predictable passwords increase the risk of brute-force attacks.

### Missing Rate Limiting

Unlimited login attempts allow attackers to test large numbers of credentials.

### Verbose Error Messages

Different responses for invalid usernames versus invalid passwords can help attackers enumerate valid accounts.

---

## Enumeration Concepts

Enumeration involves discovering valid usernames, resources, or identifiers before attempting exploitation.

Useful indicators include:

* different response lengths
* different response times
* different HTTP status codes
* different error messages

Burp Intruder can automate these comparisons efficiently.

---

## Access Control Testing

### IDOR

Changing object identifiers to access unauthorized data.

Example:

```text id="z6g4mr"
/api/users/42
```

Testing another user ID may reveal missing authorization checks.

### Privilege Escalation

Occurs when a user gains permissions beyond their intended role.

Examples:

* accessing admin endpoints as a regular user
* modifying role parameters inside requests

---

## TryHackMe Practice — Enumeration and Brute Force

The room focused on:

* username enumeration
* brute-force attacks
* response analysis
* session handling
* Burp Suite Intruder usage

### Workflow Practiced

1. Capture login request in Burp Proxy
2. Send request to Intruder
3. Select payload positions
4. Load wordlists
5. Analyze response differences
6. Confirm results manually in Repeater

---

## Hydra Practice

Basic Hydra usage was also reviewed for HTTP login testing.

Example:

```bash id="jq8hwy"
hydra -l admin -P rockyou.txt <target> http-post-form
```

Hydra automates repeated authentication attempts against supported services.

---

## Main Takeaways

* HTTP response differences can reveal important information during testing
* Enumeration is often the first step before exploitation
* Burp Suite helps visualize and manipulate requests efficiently
* Weak authentication controls remain one of the most common security issues
* Access control testing is critical for identifying unauthorized access paths
* Regular expressions are useful for extracting patterns from large amounts of data

