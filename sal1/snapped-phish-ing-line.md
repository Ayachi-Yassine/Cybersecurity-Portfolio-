# Snapped Phish-ing Line

## **Objectives**

- Analyze the provided email samples to identify key artifacts
- Investigate  URLs to understand redirection
    
    phishing
    
- Retrieve and examine the phishing kit used in the attack
- Use  tools to gather intelligence on the adversary
    
    CTI
    
- Analyze the phishing kit to uncover additional indicators

1 /2 

## 

![image.png](image%2013.png)

3- 

Investigate the attachment in the email addressed to Zoe Duncan.

What is the root domain of the redirection URL found within the file? -

![image.png](image%2014.png)

4- Open the attachment in your VM web browser.

Which company is the login page impersonating?

⇒ Microsoft

5- 

Let’s check if the attacker left any files exposed on the same website.

Navigate to the

```
/data
```

directory.

What is the name of the archive file?

![image.png](image%2015.png)

6- hash of the phishing kit archive

![image.png](image%2016.png)

7-  Investigate the file hash from the previous question using VirusTotal (opens in new tab).
Aside from phishing, what other threat category is assigned to the ZIP archive?

![image.png](image%2017.png)

the ZIP contains 49 file

8- 

Extract the phishing kit archive and locate the

```
submit.php
```

file.

What email address is used by the adversary to collect compromised credentials?

![image.png](image%2018.png)

![image.png](image%2019.png)