# TryHackMe Penetration Test Walkthrough: Pickle Rick
​**Security Researcher:** CypherX
**Target IP:** 10.130.177.2
**Objective:** Perform a full-scope penetration test on a vulnerable web application, bypass input validation mechanisms, escalate privileges to root, and exfiltrate three hidden flags (ingredients).

---

# ​Executive Summary
​This documentation details the complete attack chain executed against the **Pickle Rick** lab environment. The methodology follows a standard black-box penetration testing approach, beginning with network reconnaissance, moving into web application enumeration, and culminating in remote command execution (**RCE**). Due to poor input validation and severe sudo misconfigurations, the environment was fully compromised, allowing for a stable reverse shell connection and total administrative control.

<br>

## ​Phase 1: Environment Provisioning & Network Connectivity
​Before initiating any offensive operations, a secure tunnel into the target network was established to ensure reliable routing between the attacker machine and the victim server

- **1. VPN Initialization:**

![](images/pr_ovpn_config_dl.png)

![](images/pr_ovpn_installation.png)

The environment connection was initiated by downloading the provided OpenVPN configuration file. The package was installed and configured using `sudo apt install openvpn`.

![](images/pr_lab_initialization_seq.png)

- Verifying the Tunnel:
Upon running the client, the terminal successfully returned the Initialization Sequence Completed output. This was cross-verified on the TryHackMe access dashboard, confirming the routing was active.

![](imagespr_lab_start_lab_machine.png)

- Target Spooling & Reachability:
After deploying the **Pickle Rick v2** lab instance, the target was assigned the **IP** address 10.130.177.2. To confirm the host was alive and that **ICMP** requests were not being dropped by a firewall, a ping test was executed:

```
- ping -c 4 10.130.177.2
```

![](images/pr_lab_ping_test.png)

All four packets were successfully transmitted and received, validating network reachability.

## Phase 2: Network Reconnaissance
​With a stable connection, the next step was to identify the attack surface by mapping out open ports and running services.
Nmap Port Scan:
A comprehensive nmap scan was executed to enumerate open ports, detect service versions, and run default vulnerability scripts:
```
- nmap -sC -sV -T4 10.130.177.2
```
![](images/pr_lab_nmap_scan.png)

- sC: Executes default Nmap scripts for basic vulnerability discovery.
​- sV: Probes open ports to determine service and version info.
​- T4: Increases execution speed for a faster sweep.

**Results:** The scan returned two active services
​- Port 22 (**SSH**): Secure Shell, typically secure unless weak credentials are in play.
- ​Port 80 (**HTTP**): An active Apache web server. This presents a much larger attack surface and became the primary vector for exploitation.

## Phase 3: Web Application Enumeration

![](images/pr_lab_web_enum.png)

With port 80 confirmed open, the next logical step was to interact directly with the web server. Navigating to: `http://10.130.177.2` in the browser presented a landing page featuring a bold "Help Morty!" plea. Visual inspection of the page yielded no immediate interaction points or hyperlinks, requiring a deeper structural analysis.

- **Source Code Inspection**
![](images/pr_lab_page_src_usrname_found.png)

As a standard first step in any web app penetration test, I inspected the raw HTML using the CTRL+U shortcut. Developers frequently leave remnants of testing, debugging notes, or legacy code in these files. Scrolling to the very bottom of the page source, I discovered a hidden HTML comment left by Rick that contained a distinct string: R1ckRul3s. Recognizing this as a potential username, I immediately logged it into a Mousepad text file named pr_lab_nessecities.

- **Hunting in robots.txt**

![](images/pr_lab_web_enum_robotstxt.png)

Continuing with standard enumeration methodology, my next go-to was checking the robots.txt file. Web applications utilize this file to tell search engine crawlers which directories to ignore, which often inadvertently points attackers directly to sensitive areas or leaves unintended breadcrumbs.
​Navigating to [http://10.130.177.2/robots.txt] did not reveal hidden directories as expected, but instead displayed a single, scribbled-looking string: *Wubbalubbadubdub*. I documented this in my Mousepad notes alongside the username as: *possible password : Wubbalubbadubdub.*


​- **Directory Brute-Forcing**

![](images/pr_lab_gobuster_command.png)

![](images/pr_lab_gobuster_finished.png)


​To map the hidden architecture of the web application, a directory brute-force attack was launched using Gobuster:
```bash
gobuster dir -u [http://10.130.177.2](http://10.130.177.2) -w /usr/share/wordlists/dirb/common.txt -x php,txt,html
```
Methodology: The -x flag was explicitly used to append file extensions (.php, .txt, .html) to every word in the dictionary, ensuring hidden files, not just directories, were discovered.
​Results: The scan successfully enumerated /assets/ and, more critically, an administrative login portal at /login.php.

<br>
With /login.php clearly standing out as the primary target of interest, the focus shifted to testing the harvested credentials and gaining initial access.

![](images/pr_lab_loginphp_page.png)



## Phase 4: Initial Access

​Navigating to login.php, the credentials harvested during the enumeration phase were tested:
*​Username: R1ckRul3s*<br>
*​Password: Wubbalubbadubdub*
​Authentication was successful. The application redirected to a **Command Panel** interface. This portal essentially functioned as a stateless web shell, allowing direct interaction with the underlying Linux operating system.
​
![](images/pr_lab_loggedin_cmd_panel.png)

- **Exploiting the First Ingredient**

Discovery: To understand the current working directory environment, the command ls -la was executed. The output revealed a suspicious text file named Sup3rS3cretPickl3Ingred.txt.

![](images/pr_lab_cmd_panel_sus_file.png)

The Hurdle: Attempting to read the file using standard syntax (cat Sup3rS3cretPickl3Ingred.txt) resulted in an application-level error. The developer had implemented basic input validation, blacklisting the cat command to prevent arbitrary file reads.


![](images/pr_lab_cmd_panel_cat_cmd_failed.png)


The Bypass: Blacklists are inherently flawed because they rely on blocking known bad behavior rather than permitting known good behavior. To circumvent the filter, the less command was utilized as a substitute:
```
- less Sup3rS3cretPickl3Ingred.txt
```

![](images/pr_lab_cmd_panel_less_bypass.png)


This successfully bypassed the restriction, revealing the first flag: **mr. meeseek hair.**

![](images/pr_lab_thm_ans_1.png)

## Phase 5: Lateral Enumeration

![](images/pr_lab_cmd_panel_cluetxt.png)

![](images/pr_lab_cmd_panel_cluetxt_less.png)


​Returning to the ls -la output, another file named clue.txt was identified. Reading it with less provided the hint to **look around the file system.**
​Because the web shell was stateless (meaning it did not retain environment variables or directory changes between commands), attempting to cd into other directories yielded no results.

![](images/pr_lab_cd_home_nothing.png)


### ​5.1 Exploiting the Second Ingredient

- **Absolute Path Mapping:** To view the user directories without changing directories, absolute paths were utilized:
```bash
- ls -la /home
```
![](images/pr_lab_lsla_home.png)

This command revealed a system user named rick.

- **Deep Dive:** Drilling further into the user's directory (ls -la /home/rick) uncovered a file awkwardly named second ingredients.

![](images/pr_lab_lsla_home_rick.png)

- **The Hurdle:** Attempting to read the file with less /home/rick/second ingredients threw a pathing error. Linux bash interprets spaces as separators for distinct arguments, meaning the system was looking for a file named **second** and a file named **ingredients**.

![](images/pr_lab_less_home_rick_2nd_ingredients_pathing_error.png)

- **The Bypass:** To force the terminal to read the string as a single cohesive file path, it was enclosed in quotation marks:
```
- less "/home/rick/second ingredients"
```

![](images/pr_lab_2nd_ingredient_found.png)

The execution was successful, extracting the second flag: 1 jerry tear, which was subsequently submitted.

![](images/pr_lab_thm_ans_2.png)

## Phase 6: Privilege Escalation
​With two ingredients secured, the final objective was obtaining root-level access to extract the third flag.

- **Permissions Audit:** The first step in any Linux privilege escalation methodology is checking the sudo privileges of the current user account (www-data).
```
- sudo -l
```

![](images/pr_lab_sudo_l_cmd.png)


- **The Vulnerability:** The output revealed a catastrophic security misconfiguration: (**ALL**) **NOPASSWD**: **ALL**. This meant the www-data service account was authorized to execute any command as the root user without being prompted for a password.

- **Root Enumeration:** Leveraging this flaw, the root directory was enumerated:
```
- sudo ls -la /root
```

![](images/pr_lab_lsla_root.png)

This exposed the final target file: 3rd.txt.

- **The Bypass:** A standard read attempt (less /root/3rd.txt) would result in a permission denied error since the file is owned by root. By prepending the execution with sudo, the restrictions were entirely bypassed:

```
- sudo less /root/3rd.txt
```

![](images/pr_lab_less_3rdtxt_failed.png)

![](images/pr_lab_sudo_less_3rdtxt_confirmed_and_3rd_ingredient_found.png)


This successfully dumped the final flag: *fleeb juice.*

![](images/pr_lab_thm_ans_3.png)

![](images/pr_lab_room_completed.png)



## Phase 7: Going the Extra Mile: Establishing a Reverse Shell

​While the web panel provided **RCE**, a web shell is a cumbersome, stateless, and noisy way to maintain access during a penetration test. To solidify the compromise and demonstrate professional post-exploitation mechanics, a fully interactive reverse shell was deployed.

- **Payload Generation:** A standard Perl reverse shell payload was drafted.

![](images/pr_extra_cmd_payload.png)


- **Listener Initialization:** On the attacking machine (my machine), a Netcat listener was established to catch the incoming **TCP** connection on port 4444:
```
- nc -lvnp 4444
```

![](images/pr_extra_nc_listener.png)



- **Payload Execution:** The Perl one-liner was injected directly into the vulnerable web Command Panel and executed.

![](images/pr_extra_cmd_payload_in_browser.png)


- **Verification:** The Netcat listener instantly caught the callback. A fully interactive shell was achieved. Access was verified by executing whoami (returning www-data) and ls -la to confirm directory interaction.

![](images/pr_extra_nc_listener_established_lsla.png)


---


# Conclusion
​The Pickle Rick machine serves as a prime example of how chaining minor vulnerabilities leads to total system compromise. The attack relied on identifying leftover developer comments for initial credential harvesting, utilizing directory brute-forcing to find administrative panels, and leveraging logic flaws to bypass command blacklisting. Ultimately, the fatal flaw was the (**ALL**) **NOPASSWD**: **ALL** sudo misconfiguration, allowing trivial vertical privilege escalation.
​All flags were successfully extracted and validated by the TryHackMe platform, resulting in a 100% room completion status.
