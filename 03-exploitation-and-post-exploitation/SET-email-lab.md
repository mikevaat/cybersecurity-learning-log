# Email Phishing Simulation Lab: Social-Engineer Toolkit (**SET**) Analysis in Kali Linux

**Environment:** Isolated Kali Linux Virtual Machine (VirtualBox)<br>
**Framework:** Social-Engineer Toolkit (**SET**)<br>
**Supporting Tools:** Metasploit Framework<br>

- --

- Disclaimer

This lab was conducted strictly within an isolated and authorized sandbox environment for cybersecurity education and research purposes.

All domains, email addresses, **IP** addresses, credentials, and simulated targets used throughout this exercise are fictional and do not represent real individuals, organizations, or infrastructure.

No real phishing emails were distributed, no external systems were targeted, and no unauthorized access attempts were performed.

The purpose of this documentation is to understand phishing attack workflows from a penetration testing and defensive security perspective, allowing security professionals to better identify weaknesses and implement appropriate countermeasures.

- --

- Lab Environment

### The testing environment consisted of

- Kali Linux VM running inside VirtualBox
- Isolated networking configuration
- No external recipients or production systems involved

The isolated environment ensured that all testing activity remained contained and prevented accidental interaction with real infrastructure.

- --

- Prerequisites

### The following components were required

- Kali Linux
- Social-Engineer Toolkit (SET)
- Metasploit Framework
- Root privileges
- Basic understanding of penetration testing concepts
- Isolated virtual lab environment

- --

- Objective

The objective of this laboratory exercise was to analyze the workflow of a phishing simulation using the Social-Engineer Toolkit (**SET**) within a controlled Kali Linux environment.

The exercise focused on understanding how social engineering frameworks automate different stages of a phishing campaign, including:

- Payload generation concepts
- Document-based attack vectors
- Social engineering techniques
- Email template simulation
- Listener configuration
- Defensive considerations against phishing-based threats

The goal was not simply to execute a tool, but to understand the security implications behind each stage and how attackers abuse trust, urgency, and human behavior to increase the likelihood of compromise.

---

**Lab Overview**

The Social-Engineer Toolkit is an open-source penetration testing framework designed to simulate social engineering attacks.

During this exercise, **SET** was examined through a phishing simulation workflow involving a document-based payload scenario.

### The assessment focused on understanding

- How phishing campaigns are structured
- How attackers attempt to disguise malicious content
- How command-and-control communication is established
- How defenders can detect and mitigate these techniques

- --

### Phase 1: Launching **SET**

The Social-Engineer Toolkit was launched from the Kali Linux terminal:

- setoolkit

After initialization, **SET** presented a menu containing several social engineering modules.

The phishing-related options were selected to begin analyzing the workflow of a simulated email-based attack.

- --


### Phase 2: Payload and Document Simulation


![](images/set_email_attack.png)


**SET** provides several payload delivery methods designed to demonstrate common attack patterns.

For this laboratory exercise, a **PDF**-based attack scenario was selected to examine how malicious documents are commonly used as phishing attachments.

The selected scenario represented an older document exploitation technique involving vulnerable **PDF** readers.

Although the vulnerability used in this simulation is outdated, the concept remains relevant because attackers continue to rely heavily on:

- Malicious attachments
- Weaponized documents
- User trust exploitation
- Social engineering techniques

- --

### Phase 3: Payload Configuration 

![](images/set_email_attack_payload.png)

A reverse connection payload simulation was selected to demonstrate how compromised systems can communicate back to an attacker-controlled listener.

### The configuration involved

- Listener address configuration
- Communication port selection
- Payload generation process

In real-world environments, this stage represents the point where attackers attempt to establish command-and-control (C2) communication after successful exploitation.

### Modern defensive solutions monitor these behaviors through

- Endpoint Detection and Response (EDR)
- Network monitoring
- Application controls
- Threat intelligence systems

- --

### Phase 4: File Naming and Social Engineering Analysis

![](images/set_email_attack_payload_rename.png)

One important observation from this exercise was the role of psychological manipulation in phishing campaigns.

### The generated document was renamed to

- salaryincrease.pdf

This demonstrates how attackers frequently use believable file names to increase user interaction.

### Common social engineering themes include

- Financial rewards
- Urgent requests
- Corporate notifications
- Security alerts
- Internal documents

The technical exploit alone is rarely enough. Successful phishing campaigns often depend heavily on human decision-making.

- --

### Phase 5: Email Simulation Workflow

![](images/set_email_attack_template.png)

![](images/set_email_attack_template_2.png)


**SET** provides options for simulating email-based delivery methods.

### The exercise examined

- Single recipient simulation
- Email template selection
- Sender information configuration
- Message personalization concepts

A predefined template was selected to understand how attackers attempt to create realistic-looking communication.

The simulated recipient information used during testing was fictional.

No external email communication occurred.

- --

### Phase 6: Listener Concept

![](images/set_email_attack_listener.png)

The final stage involved analyzing the concept of a Metasploit listener.

In a real penetration test, a listener waits for communication from an authorized test payload during an assessment.

### This stage demonstrates the importance of understanding

- Network connections
- Reverse communication models
- Command-and-control infrastructure
- Detection opportunities

From a defensive perspective, suspicious outbound connections are often valuable indicators during incident response investigations.

- --

Technical Observations

During this lab exercise, the following security concepts were demonstrated:

## 1. Human Risk Is a Major Security Factor

Even strong technical defenses can be weakened when users interact with malicious content.

Security awareness training remains one of the most effective methods for reducing phishing risk.

- --

## 2. Social Engineering Relies on Trust Manipulation

Attackers frequently combine technical methods with psychological techniques.

### Examples include

- Creating urgency
- Impersonating trusted individuals
- Using attractive file names
- Exploiting workplace scenarios

- --

## 3. Legacy Vulnerabilities Remain Valuable Learning Tools

Although many older vulnerabilities have been patched, studying them helps security professionals understand:

- Exploitation concepts
- Vulnerability impact
- Patch management importance
- Defensive controls

- --

Defensive Mitigations

### Organizations can reduce phishing-related risks through

### Email Security Controls

- Advanced spam filtering
- Attachment scanning
- URL reputation checking
- Domain protection mechanisms

Endpoint Protection

- Application allowlisting
- EDR monitoring
- Malware detection
- Suspicious process analysis

User Awareness

- Phishing simulations
- Security training
- Reporting procedures
- Safe browsing practices

Vulnerability Management

- Regular patching
- Software updates
- Removing outdated applications
- Security assessments

- --

Tools Used

Tool| Purpose
Social-Engineer Toolkit (**SET**)| Social engineering simulation framework
Metasploit Framework| Security testing and handler framework
Kali Linux| Penetration testing operating system
VirtualBox| Isolated virtualization environment

- --

Key Takeaways

This laboratory exercise provided practical insight into how phishing campaigns are structured and why social engineering remains a significant cybersecurity threat.

### The main lessons learned include

- Security attacks often combine technical weaknesses with human factors.
- Understanding attacker workflows improves defensive capability.
- Controlled simulations are valuable for cybersecurity education.
- Proper authorization and ethical boundaries are essential when performing security testing.

- --

## Conclusion

The **SET** phishing simulation provided a practical overview of how social engineering frameworks are used during penetration testing engagements.

Rather than focusing only on the technical execution, this exercise highlighted the relationship between attacker methodology, user behavior, and defensive security controls.

Understanding these techniques from a controlled environment allows security professionals to build stronger detection strategies, improve awareness programs, and better protect organizations against real-world phishing campaigns.

Responsible security testing, proper authorization, and ethical practice remain fundamental principles of cybersecurity.
