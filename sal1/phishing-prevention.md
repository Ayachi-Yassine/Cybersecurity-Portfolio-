# Phishing Prevention

---

# Sender Policy Framework (SPF)

```
# SPF — Sender Policy Framework

**SPF** is an email authentication mechanism that helps prevent **sender-domain spoofing**.

### How it works
A domain publishes an SPF record in **DNS** listing which mail servers/IPs are allowed to send emails for that domain.

**Example:**
```text
company.com TXT
v=spf1 ip4:203.0.113.10 -all
```

- `v=spf1` → SPF record
- `ip4:203.0.113.10` → authorized sending server
- `all` → all other servers fail SPF

```
                📧 EMAIL
                   │
                   │ From: alice@example.com
                   ↓
          ┌─────────────────┐
          │ Receiving Server │
          │     (Gmail)      │
          └────────┬────────┘
                   │
                   │ 1. What domain?
                   ↓
             example.com
                   │
                   │ 2. Ask DNS:
                   │ "What is the SPF record?"
                   ↓
                ┌─────┐
                │ DNS │
                └──┬──┘
                   │
                   │ SPF Record
                   ↓
      ┌──────────────────────────┐
      │ example.com              │
      │                          │
      │ v=spf1                   │
      │ ip4:203.0.113.10         │
      │ -all                     │
      └────────────┬─────────────┘
                   │
                   │ 3. Gmail compares
                   │
                   ↓
          Actual sending IP
                   │
          ┌────────┴────────┐
          │                 │
          ↓                 ↓
   203.0.113.10       198.51.100.50
   Company Server        Attacker
          │                 │
          │                 │
          ↓                 ↓
    In SPF list?       In SPF list?
          │                 │
         YES                NO
          │                 │
          ↓                 ↓
      SPF PASS ✅        SPF FAIL ❌
          │                 │
          └────────┬────────┘
                   ↓
          Receiving server
          decides what to do
      (accept / spam / reject)
```

Domain owner → publishes authorized mail servers in DNS → receiving server checks the actual sending IP → compares it with SPF → PASS or FAIL.

So, in essence, when an email is sent, the receiving mail server checks the domain's SPF record to verify whether the sending server is authorized to send messages on behalf of that domain. The delivery of the email (intended action) is based on the result of the SPF record verification.

| **Verification Result** | **Intended Action** |
| --- | --- |
| Pass, Neutral, None | Accept (Allow and process the email) |
| SoftFail, PermError | Flag (Mark as suspicious but allow) |
| Fail, TempError | Reject (Immediately discard the email) |

## **SPF Records**

Let's take a look at a sample SPF record and break down its format. Further information on SPF Record Syntax can be found [here(opens in new tab)](https://dmarcian.com/spf-syntax-table/).

`v=spf1 ip4:127.0.0.1 include:_spf.google.com -all`

- `v=spf1` Signifies the start of the SPF record
- `ip4:127.0.0.1` Specifies which IP can send mail (IPv4 in this case)
- `include:_spf.google.com` Specifies which domain can send mail
- `all` Non-authorized emails will be rejected

## **Tools**

The [**SPF Surveyor(opens in new tab)**](https://dmarcian.com/spf-survey/) tool from dmarcian enables us to gain a visual look at DNS records. It also helps ensure the record uses the correct syntax.

Let's take a look at **TryHackMe**'s SPF record as an example.

![A screenshot of tryhackme.com's SPF record taken from the demarcian SPF surveyor tool. The record shows the domains that are allowed to send emails on behalf of TryHackMe.](https://cdn-images.tryhackme.com/user-uploads/616945d482ef350052080da1/room-content/616945d482ef350052080da1-1759814814615.svg)

You may notice that no IP addresses are visibly present in the main record, but three domains are listed. In this case, all IP addresses authorized by the domains will be recognized as legitimate senders.

- `_spf.google.com`
- `email.chargebee.com`
- `7168674.spf05.hubspotemail.net`

**Google Admin Toolbox [Messageheader**(opens in new tab)](https://toolbox.googleapps.com/apps/messageheader/) allows you to analyze delivery details using an email's full header. It shows various record results, including SPF. Check out the example below, which shows the SPF record as `softfail with IP Unknown!`. A `SoftFail` means the sending mail server is not listed as an authorized sender, but the receiving server will still accept the email and flag it as suspicious. In this case, the sending IP address could not be verified.

# **DomainKeys Identified Mail** ([**DKIM](https://dmarcian.com/what-is-dkim/))**

DKIM is an email authentication mechanism that uses **cryptographic signatures**.

- The sender's mail server signs the email using a **private key**.
- The corresponding **public key is published in DNS**.
- The receiving server retrieves the public key and verifies the signature.

### Purpose

- Verify that the email was signed by the claimed domain.
- Detect modification/tampering of signed parts of the email.

### Example

```
Email → Private Key → DKIM Signature
                         ↓
                    Gmail checks
                         ↓
              Public Key from DNS
                    ↙         ↘
                 Valid       Invalid
                 PASS ✅      FAIL ❌
```

# **Domain-Based Message Authentication, Reporting, and Conformance** ([**DMARC](https://dmarcian.com/getting-started-with-dmarc/))**

This means that DMARC ensures the sender's domain matches the domains verified by SPF and DKIM. If the alignment fails, DMARC instructs the recipient server on how to handle the email based on a policy specified in the record.

# **Secure/Multipurpose Internet Mail Extensions** ([**S/MIME](https://learn.microsoft.com/en-us/exchange/security-and-compliance/smime-exo/smime-exo))**

A standard protocol for sending digitally signed and encrypted messages. It is based on public key cryptography, where the private key is never shared and the public key can be distributed openly. 

The sender signs the message with their private key, the recepient verifies the sender's identity using the sender's public key.

in wireshark : use filter smtp.response.code == 550 to look fro smpt response

250 → OK / accepted ✅
550 → Mailbox unavailable / rejected ❌ 553
451 → Temporary error ⚠️

 552 t3aba

## **Technical Defenses**

Modern email systems employ various technical controls to help detect and block phishing messages before they reach users.

- [**Email Filtering**(opens in new tab)](https://www.spamhaus.org/resource-hub/ip-domain-reputation/): ****Provides filtering based on IP and domain reputation, allowing for blocking or quarantining of suspicious messages.
- [**Secure Email Gateways**(opens in new tab)](https://www.cloudflare.com/learning/email-security/secure-email-gateway-seg/) (SEGs): Scan messages to detect impersonation attempts, spoofing, and other phishing techniques that other filters might miss.
- [**Link Rewriting**(opens in new tab)](https://learn.microsoft.com/en-us/defender-office-365/safe-links-about): Replaces suspicious or unknown URLs with safe, redirected ones, giving the system time to scan and verify the link.
- [**Sandboxing**(opens in new tab)](https://learn.microsoft.com/en-us/defender-office-365/safe-attachments-about): Isolates and tests suspicious links or attachments in a secure, virtual environment to check for malicious behavior.