# Technical Lab Report: Open-Source Intelligence (OSINT)

## Author
CypherX

## Platform
TryHackMe

## Room
OhSINT

---

## Executive Summary
This report documents the findings of an Open-Source Intelligence (OSINT) investigation conducted as part of the TryHackMe "OhSINT" laboratory environment. The primary purpose of this investigation was to utilize passive reconnaissance techniques to uncover the digital footprint of a target individual, starting with a single image file.

The scope of the engagement was limited to publicly accessible information and open-source platforms. By systematically extracting metadata, pivoting through search engine results, and correlating social media profiles, the investigation successfully identified the target's physical locations, email address, wireless network configuration, and inadvertently exposed credentials. The overall outcome was the successful mapping of the target's online identity and the retrieval of all required intelligence flags.

---

## Objectives
The core objectives of this OSINT investigation included:
- Conduct passive intelligence gathering: Collect data without directly interacting with the target's infrastructure.
- Identify publicly available information: Uncover personal and technical details exposed on the public web.
- Pivot between multiple OSINT sources: Use initial clues to discover secondary accounts and platforms.
- Correlate digital evidence: Connect disparate pieces of information to verify the target's identity.
- Answer investigative questions: Fulfill the specific intelligence requirements outlined by the laboratory room.

---

## Skills Demonstrated
During this investigation, the following cybersecurity and intelligence gathering skills were demonstrated:
- Open-Source Intelligence (OSINT)
- Passive Reconnaissance
- Digital Footprinting
- Metadata Analysis
- Social Media Intelligence (SOCMINT)
- Username Enumeration
- Domain Investigation
- Search Engine Intelligence
- Information Correlation

---

## Investigation Methodology 
### Documentation & Methodology 
The investigative workflow followed a structured intelligence-gathering cycle to ensure accuracy and comprehensive coverage:
1. **Initial Evidence Review:** Examination of the provided initial artifact to determine actionable pivot points.
2. **Metadata Analysis:** Extraction of hidden data (EXIF) from the initial file to discover usernames or locations.
3. **Search Engine Pivoting:** Utilizing search engines (Google dorking) to identify digital footprints tied to the discovered username.
4. **Social Media Investigation:** Analyzing identified social media profiles for personal information, behaviors, and additional clues.
5. **Code Repository Analysis:** Reviewing developer profiles and public code repositories for hardcoded contact information.
6. **Website and Domain Investigation:** Analyzing personal blogs or websites associated with the target.
7. **Evidence Correlation:** Cross-referencing findings from multiple platforms to establish a unified target profile.
8. **Verification:** Utilizing third-party databases (e.g., WiGLE) to translate technical identifiers (BSSID) into human-readable locations (SSID).
9. **Flag Retrieval:** Documenting final answers to meet laboratory objectives.
Each stage is critical to maintaining a logical timeline. Relying on verified data before pivoting prevents false positives or misidentifying the target.

---

# Investigation Walkthrough

## 1. Initial Evidence and Metadata Analysis
The investigation began with a single artifact: an image file named `WindowsXP.jpg`. To determine if the file contained hidden intelligence, the command-line utility `exiftool` was utilized to extract EXIF metadata.

The output revealed critical pieces of intelligence. The most significant finding was the **Copyright** field, which contained the string `OWoodflint`. Additionally, GPS coordinates were embedded in the file (`54 deg 17' 41.27" N`, `2 deg 15' 1.33" W`). The discovery of the moniker `OWoodflint` served as the primary pivot point for the rest of the investigation, shifting the focus from the file itself to username enumeration.

## 2. Search Engine Pivoting
With a distinct username identified, a standard Google search was executed for the query `OWoodflint`. The search results successfully correlated the username to multiple online platforms, indicating a broad digital footprint. The top results revealed:
- An X (formerly Twitter) account.
- A personal WordPress blog (`oliverwoodflint.wordpress.com`).
- A Medium article referencing the target.
- A GitHub repository (discovered shortly after via similar searches).

This confirmed that the target actively used the handle `OWoodflint` across various domains.

## 3. Social Media Intelligence (SOCMINT) - Twitter/X
Pivoting to the discovered Twitter profile (`@OWoodflint`), the investigator analyzed the target's public timeline. The profile's avatar was identified as a cat. The user had posted a tweet stating, "From my house I can get free wifi ;D," followed by a **BSSID**: `B4:5D:50:AA:86:41`.

This action was highly beneficial. The avatar satisfied one of the investigation's intelligence requirements. More importantly, the exposed BSSID (the MAC address of a wireless access point) provided an opportunity to determine the target's exact home network. A secondary browser tab confirmed that WiGLE (**Wireless Geographic Logging Engine**) was used to search this BSSID, successfully resolving it to the SSID **UnileverWiFi**.

## 4. Code Repository Investigation - GitHub
Continuing with username enumeration, the investigator navigated to the target's GitHub profile (`OWoodfl1nt`). Note that there is a slight variation in spelling (using `'1'` instead of `'i'`), which is a common tactic users employ when their primary handle is taken.

The profile hosted a repository named **people_finder**. Analyzing its `README.md` file revealed a direct introduction:
> "Hi all, I am from London..."
> and provided a contact method:
> `OWoodflint@gmail.com`

This step successfully geolocated the target's primary residence and established a direct communication vector, satisfying two additional investigative requirements.

## 5. Website Investigation - WordPress
The final platform identified in initial Google search was the target's WordPress blog (`oliverwoodflint.wordpress.com`). Navigating there, an investigator found a single blog post titled "Hey" authored by `owoodflint`. The post read:
> "Im in New York right now, so I will update this site right away with new photos!"

This answered questions regarding the target's current holiday destination.

To ensure no hidden information was missed, manual inspection of web page elements was performed by highlighting text sections. This revealed hidden text formatted in white font matching background color:
> **pennYDr0pper!**

Given its complexity and structure, it was immediately identified as a compromised password—a severe operational security (**OPSEC**) failure by the target.

## 6. Final Verification
All discovered intelligence was cross-referenced and inputted into [TryHackMe](https://tryhackme.com) validation portal. Every piece of correlated evidence was verified as accurate, successfully concluding this active investigation phase.


---

# Evidence Correlation

**The success of this investigation relied heavily on connecting fragmented pieces of data into a cohesive intelligence profile.**

- **The Anchor:** The EXIF metadata inside *WindowsXP.jpg* provided the anchor point (*OWoodflint*). Without this initial extraction, the target would have remained anonymous.
- **Identity Verification:** The username *OWoodflint* seamlessly linked the image to a Twitter account, a WordPress blog, and a GitHub repository. The slight variation (*OWoodfl1nt*) on GitHub was verified as the same individual due to the cross-referenced Twitter handle in the README file.
- **Physical Tracking:** The target's physical movements were mapped by combining the GitHub README (establishing the home base as London) and the WordPress blog (establishing a temporary holiday location in New York). The BSSID dropped on Twitter corroborated a physical network location.
- **Compromise Correlation:** The habit of oversharing online directly led to the exposure of the target's email address on a public repository and their password hidden poorly on a personal blog.

## Findings

The investigation yielded the following confirmed intelligence regarding the target:

| **Item** | **Details** |
| --- | --- |
| Target Avatar | A cat (Discovered via Twitter/X) |
| City of Residence | London (Discovered via GitHub README.md) |
| Home Network (SSID) | UnileverWiFi (Correlated via WiGLE using BSSID B4:5D:50:AA:86:41 found on Twitter) |
| Personal Email Address | OWoodflint@gmail.com (Discovered via GitHub) |
| Current Holiday Location | New York (Discovered via WordPress blog) |
| Compromised Password | pennYDr0pper.! (Discovered via hidden HTML text on WordPress) |

## Security Perspective

This investigation highlights several critical OPSEC failures that attackers frequently leverage during the reconnaissance phase of a cyberattack:

- **Metadata Exposure:** Publishing images without stripping EXIF data can inadvertently reveal exact GPS coordinates, device models, and author names, giving attackers an immediate pivot point.
- **Oversharing on Social Media:** Bragging about "free Wi-Fi" and posting a BSSID allows anyone to geolocate the user's home using wardriving databases like WiGLE.
- **Public Profile Enumeration:** Using the same username across multiple platforms (Twitter, GitHub, WordPress) allows investigators to easily aggregate a comprehensive profile.
- **Hidden Text is Not Secure:** Relying on CSS styling (white text on a white background) to hide sensitive information like passwords is a fundamental misunderstanding of web security.

## MITRE ATT&CK Mapping

The investigative steps performed align with the following MITRE ATT&CK tactics and techniques for the Reconnaissance phase (TA0043):

- **T1589.001 - Gather Victim Identity Information:** Credentials — Identified a hidden password on the target's WordPress site.
- **T1589.002 - Gather Victim Identity Information:** Email Addresses — Extracted the target's personal email from a public GitHub repository.
- **T1590.002 - Gather Victim Network Information:** Domain Properties — Analyzed the WordPress subdomain associated with the target.
- **T1592.004 - Gather Victim Host Information:** Client Configurations — Mapped the target's local network infrastructure by converting an exposed BSSID into an SSID.

## Key Takeaways

- Always scrub metadata: Before publishing documents or images, EXIF data must be stripped to prevent unintended intelligence leaks.
- Assume public data will be correlated: Information that seems harmless in isolation (a BSSID, an email address) can be devastating when aggregated by a skilled investigator.
- Security through obscurity fails: Hiding text in plain sight is easily bypassed by rudimentary analysis.

## Conclusion

The "OhSINT" investigation was a successful demonstration of passive reconnaissance. By systematically following a digital trail originating from a single piece of metadata, a complete intelligence profile was built without ever alerting the target. This lab reinforces the importance of meticulous documentation, creative pivoting, and an understanding of how public footprints can be weaponized in real-world penetration testing engagements.

---

# Reference Lab Images

![](images/ohsint_room_thm_video.png)
![](images/ohsint_room_exiftool.png)
![](images/ohsint_room_ans_1_google_searchl.png)
![](images/ohsint_room_ans_2_google_searchl.png)
![](images/ohsint_room_ans_3_x_findingl.png)
![](images/ohsint_room_ans_4_email_finding.png)
![](images/ohsint_room_ans_6_location_finding.png)
![](images/ohsint_room_ans_7_password_finding.png)
![](images/ohsint_room_thm_I.png)
![](images/ohsint_room_thm_II.png)
