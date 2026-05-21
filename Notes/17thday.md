# Day 17 — File Upload Vulnerabilities

## Major Improvements Needed

### 1. Separate “Theory” vs “Lab”

Better structure:

```text
1. Introduction
2. Upload Processing Internals
3. Vulnerability Classes
4. Exploitation Paths
5. Defense Mechanisms
6. Real-World Cases
7. Practical Lab Walkthrough
8. Key Learnings
```

---

# Important Technical Corrections

## Double Extension Clarification

Replace:

> `.php.png` → server executes the `.php` portion

With:

```markdown
Some misconfigured servers may interpret the first extension
(`.php`) instead of the final extension (`.png`),
causing unintended code execution.
```

---

## Null Byte Injection Context

Add:

```markdown
Null byte attacks are primarily historical but still appear in
legacy systems and custom file handling code.
```

---

# Add This Missing Section

## Dangerous Server-Side Image Processing

Many applications process uploads after storage:

* thumbnail generation
* resizing
* OCR
* metadata extraction
* antivirus scanning

If these libraries are vulnerable, even “safe” image uploads can trigger RCE.

Examples:

* ImageMagick
* ExifTool
* Ghostscript
* ffmpeg

> The uploaded file itself may exploit the backend processor before anyone even opens it.

---

# Add Upload Architecture Diagram

```text
[User Upload]
      ↓
[Validation Layer]
      ↓
[Storage Engine]
      ↓
[Processing Pipeline]
      ↓
[Web Server]
      ↓
[Victim Access]
```

---

# Add This Defense Section

## Content-Disposition: attachment

```http
Content-Disposition: attachment
```

This forces browsers to download uploaded files instead of rendering them inline, reducing upload-based XSS risks from SVG or HTML files.

---

# Add Modern Cloud Storage Note

```markdown
Modern architectures often isolate uploads in object storage
(AWS S3, GCS, Azure Blob) instead of serving directly from the
application server.
```

---

# Rewrite the Web Shell Section

Replace the direct payload with:

```markdown
A minimal server-side script capable of executing system commands
was uploaded to demonstrate the impact of executable file uploads.
```

Replace the sudo section with:

```markdown
Python’s ability to spawn subprocesses allowed privilege escalation
when executed with unrestricted sudo permissions.
```

---

# Add Detection & Monitoring

## Detection & Monitoring Opportunities

| Activity                     | Detection Signal                      |
| ---------------------------- | ------------------------------------- |
| Suspicious upload extensions | WAF / SIEM alerts                     |
| MIME mismatch                | Upload validation logs                |
| Web shell execution          | Child processes spawned by web server |
| Outbound callbacks           | DNS / HTTP beaconing alerts           |
| Privilege escalation         | sudo audit logs                       |
| Repeated upload attempts     | Rate-limit anomalies                  |

---

# Add Final Takeaway

## Core Lesson

```text
File upload vulnerabilities are rarely “just upload bugs.”

They become dangerous when combined with:
- weak validation
- executable storage paths
- vulnerable processing libraries
- poor server configuration
- exposed credentials
- privilege escalation paths

Real-world compromise usually comes from chaining these weaknesses together.
```

