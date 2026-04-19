Challenge: Wireshark Packet Analysis

Date: March 28, 2026 Flags i Found: 4/5
Tools i Used

    Wireshark — packet capture and analysis
   on terminal — Base64 decoding

Challenge 1 — HTTP Traffic

Flag: FLAG{http_easy_capture} Hint: "Sometimes secrets travel unencrypted. Inspect the messages your browser asks for."
Method

    Opened challenge_1_traffic.pcap in Wireshark
    Applied filter: http
    Right-clicked a packet → Follow → HTTP Stream
    Flag found in plain HTML response body


HTTP is unencrypted — everything including credentials, cookies, and page content is visible in plain text. This is why HTTPS exists.

Challenge 1
Challenge 2 — DNS Exfiltration

Flag: FLAG{dns_exfiltration} Hint: "Even small questions can carry hidden messages. Check what your system is asking the internet."
Method

    Opened challenge_2_traffic.pcap in Wireshark
    Applied filter: dns
    Found suspicious DNS query to: FLAG_dns_exfiltration_.example.com
    Flag was encoded in the subdomain of the DNS query

DNS queries are rarely blocked by firewalls. Attackers use DNS to exfiltrate data by encoding it inside domain name queries — the data leaves the network disguised as normal DNS traffic.

Challenge 2
Challenge 3 — TCP Stream Reassembly

Flag: FLAG{tcp_stream_reassembly} Hint: "The answer might be broken into pieces across multiple packets. Reassemble to see the whole picture."
Method

    Opened challenge_3_traffic.pcap in Wireshark
    Applied filter: tcp
    Right-clicked a packet → Follow → TCP Stream
    Wireshark reassembled all packets into full message:

Hello,
Here is your secret flag:
FLAG{tcp_stream_reassembly}
Thank you!

TCP data is split across multiple packets. Following the stream tells Wireshark to reassemble them in order to show the complete conversation.

Challenge 3
Challenge 4 — SMTP Base64 Encoded

Flag: FLAG{smtp_base64} Hint: "Some messages arrive encoded. The transport might reveal patterns in the body rather than the headers."
Method

    Opened challenge_4_traffic.pcap in Wireshark
    Applied filter: smtp
    Found two email streams:
        First stream: body said "Wrong flag, try again!"
        Second stream: body contained Base64 string

RkxBR3tzbXRwX2Jhc2U2NUh0=

    Decoded in terminal:

echo "RkxBR3tzbXRwX2Jhc2U2NUh0=" | base64 -d
FLAG{smtp_base64}

Attackers encode data to avoid simple keyword detection. Base64 doesn't encrypt — it just encodes. Anyone who spots it can decode it.

Challenge 4 - Wrong flag Challenge 4 - Real flag

ICMP ping packets have a data payload field. By default ping fills it with random bytes, but attackers can replace it with any data. This is called ICMP tunneling — used to smuggle data past firewalls that only block TCP/UDP.
detailed:
# 	Flag 	Protocol 	Method
1 	FLAG{http_easy_capture} 	HTTP 	Follow HTTP stream
2 	FLAG{dns_exfiltration} 	DNS 	Filter DNS, read domain
3 	FLAG{tcp_stream_reassembly} 	TCP 	Follow TCP stream
4 	FLAG{smtp_base64} 	SMTP 	Follow SMTP stream + base64 -d
Lesson i got:
    HTTP is unencrypted — never send sensitive data over HTTP
    DNS can be used to exfiltrate data — monitor unusual domain queries
    TCP streams must be reassembled to read full conversations
    Wireshark filters (http, dns, tcp, smtp, icmp) are essential for isolating relevant traffic quickly
    Base64 is encoding not encryption — easy to decode
    ICMP payloads can carry hidden data — don't ignore ping traffic
    

