# Technical Lab Report: Scapy Network Packet Analysis

**Author:** CypherX  
**Category:** Network Security / Packet Manipulation / Traffic Analysis  

---

# Executive Summary

This laboratory exercise focused on mastering the foundational capabilities of Scapy, a powerful Python-based interactive packet manipulation program. The lab provided hands-on experience in initializing the Scapy environment, querying protocol structures, and performing real-time network traffic sniffing. By bridging the gap between high-level network commands and low-level packet construction, this session reinforced essential skills in network protocol analysis, traffic inspection, and the mechanics of data transmission.

---

# Lab Objectives

- **Packet Inspection:** Utilize Scapy to examine protocol layer structures and field definitions.
- **Traffic Analysis:** Implement packet sniffing to monitor live network activity.
- **Protocol Understanding:** Gain insight into the composition of IP and other network-level protocols.
- **Interactive Exploration:** Become proficient with Scapy’s interactive shell for rapid network experimentation.

---

# Tools and Technologies

- **Scapy (v2.6.1):** Primary framework for packet creation, sniffing, and analysis.
- **Python (v3.13):** Underlying language supporting the Scapy environment.
- **Linux Terminal (Kali Linux):** Execution environment for command-line tools.
- **Ping Utility:** Used to generate sample network traffic for sniffing exercises.

---

# Technical Background

## What is Scapy?

Scapy is a comprehensive packet manipulation library for Python. Unlike traditional network tools, it allows users to forge or decode packets of a wide number of protocols, send them over the wire, capture them, match requests and replies, and more. It functions as both a command-line tool and a library for Python scripts.

## Why Packet Analysis Matters

Deep visibility into packet structures is fundamental to security operations. It enables:

- **Incident Response:** Analyzing malicious traffic patterns to identify threats.
- **Network Troubleshooting:** Pinpointing connectivity issues by inspecting packet headers.
- **Threat Hunting:** Detecting anomalies in traffic that may indicate unauthorized activity.

---

# Lab Methodology

1. **Environment Initialization:** Launching the Scapy interactive shell.
2. **Protocol Exploration:** Using built-in help and inspection commands to understand protocol fields.
3. **Traffic Generation:** Executing network utilities to create observable traffic.
4. **Packet Sniffing:** Capturing live traffic segments within the Scapy environment.

---

# Step-by-Step Technical Walkthrough

## Step 1: Scapy Initialization

- **Objective**: Initiate the Scapy interactive environment.
- **Evidence Analysis**: I launched the `scapy` command in the root terminal, successfully loading version 2.6.1 and the IPython 8.35.0 shell.
- **Technical Explanation**: Initializing the shell provides access to Scapy’s comprehensive library of packet manipulation functions.


## Step 2: Protocol Field Exploration

- **Objective**: Understand the structure of network protocols.
- **Evidence Analysis**: Commands `ls()` and `ls(IP)` were executed to list supported protocols and the specific field attributes of the Internet Protocol (IP) header.
- **Technical Explanation**: The `ls()` command is crucial for identifying how Scapy represents protocol layers, showing fields such as version, ttl, src, and dst. This understanding is essential for later crafting custom packets.


## Step 3: Traffic Generation

- **Objective**: Create external network traffic to monitor.
- **Evidence Analysis**: I performed a ping command against cisco.com. The terminal confirmed the transmission of four ICMP Echo Requests and the receipt of four corresponding Echo Replies.
- **Technical Explanation**: Generating controlled traffic is a standard practice for verifying network path connectivity and provides a baseline to practice packet sniffing.


## Step 4: Traffic Sniffing

- **Objective**: Capture live packets.
- **Evidence Analysis**: The `sniff()` function was executed within the Scapy shell. The resulting capture summary indicates the interception of UDP and ICMP traffic.
- **Technical Explanation**: The `sniff()` function captures packets passing through the network interface. The output provides a summary of protocol distributions, allowing for quick analysis of the traffic environment.

---

# Security Relevance

Knowledge of Scapy is invaluable for modern security professionals. While used extensively in penetration testing to craft custom packets for vulnerability scanning, it is equally vital for SOC analysts and threat hunters to perform deep packet inspection (DPI) when investigating suspicious network behaviors that standard IDS signatures might miss.

---

# Skills Demonstrated

- **Network Fundamentals:** Understanding IP structure and ICMP mechanics.
- **Packet Analysis:** Intercepting and interpreting live network traffic.
- **Linux Usage:** Leveraging command-line utilities for network orchestration.
- **Technical Documentation:** Translating hands-on lab processes into a professional format.

---

# Key Findings

- Scapy provides an immediate, high-level interface for inspecting complex protocol headers.
- Active sniffing requires concurrent traffic generation to observe real-world data flow.
- The interactive `ls()` utility is the primary tool for learning the syntax of packet construction.

---

# Lessons Learned

This lab demonstrated that network visibility is not just about observing logs, but understanding the raw bytes traversing the wire. Scapy bridges the gap between theoretical networking and actionable security investigation.

---

# Conclusion

The Scapy Network Analysis Lab successfully validated the ability to initialize network environments, inspect protocol architectures, and perform real-time traffic capture. These skills are highly transferable to security roles focusing on incident response and network forensic analysis.

---

# Reference Lab Images

> [!NOTE]
> The below snippets are placed in a descending order as how they were taken during the process. Essentiall, the first image is the closest to the start of the lab.

![](images/scapy_lab_loading_the_scapy_framework.png)
![](images/scapy_lab_ls().png)
![](images/scapy_lab_help_menu.png)
![](images/scapy_lab_ls(IP).png)
![](images/scapy_lab_sniff().png)
![](images/scapy_lab_ping_cisco.png)
![](images/scapy_lab_sniff()_results.png)


