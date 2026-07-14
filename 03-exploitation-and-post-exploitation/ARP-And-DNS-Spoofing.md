# Network Traffic Manipulation: ARP Poisoning & DNS Spoofing

## BOOK REFERENCE : Penetration Testing: A Hands-On Introduction to Hacking by *Georgia Weidman*
## Methodology & Topology

The goal is to intercept traffic between an Ubuntu target (192.168.20.11) and a Windows XP target (192.168.20.10). By sending malicious ARP replies, the Kali machine (192.168.20.9) convinces both targets that it is the legitimate recipient of their traffic.

![ARP_Topology](images/pentest-book-progress-arp-cache-poisoning.png)


### Traffic Redirection
- Kali forwards traffic from Ubuntu to Windows XP and vice versa.

### Interception
- Because the traffic passes through Kali, we can now use tools like dnsspoof to alter the data in transit.

## Step 1: Executing the ARP Cache Poisoning
To start the redirection, we use `arpspoof`. This tool informs the target that our MAC address is associated with the IP address of the machine we are impersonating.

**To fool the Ubuntu target into thinking we are the Windows XP machine:**
```bash
root@kali:~# arpspoof -i eth0 -t 192.168.20.11 192.168.20.10
```

![ARP_Spoofing](images/pentest-book-progress-arpspoof.png)


- `-i eth0`: Specifies the network interface.
- `-t 192.168.20.11`: The target we are poisoning (Ubuntu).
- `192.168.20.10`: The IP address we are pretending to be (Windows XP).

> [!Note]
> For a full MITM, you would run a second instance of arpspoof targeting the Windows machine in the opposite direction.

## Step 2: DNS Spoofing with Dnsspoof
Once the traffic is flowing through our Kali machine, we can intercept DNS queries. By using a custom `hosts.txt` file, we can redirect specific domains (like www.gmail.com) to a destination of our choosing.
```bash
root@kali:~# dnsspoof -i eth0 -f hosts.txt
```

![DNS_Spoofing](images/pentest-book-progress-dns-spoofing.png)


When the Ubuntu target attempts to resolve a domain, our Kali machine responds with the spoofed IP address before the real DNS server can. We can verify the success of the attack by running an nslookup on the victim machine:
```bash
georgia@ubuntu:~$ nslookup www.gmail.com
```
If successful, the returned IP address will match that specified in `hosts.txt` rather than Gmail's actual IP.
---
**SUMMARY**
The core of this lab was about achieving "Invisible Placement" on the network. By manipulating how machines talk to each other at the lower layers, we can control what they see at the higher layers (like the web).


​○  **ARP Poisoning:** The "Identity Theft" Phase

​.   ARP poisoning is the heavy lifter here. Since computers on a local network use MAC addresses to talk, we send unsolicited ARP replies to the targets.

​.   *The Logic:* We tell the Ubuntu box, "Hey, I'm the Windows machine," and tell the Windows machine, "Hey, I'm the Ubuntu box."

 
  *​The Result:* Both machines start sending their traffic directly to our Kali instance. We aren't just "listening" anymore; we are the gateway.
​


○ **DNS Spoofing:** The "Redirection" Phase

​.   Once we have the traffic flowing through us, we can get creative. DNS spoofing is essentially lying about where a website lives.
​
.   *The Logic:* We wait for the target to ask, "Where is gmail.com?" Before the real DNS server can answer, we jump in with our own response.

  *​The Result:* The target’s browser is pointed to whatever IP we choose—usually a malicious clone of the site we're trying to spoof. It’s the ultimate way to facilitate a credential harvest or a drive-by download.
