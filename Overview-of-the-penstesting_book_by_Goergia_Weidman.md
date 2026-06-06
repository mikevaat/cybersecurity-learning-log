# Penetration Testing Lab Notes
## Based on "Penetration Testing" by Georgia Weidman

This repository contains documentation, script breakdowns, and methodology notes from my progress through the hands-on labs in Georgia Weidman's *Penetration Testing*.

---

## 1. Network Reconnaissance: Bash Ping Sweeper
**Context:** Automation of host discovery within a target subnet. This script utilizes a simple `for` loop to iterate through an IP range and filter for active hosts.

### Script Logic & Optimization
To clean up the output and return only the IP addresses of live hosts, we use a combination of `grep`, `cut`, and `sed`.

- **`grep "64 bytes"`**: Filters for successful ICMP echo replies.
- **`cut -d " " -f4`**: Isolates the fourth field (the IP address).
- **`sed 's/.$//'`**: Removes the trailing colon from the IP address provided by the `ping` output.

> ![Bash_Ping_Sweeper_Snip](images/pentest-book-progress-georgia-wiedman-bash-scipting-sed.png)


---

## 2. Man-in-the-Middle (MITM): DNS Spoofing
**Context:** After successfully poisoning the ARP cache, we use `dnsspoof` to intercept DNS queries and redirect the victim to a malicious IP (typically our Kali machine).

### Execution Steps
1. Ensure `arpspoof` is running between the target and the gateway.
2. Configure a `hosts.txt` file to map the target domain (e.g., `www.gmail.com`) to the attacker's IP.
3. Run the spoofing tool:
```bash
dnsspoof -i eth0 -f hosts.txt
```

> ![DNS_Spoofing_Snip](images/pentest-book-progress-dns-spoofing.png)

---


## 3. Client-Side Attacks: The "Aurora" Exploit & Session Persistence
**Context:** Client-side exploitation often relies on a user visiting a malicious URL. A common challenge is "session stability"—if the user closes their browser, the shell dies.
**Key Mechanism:** Process Migration
To ensure the connection survives a browser crash or closure, we use the AutoRunScript parameter in Metasploit to automatically migrate the Meterpreter process to a more stable background process (like explorer.exe or svchost.exe) immediately upon exploitation.
```msfconsole
auto_run_script migrate -f

```


> ![Aurora_Exploit](images/metasploit-autorun-meterpreter-scipts-migrate.png)

​
---


## 4. Social Engineering: Malicious Java Applets

**Context:** Modern security relies heavily on user judgment. By using a signed Java applet, we bypass technical vulnerabilities by simply asking the user for permission to execute code.

**Metasploit Module:**
exploit/multi/browser/java_signed_applet
This attack is highly effective because many users are conditioned to click "Run" or "Allow" on security prompts if the context of the website seems legitimate.



> ![Java_Applets_Snip](images/pentest-book-metasploit-java-applet-attack.png)



---


## 5. Advanced Browser Exploitation: Browser Autopwn
**Context:** Instead of manually testing different exploits against a target browser, the browser_autopwn auxiliary module automates the fingerprinting process.
**Workflow:**
the module starts a web server; when a victim connects, it detects browser version, OS, plugins; then serves compatible exploits from Metasploit's database.
​


> ![Browser_Autopwn_Snip](images/pentest-book-metasploit-browser-autopwn-exploitation.png)





### Technical Summary Table
| Technique | Tool/Module | Purpose |
| :--- | :--- | :--- |
| **Ping Sweep** | Bash / ICMP | Active host discovery |
| **DNS Spoofing** | `dnsspoof` | Traffic redirection / MITM |
| **Process Migration** | Meterpreter | Persistence and stability |
| **Social Engineering** | Signed Java Applet | Bypassing patches via user interaction |
| **Automation** | `browser_autopwn` | Efficient client-side exploitation |
