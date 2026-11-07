# A Bucket of phish

DarkInjector has been using a Cmail phishing website to try to steal our credentials. We believe some of our users may have fallen for his trap. Can you retrieve the list of victim users?

Here's the link to the website: http://darkinjector-phish.s3-website-us-west-2.amazonaws.com

![image.png](image%2020.png)

so it is a fake login page

the phishing website is hosted using **Amazon S3**.

```jsx
aws s3 ls s3://darkinjector-phish --region us-west-2 --no-sign-request
```

- `aws` → AWS command-line tool
- `s3` → we're interacting with Amazon S3
- `ls` → **list** what's inside
- `s3://darkinjector-phish` → the bucket we're investigating
- `-region us-west-2` → tells AWS where the bucket is
- `-no-sign-request` → **don't use AWS credentials**; try accessing it as a public user

this command to show publicly available files in the bucket

we have two files 

```jsx
└─$ aws s3 ls s3://darkinjector-phish --region us-west-2 --no-sign-request
2025-03-17 07:46:17        132 captured-logins-093582390
2025-03-17 07:25:33       2300 index.html
                                                                             
┌──(ayachi㉿kali)-[~]
└─$ 

```

the index is probably the login page from above , now i will install the second file

```jsx
aws s3 cp s3://darkinjector-phish/captured-logins-093582390 . --region us-west-2 --no-sign-request
```

```jsx
┌──(ayachi㉿kali)-[~]
└─$ cat captured-logins-093582390 
user,pass
munra@thm.thm,Password123
test@thm.thm,123456
mario@thm.thm,Mario123
flag@thm.thm,THM{redacted}
                                                                             
┌──(ayachi㉿kali)-[~]

```

this is the flag : 

`THM{redacted}`

### What does `user,pass` mean?

That's the **CSV header**:

```
user,pass
```

It tells us each line has:

```
username/email , password
```

For example:

```
munra@thm.thm,Password123
```

means:

- **User:** `munra@thm.thm`
- **Password:** `Password123`

### 2. Who are the victims?

The captured users are:

```
munra@thm.thm
test@thm.thm
mario@thm.thm
flag@thm.thm
```

### 🧠 The important lesson from this room

The interesting part isn't just the flag. You learned a real cloud-security technique:

**Website → identify S3 → discover bucket → enumerate public objects → retrieve exposed data**

In your investigation:

```
Phishing website
       ↓
S3 website hostname
       ↓
S3 bucket: darkinjector-phish
       ↓
Public bucket listing
       ↓
captured-logins-093582390
       ↓
Victim credentials + flag
```

And importantly, you didn't need AWS credentials because the bucket allowed **anonymous/public access** (`--no-sign-request`).

thisn is a misconfig t