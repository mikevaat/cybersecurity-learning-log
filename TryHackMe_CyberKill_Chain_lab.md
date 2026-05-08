# Overview

This documentation outlines the core concepts explored in the TryHackMe Cyber Kill Chain room. The lab provides a high-level overview of Lockheed Martin's standard framework, which models the phased approach adversaries use to plan, execute, and maintain cyber attacks. Understanding this methodology is critical from a defensive standpoint, as breaking the chain at any single phase can effectively neutralize a threat.

While the framework consists of seven distinct phases (Reconnaissance, Weaponization, Delivery, Exploitation, Installation, Command and Control, and Actions on Objectives), this write-up specifically highlights the key technical takeaways from the Reconnaissance and Command & Control (C2) stages.

---

| **Reconnaissance & OSINT**

Before any active exploitation occurs, adversaries invest significant time in intelligence gathering. This phase is heavily reliant on Open-Source Intelligence (OSINT) to build a comprehensive profile of the target infrastructure and personnel.

<br>

![Recon_snip](images/THM-cyber-kill-chain-lab-recon.png)

<br>

》**Key Concepts Explored:**
- **Email Harvesting:** The strategic collection of valid corporate email addresses using public, free, or paid services. This is typically the foundational step for staging targeted social engineering or spear-phishing campaigns designed to steal credentials.
- **Intelligence Gathering Tools:**
  - *OSINT Framework:* A centralized, web-based interface that categorizes and links to hundreds of common OSINT tools and resources.
  - *theHarvester:* A highly versatile tool used to aggregate emails, names, subdomains, IPs, and URLs from various public data sources.
  - *Hunter.io:* A specialized email-hunting service used to verify and obtain contact information associated with a specific target domain.

---

| **Command and Control (C2)**

Following successful exploitation and the installation of a persistent backdoor, the attacker must establish a communication line with the compromised endpoint. This is achieved through a Command and Control (C2) channel, allowing the adversary to remotely manipulate the victim machine.

<br>

![C2_snip](images/THM-cyber-kill-chain-lab-c2.png)

<br>

》**C2 Beaconing and Evasion:**
- Malware on the infected host will "beacon" out, making consistent, regular requests to the attacker's external C2 server. Because legacy C2 methods like IRC (Internet Relay Chat) are easily flagged by modern security appliances, adversaries now utilize protocols that blend in with normal network traffic.
- **HTTP/HTTPS Beaconing:** Attackers frequently use standard web ports (Port 80 for HTTP, Port 443 for HTTPS) for C2 traffic. By doing so, malicious beaconing blends into regular, legitimate web traffic, making it difficult for standard firewalls to detect anomalies.
- **DNS Tunneling:** A highly stealthy C2 method where the infected machine makes constant Domain Name Server (DNS) requests to an attacker-controlled DNS server. Because DNS traffic is essential for network operation and rarely blocked outright, attackers encapsulate their malicious commands within these DNS queries and responses to bypass perimeter defenses.

<br><br>

## Defensive Takeaways
The primary value of analyzing the Cyber Kill Chain lies in defense-in-depth. By understanding how an attacker transitions from passive OSINT gathering (Reconnaissance) to establishing a covert foothold (DNS Tunneling for C2), defenders can implement specific detection mechanisms—such as monitoring for abnormal DNS query volumes or locking down public-facing employee data—at each distinct phase to disrupt the attack lifecycle early on.
