# Phishing Emails in Action

Learn the different indicators of phishing attempts by examining actual phishing emails.

---

# Phishing Emails in Action — TryHackMe

## 1. Core Idea

Phishing emails combine **social engineering** with technical techniques to make a malicious message look legitimate.

Common objectives:

- Steal credentials
- Make the victim click a malicious link
- Deliver malware through attachments
- Collect information
- Redirect the victim to a fake login page

**Analyst mindset:** Don't trust the branding or appearance. Investigate the **sender, headers, URLs, attachments, and actual destination**.

---

## 2. Common Phishing Techniques

### Spoofed / Fake Sender

The displayed sender name may look legitimate while the actual email address belongs to an unrelated domain.

Example:

```
Display name: PayPal Support
Actual address: noreply@randomdomain.xyz
```

**Check:** Display name ≠ proof of identity. Always inspect the real sender address/domain.

---

### Urgency

Attackers pressure the victim to act immediately.

Examples:

- "Your account will be suspended today."
- "Your payment must be verified immediately."
- "Your document expires today."
- "You must cancel this order."

**Why it works:** Urgency reduces the time the victim spends verifying the message.

---

### HTML Impersonation

Attackers reproduce the appearance of legitimate companies using:

- Logos
- Colors
- Buttons
- HTML layouts
- Fake receipts/forms

**Important:** A professional-looking email does NOT mean it is legitimate.

---

### Malicious / Suspicious Links

A link may visually say:

```
Cancel Order
```

while actually pointing to:

```
https://malicious-domain.xyz/...
```

Look for:

- Unrelated domains
- Typosquatting
- URL shorteners
- Suspicious redirects
- Links hidden behind buttons

**Always inspect the actual destination, not just the visible text.**

---

### URL Shortening

Shortened URLs hide the real destination.

Example:

```
https://bit.ly/xxxxx
        ↓
https://malicious-domain.xyz/login
```

Treat unexpected shortened URLs cautiously and expand/analyze them safely.

---

### Tracking Pixels

A phishing/spam email can contain a tiny invisible image.

When the victim's email client loads it:

```
Victim opens email
       ↓
Tracking image loads
       ↓
Attacker's server receives request
       ↓
Attacker learns the address is active
```

This can help attackers identify active email addresses for future targeting.

---

### Credential Harvesting

The victim is redirected to a fake login page:

```
Phishing email
      ↓
Fake Microsoft/Google/etc. login page
      ↓
Victim enters credentials
      ↓
Credentials sent to attacker
```

**Key point:** The page can look almost identical to the legitimate service. Check the **domain/URL**, not just the design.

---

### Malicious Attachments

Common suspicious attachments include:

- Office documents
- Macro-enabled documents
- PDFs containing malicious links
- Excel files
- Word templates such as `.DOT`

Example:

```
Phishing email
     ↓
Excel attachment
     ↓
Macro executes
     ↓
Malicious executable
```

A familiar file type does **not** mean the file is safe.

---

### BCC

**BCC = Blind Carbon Copy**

BCC allows an email to be sent to multiple recipients while hiding the recipient list from them.

Example:

```
To: attacker@example.com
BCC: Alice, Bob, Charlie
```

Alice doesn't see Bob and Charlie, and vice versa.

**Phishing relevance:** BCC can be used in mass phishing campaigns to hide the list of targets.

**Important:** BCC itself is NOT malicious.

---

### Poor Grammar / Typos

Grammar mistakes, inconsistent branding, strange wording, and misspelled company names can indicate phishing.

However:

> One typo alone ≠ phishing.
> 

Use it together with other indicators.

---

## 3. Phishing Email Investigation Checklist

When you receive a suspicious email, investigate in this order:

### 1. Sender

- Is the actual email address legitimate?
- Does the domain belong to the claimed organization?
- Is the display name different from the real address?

### 2. Recipient

- Was it sent directly to you?
- Are many recipients hidden using BCC?
- Is the recipient address unusual?

### 3. Subject / Psychology

Look for:

- Urgency
- Fear
- Curiosity
- Financial pressure
- Account suspension
- Unexpected purchases/deliveries

### 4. Links

Inspect:

- Actual URL
- Root domain
- Redirects
- Shortened URLs
- Typosquatting
- Domain mismatch

**Defang suspicious URLs when documenting them:**

```
https://devret.xyz
        ↓
hxxps://devret[.]xyz
```

### 5. Attachments

Ask:

- Is the attachment expected?
- What is the file extension?
- Does it contain macros?
- Does it contain links?
- Does it attempt to execute another file?

### 6. Email Source / Headers

The visible email is not enough.

Headers/source

- [Phishing Analysis Tools](https://tryhackme.com/room/phishingemails3tryoe)
- [Phishing Prevention](https://tryhackme.com/room/phishingemails4gkxh)
- [The Greenholt Phish](https://tryhackme.com/room/phishingemails5fgjlzxc)
- [Snapped Phish-ing Line](https://tryhackme.com/room/snappedphishingline)