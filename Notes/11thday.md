# Day 11 — CTF Event and Burp Suite Practice

**Date:** April 25, 2026

---

## CTF Participation

Participated in a Capture The Flag event focused mainly on web reconnaissance and source analysis challenges. Managed to solve two challenges during the session.

---

## Challenge 1 — HTTP Header Analysis

### Category

Web / Reconnaissance

### Main Concept

The objective of the challenge was to identify hidden information stored inside HTTP response headers instead of the visible webpage content.

The webpage itself appeared normal, but one of the custom response headers contained encoded data.

### Investigation Process

The response headers were inspected using:

```bash id="v1pjso"
curl -s -D -
```

During inspection, a non-standard header containing encoded text was discovered.

The value was Base64 encoded and decoding it revealed the flag.

### Important Observation

This challenge demonstrated that useful information may exist outside the HTML body itself. HTTP headers, especially custom headers beginning with `x-`, should always be reviewed carefully during reconnaissance.

It also reinforced the importance of recognizing Base64 patterns quickly during CTF investigations.

---

## Challenge 2 — Client-Side Source Review

### Category

Web / Source Analysis

### Main Concept

The second challenge involved analyzing publicly accessible JavaScript files linked from the webpage source.

A JavaScript file contained a configuration object with sensitive information left inside commented code.

### Investigation Process

The page source revealed an external JavaScript file:

```html id="mvr4xw"
<script src="js/main.js"></script>
```

After downloading and reviewing the file contents, a Base64 encoded token value was identified inside an old configuration section.

Decoding the value exposed the hidden flag.

### Important Observation

Client-side files are fully accessible to users and should never contain secrets, API keys, tokens, or sensitive credentials.

This challenge highlighted the importance of reviewing:

* JavaScript files
* comments
* source maps
* hidden assets
* frontend configuration objects

during web enumeration.

---

## Burp Suite Practice Session

### Practice Environment

OWASP Juice Shop was used for practicing Burp Suite features and HTTP request analysis.

---

## Proxy and Request Interception

The browser was configured to send traffic through Burp Suite’s proxy listener using the default local proxy settings.

Captured requests were analyzed to better understand:

* request methods
* headers
* cookies
* request bodies
* server responses

### Forward vs Drop

Two important actions inside Burp Proxy were practiced:

| Action  | Purpose                                  |
| ------- | ---------------------------------------- |
| Forward | Sends the request to the server normally |
| Drop    | Blocks the request completely            |

Dropping requests helped demonstrate which requests were essential for page functionality.

---

## Repeater Usage

Requests captured in Proxy were forwarded to Repeater for manual testing.

This allowed parameter values to be modified repeatedly without refreshing the browser each time.

### Request Manipulation

Different values were tested inside parameters and headers to observe server behavior changes.

Examples included:

* changing IDs
* editing cookies
* modifying request headers
* switching request methods

Server responses were compared using:

* response codes
* response length
* returned content

Differences between responses often indicate interesting server-side behavior.

---

## Intruder Introduction

Basic Intruder usage was introduced for testing multiple input values automatically against a parameter.

The focus was mainly on observing:

* response differences
* failed vs successful responses
* variations in content length and status codes

---

## cURL and Burp Integration

Requests intercepted in Burp were replayed and modified using cURL commands.

Several useful cURL flags were practiced:

| Flag      | Purpose                     |
| --------- | --------------------------- |
| `-X`      | Specify HTTP method         |
| `-H`      | Add custom headers          |
| `-d`      | Send request body data      |
| `-i`      | Include response headers    |
| `-L`      | Follow redirects            |
| `--proxy` | Route traffic through proxy |

This helped connect command-line HTTP interaction with Burp Suite request analysis.

---

## Main Lessons Learned

### HTTP Headers Matter

Sensitive information may exist in headers even when the page body appears normal.

### Client-Side Code Should Never Store Secrets

JavaScript files are publicly accessible and should not contain credentials or tokens.

### Repeater Is Useful for Controlled Testing

Repeater makes it easier to manually test how the server reacts to modified requests.

### Response Differences Provide Clues

Changes in status codes, response length, or returned data can reveal hidden functionality or vulnerabilities.

### Burp Suite Helps Visualize HTTP Traffic

Intercepting and modifying requests improves understanding of how web applications communicate internally.

