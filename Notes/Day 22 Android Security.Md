# Android Application Security Notes

**Author:** Azariya Chombe
**Date:** June 5, 2026

## Overview

Today I learned the fundamentals of Android Application Security and Mobile Application Pentesting. The goal of Android security testing is to identify vulnerabilities that could allow attackers to steal data, bypass authentication, access unauthorized information, or compromise users and systems.

---

# What is an APK?

APK stands for **Android Package Kit**.

An APK is the installation file format used by Android devices, similar to:

* Windows → `.exe`
* Android → `.apk`

Examples:

```text
Instagram.apk
WhatsApp.apk
banking.apk
```

An APK contains:

* Application code
* Resources
* Images and icons
* AndroidManifest.xml
* Libraries
* Certificates

An APK can be analyzed and reverse engineered.

---

# Static Analysis

Static Analysis means examining an application **without running it**.

Purpose:

* Understand application behavior
* Review source code
* Identify vulnerabilities
* Discover hidden functionality

Tools:

* JADX
* APKTool

Workflow:

```text
APK
 ↓
JADX
 ↓
Source Code Review
```

---

# JADX

JADX is an Android decompiler.

Purpose:

* Convert APK files into readable Java code
* Understand application logic
* Review application structure

Common Uses:

### Finding API Endpoints

Search:

```text
https://
http://
```

Example:

```java
https://api.example.com/login
```

### Finding Authentication Logic

Search:

```text
login
signin
authenticate
```

### Finding Secrets

Search:

```text
apikey
api_key
token
secret
password
```

Example:

```java
API_KEY = "123456"
```

### Reviewing Permissions

Open:

```text
AndroidManifest.xml
```

Look for:

```xml
<uses-permission android:name="android.permission.INTERNET"/>
```

---

# Dynamic Analysis

Dynamic Analysis means testing the application while it is running.

Purpose:

* Observe real behavior
* Monitor network traffic
* Test security controls
* Analyze application functionality

Workflow:

```text
APK
 ↓
Install
 ↓
Run
 ↓
Observe
```

---

# Genymotion

Genymotion is an Android emulator.

Purpose:

* Create virtual Android devices
* Install APKs
* Test applications safely

Think of Genymotion as:

```text
A virtual Android phone on a computer
```

Uses:

* Install applications
* Test functionality
* Perform dynamic analysis
* Observe application behavior

Workflow:

```text
APK
 ↓
Genymotion
 ↓
Run App
 ↓
Test Features
```

---

# Difference Between JADX and Genymotion

JADX:

```text
Read the application
```

Genymotion:

```text
Run the application
```

Comparison:

```text
JADX = Static Analysis
Genymotion = Dynamic Analysis
```

---

# Burp Suite

Purpose:

* Intercept HTTP/HTTPS traffic
* Analyze requests and responses
* Test API security

Workflow:

```text
App
 ↓
Burp Suite
 ↓
Server
```

Questions to Ask:

* Is data encrypted?
* Are tokens exposed?
* Can requests be modified?

---

# APKTool

Purpose:

* Decode APK files
* Extract resources
* Review AndroidManifest.xml
* Modify applications

Common Uses:

* View configuration files
* Review permissions
* Rebuild APKs

---

# ADB (Android Debug Bridge)

Purpose:

* Control Android devices from the terminal

Examples:

```bash
adb devices
adb install app.apk
adb shell
```

Uses:

* Install applications
* Open a device shell
* Debug applications

---

# Frida

Purpose:

* Analyze applications while running
* Hook functions
* Monitor behavior

Uses:

* Runtime analysis
* Security testing
* Understanding application execution

---

# Mobile Application Security Risks

## Hardcoded Secrets

Example:

```java
API_KEY = "secret"
```

Risk:

Attackers can extract and abuse credentials.

---

## Insecure Data Storage

Examples:

* Stored passwords
* Stored tokens
* Sensitive files

Risk:

Sensitive information may be exposed.

---

## Broken Authentication

Examples:

* Weak login protections
* Predictable tokens
* Poor password handling

Risk:

Account compromise.

---

## Broken Authorization

Example:

```text
/user/1001
```

Changed to:

```text
/user/1002
```

Risk:

Access to another user's data.

---

## Excessive Permissions

Example:

```text
Flashlight App
 ↓
Requests Contacts Permission
```

Risk:

Unnecessary access to user information.

---

## Insecure Network Communication

Risk:

Sensitive information may be intercepted or exposed.

---

# Typical Mobile Pentesting Workflow

```text
APK
 ↓
JADX
 ↓
Understand Code
 ↓
Genymotion
 ↓
Run Application
 ↓
Burp Suite
 ↓
Capture Traffic
 ↓
Frida
 ↓
Runtime Analysis
```

---

# Key Takeaways

1. APK files contain Android applications.
2. JADX is used for static analysis.
3. Genymotion is used for dynamic analysis.
4. Burp Suite is used to inspect network traffic.
5. APKTool helps inspect and modify APK contents.
6. ADB controls Android devices from the terminal.
7. Frida helps analyze application behavior during execution.
8. Mobile security focuses on protecting users, data, authentication, authorization, and communications.

#Homeworks
-Take a begineer course on Java
