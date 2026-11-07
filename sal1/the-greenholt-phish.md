# The Greenholt Phish

points,

X-Originating-IP
↓
Possible original client

Received:
↓
Mail-server path
↓
Find the earliest trustworthy hop
↓
Potential origin

1- What is the `Transfer Reference Number` listed in the email's **Subject** line?

![image.png](image%202.png)

2- What is the display name of the sender?

![image.png](image%203.png)

3-  Continue investigating the email headers. What is the sender's email address?

![image.png](image%204.png)

4- What email address will receive a reply to this email ?

![image.png](image%205.png)

5- Begin analyzing the message source. What is the originating IP address of this email?

The originating IP is **192.119.71.157**, identified from the earliest `Received:` header. It is the public IP of the server that established the SMTP connection with the mail server. The address **10.197.41.148** is a private/internal IP and therefore is not the public originating IP. The `X-Originating-IP` should not be prioritized here because the `Received:` chain provides stronger evidence of the actual SMTP connection.

![image.png](image%206.png)

6- nvestigate the IP address from the previous question. Who is the owner of the originating IP?

tool : [https://ipinfo.io/192.119.71.157?lookup_source=search-bar](https://ipinfo.io/192.119.71.157?lookup_source=search-bar)

![image.png](image%207.png)

7- Run an SPF record check on the Return-Path domain identified in the email headers.What is the full SPF record for this domain?

![image.png](image%208.png)

tool : [https://www.palisade.email/tools/txt-record-lookup?domain=mutawamarine.com](https://www.palisade.email/tools/txt-record-lookup?domain=mutawamarine.com)

![image.png](image%209.png)

v=spf1 include:spf.protection.outlook.com -all

It basically means:

> **"Only Microsoft's authorized email servers are allowed to send emails pretending to be from my domain. Everyone else should fail."**
> 

The email contains the authentication result:

`spf=fail smtp.mailfrom=mutawamarine.com`

The Return-Path domain is **mutawamarine.com**, whose SPF record is:

`v=spf1 include:spf.protection.outlook.com -all`

This SPF record states that **Microsoft's authorized mail servers are permitted to send emails on behalf of `mutawamarine.com`**. The `-all` mechanism means that any other sending server is considered unauthorized.

During the SPF check, the server that actually sent the email was compared against the domain's authorized SPF senders. The sending server's IP was **not authorized by the SPF record**, so the SPF check returned **FAIL**.

Therefore, although the email claims to come from `mutawamarine.com`, its actual sending infrastructure was not authorized to send mail for that domain. This is a strong indicator of **sender spoofing or unauthorized use of the domain**.

8- Perform a DMARC lookup for the Return-Path domain found in the email headers. What is the complete DMARC record for this domain?

tool: [https://mxtoolbox.com/SuperTool.aspx?action=dmarc%3Amutawamarine.com&run=toolpage](https://mxtoolbox.com/SuperTool.aspx?action=dmarc%3amutawamarine.com&run=toolpage)

![image.png](image%2010.png)

`v=DMARC1; p=quarantine; fo=1`

SPF:
FAIL ❌

DMARC:
p=quarantine

The domain is therefore telling receiving mail servers:

> "If an email claiming to be from my domain doesn't authenticate correctly, don't trust it normally; quarantine it."
> 

9- What is the file name of the attachment found in the email?

![image.png](image%2011.png)

10 -Download the attachment to your virtual environment.

Using the

```
sha256sum
```

HASH

```
SHA256 : 2e91c533615a9bb8929ac4bb76707b2444597ce063d84a4b33525e25074fff3f
```

11 - virustotal : 

![image.png](image%2012.png)

12- file type : rar