# Relevent

```jsx
──(ayachi㉿kali)-[~]
└─$ nmap -sV -Pn 10.67.129.198
Starting Nmap 7.94SVN ( https://nmap.org ) at 2026-02-14 12:29 CET
Nmap scan report for 10.67.129.198
Host is up (0.15s latency).
Not shown: 995 filtered tcp ports (no-response)
PORT     STATE SERVICE            VERSION
80/tcp   open  http               Microsoft IIS httpd 10.0
135/tcp  open  msrpc?
139/tcp  open  netbios-ssn        Microsoft Windows netbios-ssn
445/tcp  open  microsoft-ds       Microsoft Windows Server 2008 R2 - 2012 microsoft-ds
3389/tcp open  ssl/ms-wbt-server?
Service Info: OSs: Windows, Windows Server 2008 R2 - 2012; CPE: cpe:/o:microsoft:windows

Service detection performed. Please report any incorrect results at https://nmap.org/submit/ .
Nmap done: 1 IP address (1 host up) scanned in 123.82 seconds

└─$ nmap -p- -Pn 10.67.137.105 --min-rate 5000
Starting Nmap 7.94SVN ( https://nmap.org ) at 2026-02-14 14:26 CET
Nmap scan report for 10.67.137.105
Host is up (0.14s latency).
Not shown: 65527 filtered tcp ports (no-response)
PORT      STATE SERVICE
80/tcp    open  http
135/tcp   open  msrpc
139/tcp   open  netbios-ssn
445/tcp   open  microsoft-ds
3389/tcp  open  ms-wbt-server
49663/tcp open  unknown
49666/tcp open  unknown
49667/tcp open  unknown

```

```jsx
┌──(ayachi㉿kali)-[~]
└─$ smbclient -L //10.67.129.198/ -N

	Sharename       Type      Comment
	---------       ----      -------
	ADMIN$          Disk      Remote Admin
	C$              Disk      Default share
	IPC$            IPC       Remote IPC
	nt4wrksv        Disk      
Reconnecting with SMB1 for workgroup listing.
do_connect: Connection to 10.67.129.198 failed (Error NT_STATUS_RESOURCE_NAME_NOT_FOUND)
Unable to connect with SMB1 -- no workgroup available

```

we got a custom share nt4wrksv , this kind of shares are often misconfigured aand my contains somthing useful 

so lets explore that 

```jsx
└─$ smbclient //10.67.129.198/nt4wrksv -N
Try "help" to get a list of possible commands.
smb: \> ls
  .                                   D        0  Sat Jul 25 22:46:04 2020
  ..                                  D        0  Sat Jul 25 22:46:04 2020
  passwords.txt                       A       98  Sat Jul 25 16:15:33 2020

		7735807 blocks of size 4096. 5100430 blocks available
		get passwords.txt
```

gthis is what inside passwords.txt

```jsx
┌──(ayachi㉿kali)-[~]
└─$ cat passwords.txt   
[User Passwords - Encoded]
Qm9iIC0gIVBAJCRXMHJEITEyMw==
QmlsbCAtIEp1dzRubmFNNG40MjA2OTY5NjkhJCQk                                                                                                                    
┌──(ayachi㉿kali)-[~]
└─$ echo "Qm9iIC0gIVBAJCRXMHJEITEyMw==" | base64 -d                          
Bob - !P@$$W0rD!123                                                                                                                    
┌──(ayachi㉿kali)-[~]
└─$ echo "QmlsbCAtIEp1dzRubmFNNG40MjA2OTY5NjkhJCQk" | base64 -d
Bill - Juw4nnaM4n420696969!$$$                                                                                                                    
┌──(ayachi㉿kali)-[~]
                                                                                                                 
```