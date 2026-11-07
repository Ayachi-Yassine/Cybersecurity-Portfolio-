# Evicton

# APT Attack Chain

## 1. Recon + Initial Access — Spearphishing Link

**Question:** What technique can an APT use to perform recon and gain initial access?

**Answer:** **Spearphishing Link — T1566.002**

### Concept

The attacker sends a victim a malicious link, usually through email or another messaging mechanism.

```

```

The link could lead to:

- A phishing login page
- A malicious download
- An exploit
- A payload

### SOC mindset

Don't only ask whether an email was sent. Investigate:

- Who sent it?
- Who received it?
- What URL/domain was involved?
- Did the victim click?
- Was a file downloaded?
- Did authentication happen afterward?
- Did a process execute afterward?

**Investigation chain:**

`Email → User action → Process → Network → Persistence`

---

# 2. Resource Development — Email Accounts

**Question:** Which accounts might the APT compromise while developing resources?

**Answer:** **Email accounts**

### Concept

An attacker may compromise an email account and use it as part of future attacks.

```

```

A real employee mailbox can make phishing more convincing than a message from an unknown attacker-controlled address.

### SOC mindset

If an account is compromised, investigate:

- Mailbox access
- Emails read
- Contacts accessed
- Forwarding rules
- Messages sent
- Attachments accessed
- OAuth tokens/app permissions
- Whether the account was later used for phishing

**Key idea:** A compromised account can become attacker infrastructure.

---

# 3. User Execution — Malicious File + Malicious Link

**Question:** What two User Execution techniques should the analyst look for?

**Answers:**

- **Malicious File**
- **Malicious Link**

### Concept

The attacker convinces the victim to perform an action that causes code or a malicious resource to execute.

```

```

## Malicious File

Examples:

- Malicious executable
- Macro-enabled document
- Disguised file

Example:

```

```

The important question is:

> **What process was created after the user opened the file?**
> 

Example:

```

```

## Malicious Link

The user clicks a malicious URL that could:

- Download a payload
- Steal credentials
- Redirect the victim
- Exploit the browser
- Trigger further execution

### SOC mindset

Think:

`Email → User action → Process → Network → Persistence`

---

# 4. Execution — PowerShell + Windows Command Shell

**Question:** Which scripting interpreters should the analyst search for?

**Answers:**

- **PowerShell**
- **Windows Command Shell**

## PowerShell

PowerShell is a Windows scripting environment that attackers can abuse for:

- Command execution
- Script execution
- Downloading files
- System interaction
- Payload execution

Example:

```

```

## Windows Command Shell

Usually:

```

```

Example:

```

```

or:

```

```

### SOC mindset

Don't just search for `powershell.exe`.

Look at the **process chain**:

```

```

Ask:

- Who launched it?
- What command line was used?
- What did it launch?
- Which user executed it?
- What happened afterward?

**Important:** PowerShell or `cmd.exe` is not automatically malicious. Context matters.

---

# 5. Persistence — Registry Run Keys

**Question:** If scripts change the registry to maintain persistence, which keys should be monitored?

**Answer:** **Registry Run Keys**

### Concept

Attackers can configure Windows to automatically execute a program when a user logs in.

```

```

Without persistence:

```

```

With persistence:

```

```

### SOC mindset

For suspicious registry changes, investigate:

- Which key changed?
- What value was created?
- What executable/script does it point to?
- When did it change?
- Which process made the change?
- Which user made the change?

**Key idea:** The process responsible for the registry modification is valuable evidence.

---

# 6. Defense Evasion — Rundll32

**Question:** Which system binary should be scrutinized for proxy execution?

**Answer:** **`rundll32.exe`**

### Concept

`rundll32.exe` is a legitimate Windows binary used to execute functions exported from DLLs.

Attackers can abuse legitimate Windows binaries to execute malicious code.

This is **Signed Binary Proxy Execution**.

```

```

The attacker uses a trusted system binary as a proxy for execution.

### Why "proxy"?

The attacker isn't necessarily executing their malicious code through an obviously malicious executable.

Instead:

> A legitimate Windows binary performs the execution on the attacker's behalf.
> 

### SOC mindset

Do **not** automatically treat `rundll32.exe` as malicious.

Investigate:

- Parent process
- Command line
- DLL loaded
- DLL location
- Digital signature
- Unusual directories
- User context
- What happened after execution

**Key principle:**

> Legitimate tools can be abused for malicious purposes.
> 

---

# 7. Discovery — Network Sniffing

**Question:** `tcpdump` was found on a compromised host. What discovery technique might the APT be using?

**Answer:** **Network Sniffing — T1040**

### Concept

`tcpdump` can capture and inspect network traffic.

```

```

An attacker may learn:

- IP addresses
- Hosts communicating
- Protocols
- DNS queries
- Network structure
- Potentially sensitive information in unencrypted traffic

### Why is this Discovery?

The attacker is trying to **learn about the environment**.

They are gathering information that can help them decide what to attack next.

### SOC mindset

If `tcpdump` appears unexpectedly, investigate:

- Who installed it?
- Who executed it?
- When?
- Which network interface was captured?
- Was a packet capture saved?
- Where was the capture saved?
- How long did the capture run?
- Was the capture transferred elsewhere?

---

# 8. Lateral Movement — SMB / Windows Admin Shares

**Question:** Which remote service should be observed when the APT uses remote services for lateral movement?

**Answer:** **SMB / Windows Admin Shares — T1021.002**

### Concept

The attacker moves from one compromised system to another.

```

```

Important Windows administrative shares include:

```

```

Attackers with appropriate privileges can abuse these for lateral movement.

### SOC mindset

When suspicious SMB activity appears, investigate:

- Source host
- Destination host
- Account used
- Authentication events
- SMB connections
- Files transferred
- Remote service creation
- Process execution

### Important

SMB itself is **not malicious**.

The question is:

> **Is this SMB activity normal for this user, host, and environment?**
> 

---

# 9. Collection — SharePoint

**Question:** What information repository could be targeted when the APT wants intellectual property?

**Answer:** **SharePoint**

### Concept

Corporate intellectual property can exist in information repositories such as SharePoint.

Possible data includes:

- Technical documents
- Project files
- Business documents
- Research
- Intellectual property

### Critical distinction

**Collection ≠ Exfiltration**

### Collection

The attacker gathers the information.

```

```

### Exfiltration

The attacker moves the information outside the organization.

```

```

This distinction is extremely important for SOC investigations.

---

# 10. Command & Control — External Proxy + Multi-hop Proxy

**Question:** The APT cannot directly connect to its C2. What types of proxy might it use?

**Answers:**

- **External Proxy**
- **Multi-hop Proxy**

**MITRE ATT&CK:** `T1090 — Proxy`

### Why proxy?

The question itself gives the clue:

> **"What types of proxy..."**
> 

The attacker can route traffic through intermediary systems rather than communicating directly with the final C2.

---

## External Proxy

```

```

The proxy sits between the compromised system and the final destination.

---

## Multi-hop Proxy

```

```

Multiple intermediary systems are used.

### Why use proxies?

Potential objectives include:

- Hiding the direct victim-to-C2 relationship
- Making network investigation harder
- Adding layers between the victim and C2
- Making infrastructure relationships harder to identify

### SOC mindset

Don't only ask:

> "What IP is the victim connecting to?"
> 

Ask:

> **"What is the communication path, and are there intermediary systems?"**
> 

---

# Complete Attack Chain

The room is not really 10 unrelated questions. It is showing a possible attack lifecycle:

```

```

---

# SAL1 Analyst Mindset

When you receive an alert, don't immediately ask:

> "Which MITRE technique is this?"
> 

Instead, build the story.

## 1. How did they get in?

**Initial Access**

- Phishing?
- Exploit?
- Valid accounts?

## 2. Did the user execute something?

**User Execution**

- Malicious file?
- Malicious link?

## 3. What actually executed?

**Execution**

- PowerShell?
- `cmd.exe`?
- Other scripting interpreter?

## 4. Did they establish persistence?

Look for:

- Registry Run Keys
- Scheduled Tasks
- Services
- Startup folder

## 5. Did they try to evade detection?

Look for:

- `rundll32.exe`
- Other signed binaries
- Obfuscation

## 6. What did they learn?

**Discovery**

Look for:

- Network information
- Users
- Processes
- Files
- Accounts

## 7. Did they move to another machine?

**Lateral Movement**

Look for:

- SMB
- RDP
- WinRM
- Other remote services

## 8. What data were they looking for?

**Collection**

Look for:

- SharePoint
- File shares
- Email
- Databases

## 9. How did they communicate?

**Command & Control**

Look for:

- Direct C2
- External proxy
- Multi-hop proxy

## 10. Did they actually steal the data?

**Exfiltration**

Always distinguish:

```

```

---

# The Most Important SAL1 Lesson

Do not memorize:

```

```

Instead, think:

```

```

### Example

Seeing:

```

```

alone is not enough.

But seeing:

```

```

provides a much stronger story.

### Core SOC principle

> **Understand the sequence, correlate evidence, identify the attacker objective, and then map the behavior to MITRE ATT&CK.**
> 

This is the mindset that is more valuable for **SAL1** than simply memorizing technique names.