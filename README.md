# TryHackMe Writeups: Red Team (eJPT) and Blue Team (SOC Level 1)

Hands-on lab writeups from my TryHackMe training, written while preparing for the **eJPTv2** (earned) and the **SAL1 / SOC Level 1** path (in progress). Each page focuses on method: what I ran, what I observed, and how I would approach it in a real engagement or SOC. Flags and answer keys are not published.

**Focus areas:** SOC alert triage · phishing analysis · network traffic analysis · SIEM and IDS · penetration testing fundamentals

[TryHackMe profile](https://tryhackme.com/p/yassinayachi55) · [GitHub profile](https://github.com/Ayachi-Yassine)

## Start here

| Track | Writeup | Scope |
|---|---|---|
| Blue team | [Phishing Unfolding](sal1/phishing-unfolding.md) | SOC Simulator scenario, medium difficulty |
| Blue team | [Data Exfiltration Detection](sal1/data-exfiltration-detection.md) | Spotting exfiltration in DNS, FTP and HTTP traffic |
| Blue team | [Man-in-the-Middle Detection](sal1/man-in-the-middle-detection.md) | Detecting MITM activity in network traffic |
| Blue team | [Network Discovery Detection](sal1/network-discovery-detection.md) | Detecting scanning and discovery activity |
| Blue team | [Eviction](sal1/eviction.md) | Threat investigation mapped to MITRE ATT&CK |
| Red team | [Internal](ejpt/internal.md) | Black-box penetration test scenario |
| Red team | [Blue](ejpt/blue.md) | Windows machine exploitation with Metasploit |
| Red team | [Kenobi](ejpt/kenobi.md) | Service enumeration and Linux privilege escalation |

## What this repo covers

**Red team (eJPT)**
- Reconnaissance and service enumeration with Nmap
- Exploitation with Metasploit, including Windows and Linux machines
- Linux and Windows privilege escalation
- Web application and CMS exploitation

**Blue team (SOC Level 1)**
- Alert triage, investigation and reporting
- Phishing email analysis (headers, SPF, DKIM, DMARC)
- Network traffic analysis with Wireshark, TShark and NetworkMiner
- SIEM with Splunk and Elastic, intrusion detection with Snort
- MITRE ATT&CK, Cyber Kill Chain and Pyramid of Pain

## Full index

<details>
<summary><b>Red team: eJPT path (23 writeups)</b></summary>

### Fundamentals and tooling

- [Vulnerabilities 101](ejpt/vulnerabilities-101.md)
- [Metasploit: Introduction](ejpt/metasploit-introduction.md)
- [Metasploit](ejpt/metasploit.md)
- [Network Services](ejpt/network-services.md)
- [Linux Privilege Escalation](ejpt/linux-privilege-escalation.md)

### Windows machines

- [Ice](ejpt/ice.md)
- [Blue](ejpt/blue.md)
- [Relevant](ejpt/relevant.md)

### CTF-style machines

- [Basic Pentesting](ejpt/basic-pentesting.md)
- [RootMe](ejpt/rootme.md)
- [Easy Peasy CTF](ejpt/easy-peasy-ctf.md)
- [Bolt](ejpt/bolt.md)
- [HackerNote](ejpt/hackernote.md)
- [GoldenEye](ejpt/goldeneye.md)
- [Anonymous](ejpt/anonymous.md)
- [HA Joker CTF](ejpt/ha-joker-ctf.md)
- [Pickle Rick](ejpt/pickle-rick.md)
- [Ignite](ejpt/ignite.md)
- [Simple CTF](ejpt/simple-ctf.md)
- [Kenobi](ejpt/kenobi.md)
- [LazyAdmin](ejpt/lazyadmin.md)
- [Cowboy Hacker](ejpt/cowboy-hacker.md)

### Full engagement

- [Internal](ejpt/internal.md)

</details>

<details>
<summary><b>Blue team: SAL1 (SOC Level 1) path (36 writeups)</b></summary>

### SOC fundamentals

- [SOC L1 Alert Triage](sal1/soc-l1-alert-triage.md)
- [SOC L1 Alert Reporting](sal1/soc-l1-alert-reporting.md)
- [SOC Workbooks and Lookups](sal1/soc-workbooks-and-lookups.md)
- [SOC Metrics and Objectives](sal1/soc-metrics-and-objectives.md)

### SIEM, EDR and log analysis

- [Introduction to EDR](sal1/introduction-to-edr.md)
- [Introduction to SIEM](sal1/introduction-to-siem.md)
- [Splunk: The Basics](sal1/splunk-the-basics.md)
- [Splunk: Exploring SPL](sal1/splunk-exploring-spl.md)
- [Elastic Stack: The Basics](sal1/elastic-stack-the-basics.md)

### Threat frameworks

- [Pyramid Of Pain](sal1/pyramid-of-pain.md)
- [Cyber Kill Chain](sal1/cyber-kill-chain.md)
- [Unified Kill Chain](sal1/unified-kill-chain.md)
- [MITRE](sal1/mitre.md)

### Phishing analysis

- [Phishing Analysis Fundamentals](sal1/phishing-analysis-fundamentals.md)
- [Phishing Emails in Action](sal1/phishing-emails-in-action.md)
- [Phishing Analysis Tools](sal1/phishing-analysis-tools.md)
- [Phishing Prevention](sal1/phishing-prevention.md)
- [The Greenholt Phish](sal1/the-greenholt-phish.md)
- [Snapped Phish-ing Line](sal1/snapped-phish-ing-line.md)
- [A Bucket of Phish](sal1/a-bucket-of-phish.md)
- [Phishing Unfolding](sal1/phishing-unfolding.md)

### Network traffic analysis and detection

- [Networking Essentials](sal1/networking-essentials.md)
- [Network Traffic Basics](sal1/network-traffic-basics.md)
- [Wireshark: The Basics](sal1/wireshark-the-basics.md)
- [Wireshark: Packet Operations](sal1/wireshark-packet-operations.md)
- [Wireshark: Traffic Analysis](sal1/wireshark-traffic-analysis.md)
- [NetworkMiner](sal1/networkminer.md)
- [TShark Challenge I: Teamwork](sal1/tshark-challenge-i-teamwork.md)
- [Network Security Essentials](sal1/network-security-essentials.md)
- [Network Discovery Detection](sal1/network-discovery-detection.md)
- [Data Exfiltration Detection](sal1/data-exfiltration-detection.md)
- [Man-in-the-Middle Detection](sal1/man-in-the-middle-detection.md)

### Intrusion detection

- [IDS Fundamentals](sal1/ids-fundamentals.md)
- [Snort](sal1/snort.md)

### Investigations

- [Eviction](sal1/eviction.md)
- [SUMMIT](sal1/summit.md)

</details>

## Repository layout

```
ejpt/                   red team writeups
sal1/                   blue team writeups
images/                 screenshots used by the writeups
WRITEUP_TEMPLATE.md     structure for new writeups
```
