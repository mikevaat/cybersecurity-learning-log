# Exploration & Simulation of the Social-Engineer Toolkit (SET)

## Objective

To conduct a comprehensive structural analysis of the Social-Engineer Toolkit (SET) framework, explore its automated attack vectors, and execute a controlled credential harvesting simulation to understand the mechanics of phishing campaigns.

| Category | Details |
|----------|---------|
| **Environment** | Kali Linux (Isolated Sandbox) |
| **Framework** | Social-Engineer Toolkit (SET)

---

# 1. Initialization and Environment Validation

The lab commenced by launching the SET framework via the terminal. The initial boot sequence displayed the TrustedSec splash screen and loaded the core framework components, successfully identifying as version **8.0.3**

![](images/set_boot.png)

During the boot and update check process, the system threw the following error:

```text
urllib.error.URLError: <urlopen error [Errno -3] Temporary failure in name resolution>
```

![](images/set_boot_options.png)


This error occurred because the lab environment is deliberately isolated from the open internet to prevent unintended external transmissions, confirming the safety and containment of the sandbox prior to exploring the modules.

Navigating to the primary interface, **Option 1 – Social-Engineering Attacks** was selected, unlocking the core toolkit arsenal.

---

# 2. Comprehensive Module Exploration

To thoroughly understand the toolkit's capabilities, i clicked the first option that was displayed, the *social engineering attack*.


![](images/set_boot_options.png)


This then led me to a list of options. I explored each of the primary attack vectors (**Options 1–10**) systematically.

![](images/set_social_eng.png)

<br>

## Vector 1: Spear-Phishing Attack Vectors

![](images/set_social_eng_1.png)

This module automates the creation and delivery of malicious emails. It allows an operator to attach file-format payloads (like compromised PDFs or Word documents) and spoof sender addresses to target specific individuals or execute mass campaigns.

---

## Vector 2: Website Attack Vectors

![](images/set_social_eng_2.png)

The web attack suite is designed to compromise victims via web browsers. It includes methods like Java Applet spoofing, Metasploit browser exploits, TabNabbing (refreshing inactive tabs to malicious pages), and Credential Harvesting (cloning sites to intercept login data).

---

## Vector 3: Infectious Media Generator

![](images/set_social_eng_3.png)

This vector focuses on physical social engineering (e.g., dropping USB drives in a parking lot). It automates the creation of an `autorun.inf` file paired with a Metasploit payload (either a file-format exploit or a standard executable) designed to trigger when the media is inserted into a target machine.

---

## Vector 4: Create a Payload and Listener

![](images/set_social_eng_4.png)

A streamlined utility to quickly generate standard Metasploit payloads (such as Windows Reverse TCP shells or Meterpreter sessions) and automatically configure the corresponding listener on the attacker's machine to catch the incoming connection.

---

## Vector 5: Mass Mailer Attack

![](images/set_social_eng_5.png)


Distinct from targeted spear-phishing, this module is built for volume. It allows the operator to either target a single address or import an extensive list of targets to blast out social engineering templates en masse.

---

## Vector 6: Arduino-Based Attack Vector

![](images/set_social_eng_6.png)


This module programs physical HID (Human Interface Device) hardware, specifically Teensy USB devices. Because computers inherently trust keyboards, this vector bypasses autorun restrictions by simulating rapid, automated keystrokes to deploy payloads (like PowerShell downloaders) the moment the USB is plugged in.

---

## Vector 7: Wireless Access Point Attack Vector


This module automates the deployment of a rogue Wi-Fi access point (an **"Evil Twin"**). It is used to intercept wireless traffic, capture handshakes, or utilize DNS spoofing to redirect connected victims to malicious captive portals.

---

## Vector 8: QRCode Generator Attack Vector


A modern social engineering vector that generates malicious QR codes. When scanned by a mobile device, the QR code redirects the victim to a cloned credential harvesting site or initiates a direct payload download.

---

## Vector 9: PowerShell Attack Vectors

This module leverages Windows PowerShell to deploy fileless malware. By executing shellcode directly in the system's memory, this vector is specifically designed to evade traditional, disk-based antivirus detection mechanisms.

---

## Vector 10: Third Party Modules

![](images/set_social_eng_10.png)


This section houses community-contributed modules, such as **RATTE (Remote Administration Tool Tommy Edition)** and **Google Analytics attack scripts**, showcasing the extensible, open-source nature of the SET framework.

---

# 3. Practical Simulation: Credential Harvesting via Site Cloner

Following the architectural exploration, a practical demonstration was executed focusing on the **Website Attack Vectors**.

### Selecting the Attack Path


![](images/set_social_eng.png)

From the main menu, **Option 2 (Website Attack Vectors)** was selected.

### Choosing the Method

The menu presented various web-based exploits. **Option 3 (Credential Harvester Attack Method)** was chosen, which intercepts POST requests to capture plaintext usernames and passwords.

![](images/set_cloning_option_3.png)


### Deployment Configuration

![](images/set_cloning_example_dot_com.png)


**Option 2 (Site Cloner)** was selected to dynamically replicate a target dummy website.

### Network Setup & Target Designation

SET prompted for the **POST back IP address** (where the harvested credentials should be sent). The local loopback address:

```text
127.0.0.1
```

was utilized to keep the traffic strictly internal to the Kali machine.

When prompted for the URL to clone, the dummy domain:

```text
cloned-example.com
```

was supplied to complete the simulation setup.

At this stage, SET successfully spooled up an Apache listener on **port 80**, ready to serve the cloned page and intercept any submitted form data.

---

# 4. Conclusion & Defensive Mitigation

This exploration highlights the severe efficiency with which threat actors can automate complex social engineering campaigns. By removing the technical overhead of manual HTML cloning and listener configuration, tools like SET make phishing highly accessible.

## Defensive Recommendations

- **FIDO2 / Hardware Security Keys**  
  Implementing robust Multi-Factor Authentication renders harvested passwords functionally useless.

- **Proactive Domain Monitoring**  
  Utilizing threat intelligence to monitor for newly registered, typosquatted domains before they are weaponized.

- **User Behavior Analytics (UBA)**  
  Training systems to flag impossible travel or anomalous login attempts stemming from unexpected IP addresses, even if the credentials supplied are technically correct.
