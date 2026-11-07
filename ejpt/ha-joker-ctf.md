# HA JOKER CTF

first enumeration

```jsx
──(ayachi㉿kali)-[~]
└─$ nmap -sC -sV -Pn 10.67.189.184           
Starting Nmap 7.94SVN ( https://nmap.org ) at 2026-02-13 18:38 CET
Nmap scan report for 10.67.189.184
Host is up (0.14s latency).
Not shown: 997 closed tcp ports (reset)
PORT     STATE SERVICE VERSION
22/tcp   open  ssh     OpenSSH 8.2p1 Ubuntu 4ubuntu0.13 (Ubuntu Linux; protocol 2.0)
| ssh-hostkey: 
|   3072 8f:12:cb:29:f6:db:89:e0:80:d6:67:91:c4:24:c4:02 (RSA)
|   256 17:cf:73:b5:56:9c:83:4e:74:16:72:08:45:d8:4f:af (ECDSA)
|_  256 81:49:97:4a:28:83:36:0a:0b:76:ac:a4:1e:26:1c:e4 (ED25519)
80/tcp   open  http    Apache httpd 2.4.41 ((Ubuntu))
|_http-title: HA: Joker
|_http-server-header: Apache/2.4.41 (Ubuntu)
8080/tcp open  http    Apache httpd 2.4.41
| http-auth: 
| HTTP/1.1 401 Unauthorized\x0D
|_  Basic realm=Please enter the password.
|_http-title: 401 Unauthorized
|_http-server-header: Apache/2.4.41 (Ubuntu)
Service Info: Host: localhost; OS: Linux; CPE: cpe:/o:linux:linux_kernel

```