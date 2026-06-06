# Domain Intelligence & DNS Record Analysis

## Executive Summary
This project explores the mechanics of the Domain Name System (DNS) using command-line diagnostics. The goal was to move beyond simple connectivity checks and perform deep-packet reconnaissance of domain configurations. By querying specific record types (such as MX for mail routing and NS for authority) I've documented how to map out a domain’s digital footprint and verify its infrastructure integrity.

## Technical Workflow

### 1. Initial Name Resolution & IP Discovery
The first objective was to establish baseline connectivity and resolve hostnames to their respective IP addresses. Using nslookup, I targeted several high-traffic domains to observe how global servers distribute traffic across different IP ranges.

![ip_discovery](images/dns-lookups-lab-nslookup-cisco-lookup.png)


Reflecting on the initial queries: The transition from IPv4 to IPv6 is clearly visible in the AAAA records returned for major web platforms.

### 2. Deep Dive: Mail Exchange (MX) Infrastructure
To understand how a domain handles communication, I shifted the query scope to Mail Exchange records. By utilizing the set type=mx command within the interactive nslookup shell, I was able to identify the specific mail servers responsible for receiving email on behalf of the domain.

![mx_ifrastructure](images/dns-lookups-lab-nslookup-google-lookup.png)


Key Insight: Examining MX records reveals the mail gateway's priority levels, which are essential for understanding a domain’s redundancy and failover strategy.

### 3. Authoritative Reconnaissance (NS Records)
Identifying who "owns" the truth for a domain is critical. By querying NS (Name Server) records, I identified the authoritative servers for the target domains. This step is vital for verifying that a domain is pointed toward the correct hosting provider and isn't susceptible to DNS hijacking or unauthorized redirection.

### 4. Validating Identity with Reverse Lookups
A professional network analysis isn't complete without verifying the reverse path. I performed reverse DNS lookups on the previously discovered IP addresses. This process ensures that the IP address is legitimately associated with the domain name provided, a common practice in security auditing to identify spoofing or misconfigurations.

![rDNS_lookups](images/rdns-lookups-lab-dig-x.png)


The reverse lookup confirms the Pointer (PTR) records, successfully mapping the 127.x.x.x or public IP back to its host identity.

### 5. Advanced Querying with dig
While nslookup provides a clean interface, I utilized dig (Domain Information Groper) for more granular data. dig is the preferred tool for many Linux professionals because it reveals the "Answer," "Authority," and "Additional" sections of the DNS response, along with the precise query time in milliseconds.

![dig_query](images/dns-lookups-lab-dig-cisco.png)


Analysis: The dig output provides a comprehensive look at the TTL (Time to Live) values, showing how long these records are cached by intermediate resolvers.

## Conclusion
Through this analysis, I demonstrated proficiency in navigating the DNS hierarchy. By manipulating query types and interpreting the raw output of network utilities, I successfully mapped out the mail, name server, and IP infrastructure for several live domains. This methodology forms the backbone of any robust network troubleshooting or security assessment.

## Tools & Technologies Used
Primary Utilities: nslookup, dig  
Protocol Focus: DNS (UDP Port 53)  
Environment: Command Line Interface (CLI)  
Record Types Analyzed: A, AAAA, MX, NS, PTR
