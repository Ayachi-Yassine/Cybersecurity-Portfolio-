# ICE

Deploy & hack into a Windows machine, exploiting a very poorly secured media serve

## 1️⃣ Objective (Blog Section)

> The objective of the ICE room is to gain access to a Windows machine by identifying vulnerable services and exploiting them using Metasploit, then escalating privileges.
> 

## 2️⃣ Enumeration

### Command:

```bash
nmap -sS -sV -Pn <TARGET_IP>

```

### Why we do this:

- `sS` → fast, stealthy TCP scan
- `sV` → identify **service versions**
- `Pn` → Windows hosts often block ping

```bash
─$ nmap -sS -Pn 10.64.147.210
Starting Nmap 7.94SVN ( https://nmap.org ) at 2025-12-27 19:52 CET
Nmap scan report for 10.64.147.210
Host is up (4.3s latency).
Not shown: 988 closed tcp ports (reset)
PORT      STATE SERVICE
135/tcp   open  msrpc
139/tcp   open  netbios-ssn
445/tcp   open  microsoft-ds
3389/tcp  open  ms-wbt-server
5357/tcp  open  wsdapi
8000/tcp  open  http-alt
49152/tcp open  unknown
49153/tcp open  unknown
49154/tcp open  unknown
49158/tcp open  unknown
49159/tcp open  unknown
49160/tcp open  unknown

Nmap done: 1 IP address (1 host up) scanned in 54.67 seconds
                                                                                
┌──(ayachi㉿kali)-[~]
└─$ nmap -p135,139,445,3389,5357,8000,49152,49153,49154,49158,49159,49160 -sV -sC -T4 -Pn 10.64.147.210
Starting Nmap 7.94SVN ( https://nmap.org ) at 2025-12-27 19:58 CET
Nmap scan report for 10.64.147.210
Host is up (0.29s latency).

PORT      STATE SERVICE      VERSION
135/tcp   open  msrpc        Microsoft Windows RPC
139/tcp   open  netbios-ssn  Microsoft Windows netbios-ssn
445/tcp   open  microsoft-ds Windows 7 Professional 7601 Service Pack 1 microsoft-ds (workgroup: WORKGROUP)
3389/tcp  open  tcpwrapped
|_ssl-date: 2025-12-27T19:01:29+00:00; -2s from scanner time.
| rdp-ntlm-info: 
|   Target_Name: DARK-PC
|   NetBIOS_Domain_Name: DARK-PC
|   NetBIOS_Computer_Name: DARK-PC
|   DNS_Domain_Name: Dark-PC
|   DNS_Computer_Name: Dark-PC
|   Product_Version: 6.1.7601
|_  System_Time: 2025-12-27T19:01:06+00:00
| ssl-cert: Subject: commonName=Dark-PC
| Not valid before: 2025-12-26T18:37:14
|_Not valid after:  2026-06-27T18:37:14
5357/tcp  open  http         Microsoft HTTPAPI httpd 2.0 (SSDP/UPnP)
|_http-server-header: Microsoft-HTTPAPI/2.0
8000/tcp  open  http         Icecast streaming media server
49152/tcp open  msrpc        Microsoft Windows RPC
49153/tcp open  msrpc        Microsoft Windows RPC
49154/tcp open  msrpc        Microsoft Windows RPC
49158/tcp open  msrpc        Microsoft Windows RPC
49159/tcp open  msrpc        Microsoft Windows RPC
49160/tcp open  unknown
Service Info: Host: DARK-PC; OS: Windows; CPE: cpe:/o:microsoft:windows

Host script results:
| smb2-time: 
|   date: 2025-12-27T19:01:03
|_  start_date: 2025-12-27T18:36:55
| smb-security-mode: 
|   account_used: guest
|   authentication_level: user
|   challenge_response: supported
|_  message_signing: disabled (dangerous, but default)
| smb2-security-mode: 
|   2:1:0: 
|_    Message signing enabled but not required
| smb-os-discovery: 
|   OS: Windows 7 Professional 7601 Service Pack 1 (Windows 7 Professional 6.1)
|   OS CPE: cpe:/o:microsoft:windows_7::sp1:professional
|   Computer name: Dark-PC
|   NetBIOS computer name: DARK-PC\x00
|   Workgroup: WORKGROUP\x00
|_  System time: 2025-12-27T13:01:07-06:00
|_clock-skew: mean: 1h12m01s, deviation: 2h41m06s, median: -1s

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 210.56 seconds

```

# 🧠 Step 1: Read Nmap

---

## 🔍 High-Level Conclusion (Very Important)

From your scan we know:

✅ **Operating System**:

> Windows 7 Professional SP1 (6.1.7601)
> 

🚨 **Mindset alarm:**

Windows 7 is **EOL (End of Life)** → **known critical vulnerabilities**

---

## 🧊 Step 2: Port-by-Port Analysis (PRIORITY ORDER)

We don’t attack everything. We **rank targets**.

---

### 🟥 1️⃣ Port 3389 — RDP (TOP PRIORITY)

```
3389/tcpopentcpwrapped
Product_Version:6.1.7601

```

Even though it says `tcpwrapped`, scripts confirmed:

- RDP is active
- NTLM info leaked
- System name: **DARK-PC**
- OS version confirmed

🧠 **Why this is gold:**

- Windows 7 + RDP
- Famous vulnerability: **BlueKeep (CVE-2019-0708)**
- **Unauthenticated RCE**
- SYSTEM-level compromise

👉 This is your **main attack path**

---

### 🟧 2️⃣ Port 8000 — Icecast HTTP

```
8000/tcpopen http Icecast streaming mediaserver

```

🧠 Think like this:

- Third-party software
- Often outdated
- Sometimes misconfigured
- Possible credentials / RCE

👉 This is **secondary**, not primary.

---

### 🟨 3️⃣ Ports 445 / 139 — SMB

```
445/tcp open microsoft-ds
Message signing disabled

```

🧠 Important signals:

- SMB signing **not required**
- Guest access possible
- Windows 7

This opens doors to:

- Enumeration
- SMB exploits
- Credential attacks

👉 Strong **fallback attack vector**

---

### 🟦 4️⃣ MSRPC High Ports (49152–49159)

These are **normal Windows RPC ports**.

Not first targets, but they **confirm Windows internals exposure**.

---

## 🧠 Step 3: WHY 3389 > Icecast (Final Answer)

This is the mindset difference 👇

| Service | Risk Level | Why |
| --- | --- | --- |
| RDP (3389) | 🔥🔥🔥🔥🔥 | Full OS access, critical RCE |
| Icecast (8000) | 🔥🔥 | App-level, limited |
| SMB (445) | 🔥🔥🔥 | Lateral / credential abuse |

👉 **Always attack the service that gives you the OS first**

---

## 📝 Step 4: How to Write This in Your Notion Blog

You can write **something like this** (logic > commands):

---

### 🔎 Enumeration Results

> Nmap enumeration revealed that the target is running Windows 7 Professional Service Pack 1. Several Windows services were exposed, including SMB (ports 139/445), RDP (port 3389), and an Icecast streaming server on port 8000.
> 

### 🎯 Attack Surface Analysis

> Port 3389 (RDP) was identified as the primary attack vector due to the operating system being Windows 7, which is known to be vulnerable to critical RDP vulnerabilities such as BlueKeep (CVE-2019-0708). Successful exploitation of RDP could result in full system compromise.
> 

> Icecast on port 8000 was considered a secondary target, as it is a third-party application that may contain vulnerabilities, but does not provide direct operating system access.
> 

```jsx
run post/multi/recon/local_exploit_suggester
```

this will seggest exploits