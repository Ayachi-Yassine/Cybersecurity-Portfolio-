# BOLT

# 🔍 Enumeration – TryHackMe “Bolt: A Hero Is Unleashed”

## 1️⃣ Network Enumeration (Nmap)

The first step was identifying exposed services using `nmap`:

```bash
nmap -sV -Pn 10.64.131.151

```

### Results:

```
22/tcp   open  ssh     OpenSSH7.6p1 Ubuntu4ubuntu0.3
80/tcp   open  http    Apache httpd2.4.29
8000/tcp open  http    PHP7.2.32

```

### 🧠 What this tells us:

- **SSH (22)** → possible credential-based access later
- **HTTP (80 & 8000)** → web attack surface
- **Port 8000** is unusual → deserves extra attention

---

## 2️⃣ Web Enumeration (Directory Bruteforce)

I attempted directory enumeration using Gobuster:

```bash
sudo gobusterdir -u http://10.64.131.151 -w /usr/share/wordlists/dirb/common.txt

```

### Result:

- No useful directories were discovered

### 🧠 Important takeaway:

> Gobuster failure does NOT mean no attack surface exists.
> 
> 
> Some CMS platforms (like Bolt) use **known routes** instead of guessable directories.
> 

---

## 3️⃣ Manual Web Inspection

I manually browsed the web application on port **8000** and found a blog post written by the administrator:

> “Hello everyone,
> 
> 
> myself Jake and my username is **bolt** …
> 
> Regards,
> 
> Jake (Admin)”
> 

### 🧠 What this gives us:

- **Confirmed username:** `bolt`
- **CMS-based website** (content, admin post, structure)

This immediately suggested the use of a **Content Management System (CMS)**.

---

## 4️⃣ CMS Identification

By inspecting the page source and metadata, I discovered the CMS in use:

- **CMS:** Bolt
- **Version:** Bolt **3.7.1**

This was confirmed by:

- HTML source inspection
- CMS-specific paths such as `/bolt/login`

---

## 5️⃣ Login Page Discovery

Although Gobuster did not reveal it, I manually tested known CMS routes and found the admin login page at:

```
/bolt/login

```

### 🧠 Mindset lesson:

> When a CMS is identified, always try default CMS paths
> 
> 
> (e.g. `/admin`, `/login`, `/cms`, `/bolt/login`)
> 

---

## 6️⃣ Credential Discovery

From the content on the website, the administrator **leaked credentials**:

- **Username:** `bolt`
- **Password:** `boltadmin123`

Using these credentials, I successfully authenticated to the Bolt admin panel.

---

## 7️⃣ Vulnerability Research

Once authenticated and knowing the CMS version, I searched for public exploits:

- **Platform:** Exploit-DB
- **CMS:** Bolt 3.7.1
- **Exploit ID:** `48296`
- **Vulnerability:** Authenticated Remote Code Execution (RCE)

---

## 8️⃣ Exploitation (Metasploit)

I launched Metasploit and used the available module:

```bash
msfconsole
use exploit/unix/webapp/bolt_authenticated_rce

```

After configuring the required options and running the exploit, a **reverse shell** was obtained.

---

## 9️⃣ Post-Exploitation

Once the shell was established:

```bash
shell
whoami

```

I confirmed I had **root** access.

Finally, I retrieved the flag:

```bash
cat flag.txt

```

✅ **Flag successfully captured**

---

# 🧠 Key Learning Points (VERY IMPORTANT)

- Enumeration is **not only automated scanning**
- CMS attacks often rely on:
    - Manual inspection
    - Known paths
    - Version fingerprinting
- Gobuster failing is normal
- Knowing *what* the application is matters more than brute force