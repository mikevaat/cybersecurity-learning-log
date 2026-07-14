# OSINT & Threat Intelligence Report: Operation Sakura

**Investigating Analyst:** CypherX  
**Date:** July 2026  
**Document Classification:** TLP:CLEAR

## Executive Summary
This report documents a comprehensive Open Source Intelligence (OSINT) and threat hunting investigation into a threat actor operating under the alias "Sakura". The objective of this engagement was to deanonymize the attacker, map their infrastructure, trace their cryptocurrency transactions, and geographically track their physical movements.
By leveraging metadata extraction, version control history analysis, blockchain forensics, dark web enumeration, and geospatial intelligence (GEOINT), the investigation successfully identified the actor's primary email address, cryptocurrency wallet, exact home location, and travel itinerary. This document outlines the full investigative workflow, tools utilized, and Indicators of Compromise (IOCs) discovered during the analysis.

## Lab Information
| Attribute | Details |
| --- | --- |
| Platform | TryHackMe |
| Room | Sakura |
| Category | Open Source Intelligence (OSINT) |
| Investigation Type | Threat Actor Deanonymization & GEOINT |
| Skills Practiced | Metadata Analysis, Blockchain Forensics, GitHub Reconnaissance, Dark Web OSINT, Wi-Fi Geolocation (WiGLE), Image Geolocation |

## Tools Utilized
- **ExifTool:** File metadata extraction and analysis.
- **GitHub:** Version control reconnaissance and commit history analysis.
- **PGP Decoder (cirw.in):** Cryptographic key parsing.
- **Etherscan:** Ethereum blockchain exploration and transaction tracing.
- **Twitter (X):** Social media intelligence (SOCMINT).
- **DeepPaste / Tor:** Dark web pastebin enumeration.
- **WiGLE (Wireless Geographic Logging Engine):** BSSID mapping and geolocation.
- **Google Lens & Dorking:** Advanced reverse image searching and geospatial analysis.

---


# Investigation Workflow

# Phase 1: Initial Reconnaissance & Metadata Analysis
**Objective:** Identify the threat actor's core username from provided digital artifacts.
The investigation began with an initial artifact: an SVG file named `sakurapwnedletter.svg`. To uncover hidden identifying information, I analyzed the file's metadata using a command-line utility.

### Analysis:
Vector graphics frequently retain directory paths and system information from the local environment where they were created.

### Commands:
dosexiftool /home/cypherx/Downloads/sakurapwnedletter.svg
The output explicitly contained an `Export-filename` parameter pointing to a local desktop path: `/home/SakuraSnowAngelAiko/Desktop/pwnedletter.png`. This OPSEC failure provided the primary target alias: **SakuraSnowAngelAiko**.

# Phase 2: Code Repository Enumeration & Cryptographic Artifacts
**Objective:** Map the attacker's public infrastructure and extract communication channels.
Using the discovered alias, I pivoted to GitHub to identify potential developer profiles linked to the threat actor.

### Analysis:
the GitHub search successfully located the user `sakurasnowangelaiko` operating under the display name "Aiko". The profile hosted several repositories indicating an interest in cryptocurrency (`cpuminer`, `xmrig`) and encrypted communications (`Mailpile`, `PGP`).
I extracted the ASCII-armored PGP public key from the PGP repository. PGP keys inherently contain user ID packets, which typically bind the cryptographic key to a name and email address. By parsing the key block through a decoder, I successfully extracted the underlying User ID.

### Discovered Email Address:
`sakuraSnowAngel83@protonmail.com`

# Phase 3: Cryptocurrency Tracing & Blockchain Forensics

**Objective:** Track the threat actor's financial infrastructure and mining operations.

Given the presence of mining software in the GitHub repositories, I investigated the commit history to locate misconfigured configuration files or hardcoded wallet addresses.

### Analysis:
- Threat actors frequently scrub credentials from active code, but historical commits often remain accessible.
- Reviewing the commit hash `d507757d5d2208d0124783a8a670239ce19b806c` revealed a previously deleted line containing stratum connection parameters.

### Extracted Wallet Address:
`0xa102397dbeeBeFD8cD2F73A89122fCdB53abB6ef`

I queried this address on the Ethereum blockchain via Etherscan. The transaction history showed consistent incoming micro-transactions, a pattern indicative of cryptocurrency mining. The sender for these transactions was identified as the Ethermine pool.

Further analysis of the wallet's internal transactions and token transfers revealed that the attacker did not strictly hold Ethereum. By filtering for ERC-20 tokens, I discovered outbound transfers of Tether USD (USDT) routed to Binance 3, mapping the actor's cash-out trajectory.

# Phase 4: Social Media SOCMINT & Dark Web Corroboration

**Objective:** Locate alternative social media personas and pivot to dark web intelligence for localized infrastructure.

### Analysis:
- Searches for variations of the "Sakura" alias on X (formerly Twitter) surfaced the account [@SakuraLoverAiko](https://twitter.com/SakuraLoverAiko).
- A timeline analysis revealed a significant OPSEC slip: a post stating "Silly me, I forgot to introduce myself! Hi there! I'm @AikoAbe3!". This confirmed an alternative, likely secondary, persona.
- During external OSINT gathering, an MD5 hash linked to the attacker's operations was discovered.
- Querying this hash on a dark web paste service (DeepPaste V3) uncovered a text file authored by "Anon" detailing saved Wi-Fi networks and passwords.
- The paste contained an entry for "Home WiFi" assigned the SSID **DK1F-G**. To geo-locate this network, I utilized the Wireless Geographic Logging Engine (WiGLE). Searching the database for **DK1F-G** returned a unique Basic Service Set Identifier (BSSID).

### Discovered BSSID:
`84:AF:EC:34:FC:F8`

The dark web paste also included an entry for **HIROSAKI_FREE_Wi-Fi**, establishing a strong geographic anchor suggesting the actor's home city is Hirosaki, Japan.

# Phase 5: Geospatial Intelligence (GEOINT) & Flight Tracking

**Objective:** Track the attacker's physical movements and pinpoint locations based on photographic evidence.

The threat actor's Twitter timeline contained several images from a recent trip, providing an opportunity for rigorous GEOINT analysis.

### Evidence & Analysis:
- **The Cherry Blossoms:** The blooms were identified as the Okame flowering cherry tree, establishing a temporal and geographic baseline.
- **The Pathway Image:** Using key landmarks in the background—specifically the Washington Monument obelisk in the distant skyline and the parallel railway—the location was pinpointed to the Anacostia Riverwalk Trail in Washington, D.C.
- **Departure Airport:** The closest major airport to the Anacostia Riverwalk Trail is Ronald Reagan Washington National Airport (DCA), positioned just across the Potomac River.
- **The Final Layover:** A Twitter post captioned "My final layover, time to relax!" featuring a lounge sign demonstrates the reverse search of this specific slatted wood design, positively identifying it as the Japan Airlines (JAL) First Class Sakura Lounge located in Tokyo Haneda Airport (HND).
- **The Aerial Map:** The analysis of a map shared during the final flight home. Topographical correlation and satellite imagery identified the large, dark blue body of water as Lake Inawashiro in Fukushima Prefecture.

---


# Key Indicators & Findings
| Indicator Type | Value | Context |
|----------------|--------|---------|
| Username / Alias | SakuraSnowAngelAiko | Primary alias derived from Exif metadata |
| Email Address | SakuraSnowAngel83@protonmail.com | Extracted from PGP public key |
| Crypto Wallet (ETH) | 0xa102397dbeeBeFD8cD2F73A89122fCdB53abB6ef | Found in GitHub commit history |
| Mining Pool | Ethermine | Source of incoming ETH transactions |
| Alt. Currency | Tether USD (USDT) | Outbound ERC-20 token transfers |
| Twitter Handle | @SakuraLoverAiko | Primary social media account |
| Alt. Twitter Handle | @AikoAbe3 | Secondary social media alias |
| Home BSSID | 84:AF:EC:34:FC:F8 | Discovered via DeepPaste & WiGLE correlation |
| Home City | Hirosaki | Correlated from saved dark web Wi-Fi data |

# Lessons Learned
- **Metadata is a Silent Witness:** The extraction of the local desktop path from an SVG file demonstrates that even visually benign files can leak critical environmental data. Analysts must rigorously sanitize documents before publishing.
- **Version Control OPSEC:** Deleting a credential or sensitive string from a file and committing the changes does not remove it from the repository's history. Threat actors often fail to realize that the entire Git commit tree is immutable unless explicitly purged.
- **Cross-Domain Correlation:** The success of this investigation relied entirely on linking seemingly disparate pieces of data. Tying a GitHub username to a PGP key, an Ethereum wallet to dark web hashes, and physical photos to satellite telemetry exemplifies the power of a holistic OSINT methodology.

# Conclusion
This investigation systematically dismantled the operational security of the threat actor known as "Sakura". By chaining metadata leaks to version control errors, and ultimately to physical world GEOINT artifacts, the investigation transitioned from a simple anonymous moniker to a highly detailed profile including email infrastructure, financial ledgers, hardware fingerprints (BSSID), and precise physical locations. The findings provide a complete intelligence package suitable for further defensive modeling or law enforcement escalation.

---

# Reference Lab Images 

![](images/sakura_room_II_pgp_key.png)
![](images/sakura_room_II_pgp_key_decoding.png)
![](images/sakura_room_II_pgp_repo.png)
![](images/sakura_room_II_real_name_finding.png)
![](images/sakura_room_IV_answers.png)
![](images/sakura_room_VI_answerspng.png)
![](images/sakura_room_VI_layover.png)
![](images/sakura_room_VI_reverse_image_airport_hnd.png)
![](images/sakura_room_VI_reverse_image_search.png)
![](images/sakura_room_VI_reverse_image_search_2.png)
![](images/sakura_room_VI_reverse_image_search_closest_airport.png)
![](images/sakura_room_VI_reverse_image_search_lake.png)
![](images/sakura_room_V_answers.png)
![](images/sakura_room_V_deeppaste_result.png)
![](images/sakura_room_V_twitter_handle.png)
![](images/sakura_room_V_wigle.png)
![](images/sakura_room_eth_repo.png)
![](images/sakura_room_etherscan_ethermine.png)
![](images/sakura_room_etherscan_results.png)
![](images/sakura_room_etherscan_tether.png)
![](images/sakura_room_username_finding.png)
