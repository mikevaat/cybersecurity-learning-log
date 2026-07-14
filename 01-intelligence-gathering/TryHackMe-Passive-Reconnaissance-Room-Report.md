# Technical Lab Report: Passive Reconnaissance

**Author:** CypherX  
**Platform:** TryHackMe  
**Room Name:** Passive Reconnaissance

## Executive Summary

This technical report details the methodology and findings from the Passive Reconnaissance room on TryHackMe. The core objective of passive reconnaissance is to gather intelligence about a target infrastructure using publicly available data and open-source intelligence (**OSINT**) tools without generating direct traffic or interacting with the target’s network assets.

By remaining entirely passive, an operative minimizes their digital footprint and avoids detection by defensive technologies such as Intrusion Detection Systems (**IDS**), Intrusion Prevention Systems (**IPS**), or security information and event management (**SIEM**) solutions. This engagement focuses on mapping out the external attack surface of domain properties, discovering subdomains, querying registration data, and inspecting public search engines for internet-connected infrastructure.

- **Objective**
  - The primary objective of this lab is to master the core concepts, methodologies, and tools associated with passive network foot-printing. The target domain evaluated throughout this simulation is [tryhackme.com](https://tryhackme.com) and its associated network infrastructure.

## Skills Covered
- Core **OSINT** Concept Differentiation: Dissecting the boundaries between passive information gathering and active structural network probing.
- Domain Registration Analysis: Querying domain registry endpoints via command-line utilities and standardized web registries.
- **DNS** Infrastructure Mapping: Performing passive **DNS** enumerations to extract zone structures, subdomains, and historical record classifications.
- Threat Intelligence Infrastructure Search: Using internet-wide scanners to index exposed host properties, banner responses, and server configurations without sending target-bound packets.

## Tools Used
- **CLI** Networking Protocols: `whois`, `curl` (via **RDAP** **API**), `nslookup`, `dig`.
- **DNS** Intelligence Platforms: DNSDumpster, crt.sh (Certificate Transparency Log search).
- Internet-Wide Scanner Search Engines: [Shodan.io](https://shodan.io).

## Methodology

Passive reconnaissance operates under a defensive and non-intrusive paradigm. The engagement followed a structured progression:
[Passive Identification] ➔ [Domain Registry Analysis] ➔ [**DNS** Infrastructure Querying] ➔ [Threat Intel Aggregation]

- **Passive Identification:** Classifying the nature of information-gathering tasks.
- **Domain Registry Analysis:** Looking up registration properties using **WHOIS** databases and **RDAP** endpoints.
- **NS Infrastructure Querying:** Resolving infrastructure markers (A, **MX**, **TXT**) records and discovering non-indexed subdomains via alternative data sets (such as **CT Logs** and open maps).
- Threat Intel Aggregator Inspection: Querying distributed device scanners to extract infrastructure metadata, geographic telemetry, and technology stack attributes.

## Executive Summary of Findings
During the passive foot-printing phase of tryhackme.com, multiple actionable data elements were uncovered:
- Infrastructure Anchors: Primary registration assets are managed via Namecheap, Inc., with name resolution architecture delegated to Cloudflare.
- Hidden Subdomains Exposed: Passive tracking of Certificate Transparency logs and historical zone databases revealed critical administrative and staging endpoints including `admin.tryhackme.com`, `auth.tryhackme.com`, and `remote.tryhackme.com`.
- Mail Server Matrix: The target delegates external corporate mail exchange paths completely to Google Workplace pools (`aspmx.l.google.com`).

## Step-by-Step Walkthrough

### Task 2: Passive Versus Active Recon

![](images/passive_recon_thm_room_task_2.png)
![](images/passive_recon_thm_room_task_2_answers.png)

Reconnaissance is the initial stage of any standard attack framework, including the Unified Kill Chain and the classic Cyber Kill Chain. Passive reconnaissance relies strictly on publicly accessible data sources. No packets originate from the examiner's station directly to the target hosts.
A diagnostic check was conducted to distinguish between active and passive interaction vectors:
| Scenario / Action | Interaction Mechanics | Categorization |
|---|---|---|
| Visiting the corporate Facebook page to extract employee directories. | Queries go to Facebook servers; the target's network infrastructure is completely untouched. | Passive (P) |
| Pinging the company web server IP address to determine if ICMP replies are allowed. | Packets directly cross the target's boundary interface, leaving an entry in connection logs. | Active (A) |
| Attempting social engineering on a target IT Administrator at a social event. | Direct physical/verbal communication targeting human assets belonging to the entity. | Active (A) |

### Task 3: WHOIS and RDAP Domain Analysis

![](images/passive_recon_thm_room_task_3.png)
![](images/passive_recon_thm_room_task_3_answers.png)
![](images/passive_recon_thm_room_task_3_curl_query.png)
![](images/passive_recon_thm_room_task_3_whois_query.png)

Domain registration infrastructure relies on the **WHOIS protocol** (TCP port 43), defined in [RFC 3912](https://tools.ietf.org/html/rfc3912). It provides registration profiles for leased domains, specifying registrars, contact data, key expiration milestones, and configured authoritative name servers. To evaluate the registration layout of `tryhackme.com`, the native Linux command-line CLI tool was executed.

## WHOIS Command Output
```bash
whois tryhackme.com
```
**Result:**
```
Domain Name: TRYHACKME.COM
Registry Domain ID: 2282723194_DOMAIN_COM-VRSN
Registrar WHOIS Server: whois.namecheap.com
Registrar URL: [http://www.namecheap.com](http://www.namecheap.com)
Updated Date: 2025-05-11T14:06:02Z
Creation Date: 2018-07-05T19:46:15Z
Registry Expiration Date: 2034-07-05T19:46:15Z
Registrar: NameCheap, Inc.
Registrar IANA ID: 1068
Registrar Abuse Contact Email: abuse@namecheap.com
Registrar Abuse Contact Phone: +1.6613102107
Domain Status: clientTransferProhibited [https://icann.org/epp#clientTransferProhibited](https://icann.org/epp#clientTransferProhibited)
Name Server: KIP.NS.CLOUDFLARE.COM
Name Server: UMA.NS.CLOUDFLARE.COM
```
**Note:**
> **Key Identification Metrics Extracted:**
> - **Registration Metric:** `20180705` (July 5, 2018)
> - **Authoritative Registrar:** `namecheap.com`
> - **Delegated Name Servers:** Cloudflare infrastructure clusters (`KIP.NS.CLOUDFLARE.COM`, `UMA.NS.CLOUDFLARE.COM`)
> - **EPP Status Code:** `clientTransferProhibited` indicates protection against unauthorized domain transfers.
> - **Alternative Query Mechanics:** Registration Data Access Protocol (**RDAP**)
>
Because WHOIS lacks structured output schemas and standardized authentication, the industry is transitioning toward the **Registration Data Access Protocol (RDAP)**. This protocol delivers JSON payloads over standard HTTPS queries.

## RDAP Query Command and Output
```bash
curl -s https://rdap.verisign.com/com/v1/domain/tryhackme.com | jq .
```
**Output Analysis:**
```json
{
  "objectClassName": "domain",
  "handle": "2282723194_DOMAIN_COM-VRSN",
  "ldhName": "TRYHACKME.COM",
  "links": [
    {
      "value": "https://rdap.verisign.com/com/v1/domain/TRYHACKME.COM",
      "rel": "self",
      "href": "https://rdap.verisign.com/com/v1/domain/TRYHACKME.COM",
      "type": "application/rdap+json"
    }
  ],
  "status": [
    "client transfer prohibited"
  ],
  "entities": [
    {
      "objectClassName": "entity",
echo handle" :
```

### Task 4: Standard DNS Investigation via `nslookup` and `dig`

![](images/passive_recon_thm_room_task_4_dig.png)
![](images/passive_recon_thm_room_task_4_dig_flag.png)
![](images/passive_recon_thm_room_task_4_nslookup.png)
![](images/passive_recon_thm_room_task_4_nslookup_mx.png)

To trace operational network destinations without initiating application-layer contact, standard tools were used to query public DNS systems (such as Cloudflare's 1.1.1.1 resolver).

- **1. Checking Address Records (A) via `nslookup`**
```bash
nslookup -type=A tryhackme.com 1.1.1.1
```
**Output:**
```
Server:        1.1.1.1
Address:       1.1.1.1#53

Non-authoritative answer:
Name:    tryhackme.com
Address: 64.239.109.65
Name:    tryhackme.com
Address: 64.239.123.65
```
*The query returns secondary cloud routing mappings pointing to internal target hosting infrastructure networks.*

- **2. Investigating Mail Exchanger Mapping (MX) via `nslookup` and `dig`**
Mail Exchanger records define exactly where inbound messages are routed for users on that domain.
```bash
nslookup -type=MX tryhackme.com
```
**Output:**
```
Server:        10.86.65.155
Address:       10.86.65.155#53

Non-authoritative answer:
tryhackme.com   mail exchanger = 10 alt4.aspmx.l.google.com.
tryhackme.com   mail exchanger = 5 alt1.aspmx.l.google.com.
tryhackme.com   mail exchanger = 10 alt3.aspmx.l.google.com.
tryhackme.com   mail exchanger = 5 alt2.aspmx.l.google.com.
tryhackme.com   mail exchanger = 1 aspmx.l.google.com.
```
To cross-verify, a targeted query was run using `dig`, pointing to an explicit public resolver array:
```bash
dig @1.1.1.1 thmlabs.com TXT
```
**Output:**
```
thmlabs.com.		300 IN TXT "THM{a5b8392988ed36acb0272971e438d78}"
```

<br>

| **Flag Obtained:** THM{a5b8392988ed36acb0272971e438d78}

### Task 5: Advanced Subdomain Investigation via DNSDumpster & crt.sh

![](images/passive_recon_thm_room_task_5.png)
![](images/passive_recon_thm_room_task_5_answer.png)
![](images/passive_recon_thm_room_task_5_crtsh.png)
![](images/passive_recon_thm_room_task_5_dnsdumpster.png)
![](images/passive_recon_thm_room_task_5_dnsdumpster_II.png)
![](images/passive_recon_thm_room_task_5_dnsdumpster_III.png)
![](images/passive_recon_thm_room_task_5_dnsdumpster_IV.png)


Standard lookups only work when the specific host label is already known.
To map out unadvertised subdomains or staging nodes that increase the external attack surface, historical caching tools and Certificate Transparency (CT) data sets were checked.

- **1. DNSDumpster Profiling**
Searching `tryhackme.com` within the DNSDumpster engine uncovered deep structural entries that normal iterative lookups typically miss:

```
[System Locations Mapping]
**Hosting Distributions:**
- CLOUDFLARENET [30 endpoints]
- AMAZON-02 [18 endpoints]
```

Extracted endpoints revealed structural addresses:
- `assets.tryhackme.com` (`65.8.54.71` -> Amazon CloudFront distribution)
- `auth.tryhackme.com` (`104.18.35.233` -> Cloudflare)
- `blog.tryhackme.com` (`104..20..29..66` -> Cloudflare)
- `status-api.vm.tryhackme.com` (`18..200..80..242` -> Amazon AWS deployment routing via Ireland)
- `​admin.tryhackme.com`
- ​`remote.tryhackme.com`

> [!NOTE]
> Key Finding:
> When querying DNSDumpster for historical assets alongside standard `www` and `blog` references, an additional system host node named **remote** was identified.

- **2. Certificate Transparency Log Parsing (`crt.sh`)**
Whenever an organization requests an SSL/TLS certificate from a public Certificate Authority (CA), the certificate is logged in an immutable public database known as a **Certificate Transparency (CT) log**. Attackers can query these logs to discover subdomains the moment a certificate is created, completely out-of-band from any active DNS probing.

A public wildcard match (`%.tryhackme.com`) was executed via `crt.sh`, revealing precise certificate provisioning history:

| Log ID       | Logged At   | Not Before  | Common Name                  | Matching Identity            | Issuer Name          |
|--------------|-------------|-------------|------------------------------|------------------------------|----------------------|
| 2698489802   | 2026-06-08  | 2026-06-08  | tryhackme.com                | *.tryhackme.com              | Sectigo Limited     |
| 2693571053   | 2026-06-06  | 2026-06-06  | careers.tryhackme.com         | careers.tryhackme.com        | Let's Encrypt        |
| 2691448124   | 2026-06-05  | 2026-06-05  | cdn-images.tryhackme.com      | cdn-images.tryhackme.com     | Amazon RSA           |

### Task 6: Platform Exploration via Shodan.io


![](images/passive_recon_thm_room_task_6.png)
![](images/passive_recon_thm_room_task_6_shodan.png)
![](images/passive_recon_thm_room_task_6_shodan_US.png)
![](images/passive_recon_thm_room_task_6_shodan_apache_port.png)
![](images/passive_recon_thm_room_task_6_shodan_filtering.png)


Shodan indexes internet-connected hardware appliances, routers, IoT setups, and servers by continuously scanning public IP ranges and parsing service banners.

- **1. Host Target Inspection (tryhackme.com)**
A dedicated lookup on Shodan returned information on external target profiles and isolated public systems.
- **Search Parameter:** tryhackme.com
- **Total Passive Profiles Tracked:** 8

One specific record highlights a researcher workstation environment:
- **Host:** Jacob Riggs - Security Researcher
- **IP Address:** 54.37.18.93
- **Location:** United Kingdom, London
- **Hosting Provider:** OVH Ltd
- **Active Certificate Instance:** Issued by Let's Encrypt to www.jacobriggs.io
- **Headers Captured:** `HTTP/1.1 200 OK` | Server: Apache | X-Frame-Options: SAMEORIGIN

- **2. Investigating Specific Service Mappings across Shodan Data Streams**
To gather general intelligence on specific application stacks, global queries were executed to inspect product footprints:

| **Query: `product:apache`**
- **Total Global Results Identified:** 11,024,285
- **Top Contributing Regions:**
  - United States (3,320,930)
  - Japan (1,161,233)
  - Germany (1,144,720)
- Exposes running application fingerprints like Apache Tomcat/7.0.50 on port 8080.

| **Query: `product:nginx`**
Global telemetry tracks multi-million host setups generating `400 Bad Request` or specific raw protocol responses back to the scanning arrays.

| **Target-Specific Verification:**
target: tryhackme.com port:443 country:US
Evaluated direct configurations over narrow geographic parameters, isolating specific web servers running customized instances of nginx/1.27.1.

| **Reconnaissance Findings**
- **Infrastructure Footprint:** The primary web tier relies on Cloudflare and AWS, leveraging Anycast routing arrays to block direct backend server lookups.
- **Administrative Vector Points:** Endpoints such as [admin.tryhackme.com](https://admin.tryhackme.com) and [remote.tryhackme.com](https://remote.tryhackme.com) serve as primary remote management interfaces for internal administration.
- **Technology Profiling:** Public platform components expose direct dependencies on Nginx routing distributions and backend application services hosted within specific Western European regions.

| **OSINT Techniques Used:**
- Passive Registration Scrapes: Extracted operational context from domain data without interacting with target assets.
- Public Out-of-Band Log Lookups: Parsed public Certificate Transparency record trees to find subdomains that do not appear in conventional zone transfers.
- Passive Metadata Scraping: Used third-party scanners (Shodan) to pull server software configurations, patch levels, and certificate states from cache data.

---

# Key Takeaways:
- Pervasiveness of Digital Footprints: Even when an organization uses proxy infrastructure like Cloudflare to hide its backend servers, third-party caches (like Shodan) and public infrastructure logs (like CT logs) often leave a visible history of the real assets.
- Value of Passive Analysis: This lab demonstrates how an operator can map out a target domain structure, find corporate mail paths, and discover administrative interfaces without sending a single packet to the target network.
- Defense-in-Depth and OpSec: To reduce exposure to passive reconnaissance, organizations should monitor CT logs for unauthorized certificate creation and audit their public-facing profiles on internet-wide scanners like Shodan.

# Conclusion:
This passive reconnaissance phase successfully mapped out the infrastructure profile of the target domain (`tryhackme.com`) without alerting defensive monitoring teams. The technical report details the target's dependencies on cloud providers, uncovers administrative endpoints, and demonstrates the power of passive information gathering. This intelligence will serve as the foundation for subsequent active testing phases.
