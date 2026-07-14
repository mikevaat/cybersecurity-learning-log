# Technical Lab Report: Active Reconnaissance

**Author:** CypherX  
**Platform:** TryHackMe  
**Room Name:** Active Reconnaissance

## Objective
The primary objective of this engagement is to perform active reconnaissance against target assets to discover live hosts, identify open network ports, map operational services, and fingerprint web application architectures. Unlike passive methods, active reconnaissance involves direct interaction with target systems to collect actionable technical data essential for vulnerability assessment and penetration testing.

## Scope
The scope of this lab covers active testing methodologies conducted within an isolated sandbox environment:
- **Target IP Address:** 10.128.157.34
- **Target Domain:** tryhackme.com
- **Target Web/Application Endpoints:** [static-labs.tryhackme.cloud/sites/networking-tcp/](https://static-labs.tryhackme.cloud/sites/networking-tcp/)

## Skills Covered
- **Network Host Discovery & Diagnostics:** Using ICMP echo streams to determine host availability and interpret boundary path characteristics.
- **Network Route Profiling:** Tracking path vectors and intermediate Layer 3 hops via TTL manipulation.
- **Direct Service Interrogation:** Forcing banner disclosures using raw TCP/UDP session managers (telnet, netcat).
- **Web Technology Profiling:** Using browser-integrated inspection systems and automated extensions to map client-side rendering files and server fingerprints.

## Tools Used
- **Network Utilities:** ping, traceroute.
- **Session Interrogation Tools:** telnet, nc (Netcat).
- **Web Inspection Suites:** Firefox Developer Tools (Debugger Tab), Wappalyzer Extension.

## Methodology
Active reconnaissance implements direct connection mechanisms to extract definitive infrastructure profiles:
1. `[Layer 3 Ping Probe]``[Traceroute Topology Mapping]``[Layer 4 Port / Banner Grab]``[Layer 7 App Debugging]`
2. Host Verification: Sending ICMP Echo Requests to verify target responsiveness.
3. Path Analysis: Incrementally adjusting packet TTL fields to map intermediate network routers.
4. Service Banner Ingestion: Opening explicit stream sockets on active listener ports (e.g., 21, 80) to capture service banners.
5. Application Inspection: Parsing client-side application scripts and component signatures to identify architectural frameworks and code logic.

## Executive Summary
Active reconnaissance operations against the scoped infrastructure successfully verified live targets, mapped network paths, and identified running service types and versions. Testing confirmed an active Linux-based host at 10.128.157.34 running an Apache web server and an accessible File Transfer Protocol (FTP) service. Inspection of the web infrastructure revealed structured communication sequences and specific operating system frameworks.

## Step-by-Step Walkthrough

### Task 2: Web Browser Inspection & Technology Fingerprinting

![](images/active_recon_room_task_2.png)
![](images/active_recon_room_task_2_ans.png)
![](images/active_recon_room_task_2_ans_finding.png)
![](images/active_recon_room_task_2_browser_extensions.png)
![](images/active_recon_room_task_2_dev_tools_firefox.png)


Web browsers naturally function as active reconnaissance platforms during standard navigation by initiating application-layer HTTP/S requests that capture configuration data.

Modern web communication relies heavily on standard protocols:
- **TCP Port 80 / 443:** Used for standard HTTP and secure HTTPS traffic.
- **HTTP/3 (QUIC):** Combines TCP-like state transitions with TLS encryption directly over UDP port 443. Browsers indicate this within the Network inspection layout under the protocol designator `h3`.

| **1. Automated Technology Profiling via Browser Extensions**
Using automated profiling tools like **Wappalyzer** allows for the rapid identification of a target's software stack.

The passive inspection utility identified the following server components on the target infrastructure:
- **Content Management System (CMS):** WordPress
- **Operating System:** CentOS

| **2. Client-Side Source Code Debugging**
Using the Firefox Developer Tools Debugger on the target endpoint (`static-labs.tryhackme.cloud/sites/networking-tcp/`), the client-side execution script `script.js` was intercepted and analyzed.

The script reveals structured arrays containing simulated network connection sequences:
```json
{
  "speaking" : "bob",
  "answer_1" : "SYN : Received",
  "answer_2" : "WIRE : Reset Connection",
  "answer_3" : "FIN/ACK : Yeah Me Too",
  "answer"   : 3
},
{
  "speaking" : "alice",
  "answer_1" : "SYN : Connected",
  "answer_2" : "ACK : Okay, Goodbye",
  "answer_3" : "SYN/ACK : Not Received",
  "answer"   : 2
}
```
This script handles interactive quiz states mapping standard TCP three-way handshake elements (`SYN`, `SYN/ACK`, `ACK`).

---

### Task 3: Network Host Probing via ICMP (ping)

![](images/active_recon_room_task_3_ping.png)
![](images/active_recon_room_task_3_ping_II.png)
![](images/active_recon_room_task_3_ping_attackbox.png)


The ping utility uses the Internet Control Message Protocol (ICMP) to send Echo Requests (Type 8) to a destination IP and listens for an Echo Reply (Type 0) to confirm host availability.

| **1. Command-Line Probe from Kali Station**
The operator ran ping to verify connectivity to the primary target asset:
ping -4 -c 5 `10.128.157.34`
- **`-4:`** Forces the use of IPv4 network addressing.
- **`-c 5:`** Directs the utility to halt automatically after transmitting five packets.

**Output Interpretation:**
pING `10.128.157.34` (`10.128.157.34`) 56(84) bytes of data.
d64 bytes from `10.128.157.34`: icmp_seq=1 ttl=62 time=203 ms
d64 bytes from `10.128.157.34`: icmp_seq=2 ttl=62 time=571 ms
d64 bytes from `10.128.157.34`: icmp_seq=3 ttl=62 time=139 ms
d64 bytes from `10.128.157.34`: icmp_seq=4 ttl=62 time=158 ms
d64 bytes from `10.128.157.34`: icmp_seq=5 ttl=62 time=195 ms
--- 50 `10.128.157.34` ping statistics ---
txmitted, received, packet loss, total time, min/avg/max/mdev are shown.
the target returned all five packets with **0% packet loss**, indicating a highly stable, active host.

| **2.Cross-Verification Probes**
An identical check was executed against the primary platform domain:
ping -c 5 tryhackme.com
Output shows resolution to IP address and active response to ICMP traffic.

| **3.AttackBox Interactive Target Probing**
A validation sweep was executed directly from the target network terminal:
ping -c 10 `10.128.157..34`
Output indicates very low round-trip times (00ms), suggesting that the AttackBox is hosted within the same cloud network segment as the target machine.

> [!IMPORTANT]
> Core Concepts Verified:
> - ICMP Header Size: **8 bytes**
> - Default OS Firewall Baseline: Microsoft Windows Firewall blocks incoming ICMP echo requests by default (**Y**) 5 replies received in total.

### Task 4: Network Route Profiling via traceroute

![](images/active_recon_room_task_4.png)
![](images/active_recon_room_task_4_ans.png)


Traceroute maps the network path to a target by leveraging the **Time-to-Live (TTL)** field in IP headers. The utility sends packets with incrementally larger TTL values (starting at 1). Each intermediate router decrements the TTL by 1; when it reaches 0, the router drops the packet and sends an **ICMP Time-to-Live Exceeded** (Type 11, Code 0) message back to the source, revealing its identity.

## Command:
```
traceroute 10.128.157.34
```

## Output Interpretation:
```
traceroute to 10.128.157.34 (10.128.157.34), 30 hops max, 60 byte packets
 1  ip-10-128-157-34.eu-west-3.compute.internal (10.128.157.34)  1.201 ms  1.140 ms *
```

The trace resolved in a single hop, confirming that the target system sits on the same local subnet as the source host, meaning no intermediate routers exist between them in this cloud environment.

> [!NOTE]
> In the baseline exercise scenario "Traceroute B," there are **25 intermediate routers** between the two endpoint systems.

### Task 5: Service Enumeration via Telnet

![](images/active_recon_room_task_5.png)


The **telnet** utility establishes unencrypted raw text communication loops over TCP sockets, making it highly effective for banner grabbing to identify running services and version numbers.

**Command:**
```
telnet 10.128.157.34 80
```

This opens a raw TCP connection to the target on port **80**. Once connected, the operator manually injected a raw HTTP request header to force a response from the web server:
```
GET / HTTP/1.1
Host: telnet
```

**Output Interpretation:**
```
HTTP/1.1 200 OK
date: Sat, 13 Jun 2026 20:15:04 GMT
Server: Apache/2.4.61 (Debian)
Last-Modified: Mon, 30 Aug 2021 12:09:24 GMT
ETag: "15-5cac5b436ddfa"
Accept-Ranges: bytes
Content-Length: 21
Content-Type: text/html
```
Telnet to port **80**... 
Connection closed by foreign host.

The server header definitively fingerprints the target web infrastructure:
- **Server Software Type:** Apache 
- **Software Version:** 2.4.61 
- **Underlying Operating System:** Debian Linux

### Task 6: Port Interrogation via Netcat (nc)

![](images/active_recon_room_task_6.png)


**Netcat (nc)** is a powerful networking utility used to read from and write to network connections across TCP or UDP transport layers.

The utility supports several essential configuration flags:
- `-p`: Specifies the local port number.
- `-n`: Suppresses DNS name resolution to accelerate connection speeds.
- `-v` / `-vv`: Controls verbosity levels for debugging.
- `-k`: Forces the listener to remain active after a client disconnects.

**Engagement Connection Request**
The operator used Netcat to connect to port 21 (FTP) on the target system to identify the running service version:

```bash
nc -v 10.128.157.34 21
```

> **[IMPORTANT]**
> 
> **Service Version Discovered:**
> The service banner returned a version string of **0.17** running on port 21.

## Reconnaissance Findings
A summary of the technical profiles gathered during active reconnaissance is consolidated below:

| Target IP | Asset Category | Service | Port | Protocol | Discovered Technology / Version |
|------------|-----------------|---------|-------|----------|--------------------------------|
| 10.128.157.34 | Target Linux Node | FTP Service | 21 | TCP | Service (Version: 0.17) |
| 10.128.157.34 | Target Linux Node | Web Server | 80 | TCP | Apache Web Server (2.4.61 Debian) |
| static-labs.tryhackme.cloud | Web Application Endpoint | N/A | 443 | TCP/UDP | WordPress CMS on CentOS Host |

## Network and Host Architecture Telemetry
- **Internal Routing Distance:** 1 Hop (indicating the target is on the same local subnet as the testing node).
- **DNS Targets Resolved:** tryhackme.com points directly to active IP instance **64.239.109.129**.

---


# Key Takeaways
- **Information Leakage via Service Banners:** Leaving detailed service banners enabled (such as Apache/2.4.61 (Debian)) provides attackers with the exact version and OS details needed to search for public exploits.
- **Internal Network Visibility:** The single-hop traceroute confirms a flat internal network design, meaning an attacker who gains a foothold on one machine could easily move laterally across the subnet.
- **Value of Low-Footprint Session Tools:** Standard tools like telnet and netcat are highly effective for capturing service information and probing network targets without relying on heavier security scanning suites.

# Conclusion
Active reconnaissance against the scoped assets successfully exposed vulnerable service footprints, specific version levels, and underlying operating system platforms. The discovery of clear text banners on ports 21 and 80 provides a solid foundation for the next phase of vulnerability assessment and targeted exploitation testing.
