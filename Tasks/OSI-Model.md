OSI Model — All About.

Name: Azariya Chombe
Date: March 22, 2026
What is the OSI Model?

The OSI (Open Systems Interconnection) Model is a way to understand how data moves from one device to another over a network. It breaks communication into 7 layers, where each layer has a specific job.

It was created by ISO to make sure different systems and technologies can communicate without problems.

In simple terms:
It explains how data travels from your device to another device step by step.

The 7 Layers (Explained Simply)
Layer 1 — Physical

This is where actual transmission happens.

Sends raw bits (0s and 1s)
Uses cables, fiber, or wireless signals
Devices: cables, hubs

Example:
When you connect a cable or use WiFi, this layer carries the signals.

Security:
If someone gets physical access, they can interfere with or capture data.

Layer 2 — Data Link

Handles communication inside the same network.

Uses MAC addresses
Sends data as frames
Devices: switches, network cards

Example:
Your laptop sending data to your router at home.

Security:
MAC spoofing and ARP attacks can be used to intercept traffic.

Layer 3 — Network

Responsible for routing data between different networks.

Uses IP addresses
Sends packets
Devices: routers

Example:
Sending data from Ethiopia to a server in another country.

Security:
IP spoofing and routing attacks can redirect or fake traffic.

Layer 4 — Transport

Ensures data is delivered correctly.

Breaks data into segments
Uses TCP and UDP
Works with ports

TCP:

Reliable and ordered
Slower

UDP:

Faster but less reliable

Example:
Streaming uses UDP, websites use TCP.

Security:
SYN flood and port scanning attacks happen here.

Layer 5 — Session

Manages connections between devices.

Starts and ends sessions
Keeps communication active

Example:
Staying logged into a website while browsing.

Security:
Session hijacking allows attackers to take over active sessions.

Layer 6 — Presentation

Handles how data is formatted.

Encryption and decryption
Data conversion and compression

Example:
HTTPS encrypts your data before sending.

Security:
Weak encryption can expose sensitive data.

Layer 7 — Application

Closest to the user.

Provides services like web browsing and email
Protocols: HTTP, HTTPS, FTP, DNS

Example:
Opening a website in your browser.

Security:
Most attacks happen here like phishing, SQL injection, and XSS.

How Data Moves
Sending (top → bottom)
Application creates data
Presentation encrypts
Session manages connection
Transport splits data
Network adds IP
Data Link adds MAC
Physical sends bits
Receiving (bottom → top)

Everything is reversed until the data reaches the application.

OSI vs TCP/IP
OSI is mainly for learning and understanding
TCP/IP is what the internet actually uses

Mapping:

OSI top 3 layers → TCP/IP Application
Transport → Transport
Network → Internet
Bottom layers → Network Access
Security by Layer (Quick View)
Layer 7: Web attacks (XSS, SQL injection)
Layer 6: Encryption issues
Layer 5: Session attacks
Layer 4: Port and DoS attacks
Layer 3: IP-based attacks
Layer 2: MAC and ARP attacks
Layer 1: Physical access attacks
