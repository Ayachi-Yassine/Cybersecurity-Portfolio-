# Pyramid Of Pain

## Punycode & URL Shortener Attacks

### 1. Punycode / IDN

**Punycode** is a method of representing Unicode characters in domain names using ASCII-compatible characters.

Example:

`adıdas.de` → `xn--addas-o4a.de`

Attackers can abuse Unicode characters that visually resemble normal characters to create **lookalike domains** (homograph attacks).

Example concept:

`legitimate-domain.com`

↓ visually similar Unicode characters

`malicious-lookalike-domain.com`

### Why attackers use Punycode

- Make malicious domains look legitimate.
- Trick users into clicking phishing links.
- Create fake login pages and steal credentials.
- Hide the real characters used in the domain.

### Detection

Look for suspicious domains containing:

`xn--`

Useful sources:

- Proxy logs
- Web server logs
- DNS logs
- SIEM searches

When investigating a suspicious domain, don't rely only on how it visually appears. Inspect the actual hostname and domain representation.

---

## 2. URL Shorteners

A URL shortener creates a short URL that redirects the user to another destination.

Example:

`https://tinyurl.com/xxxxx`

↓

`https://destination.com/`

### Why attackers use URL shorteners

They can hide the real destination of a malicious link.

Possible phishing chain:

User → Short URL → Redirect → Malicious website → Credential theft

Common URL-shortening services encountered in the room:

- bit.ly
- goo.gl
- ow.ly
- s.id
- smarturl.it
- tiny.pl
- tinyurl.com
- x.co

### Previewing a TinyURL

For TinyURL, adding `+` to the shortened URL can display a preview page:

`https://tinyurl.com/xxxxx`

↓

`https://tinyurl.com/xxxxx+`

The preview can reveal the redirect destination without immediately visiting the final website.

---

## 3. Redirect Chains

A shortened URL may pass through multiple redirects.

Example from this room:

`tinyurl.com/bw7t8p4u`

↓

`redirect.viglink.com`

↓

`https://tryhackme.com/`

The preview showed:

`u=https%3A%2F%2Ftryhackme.com%2F`

The `u=` parameter contained the encoded destination.

---

## 4. URL / Percent Encoding

paste the result into a url decoder

```
python3 -c "import urllib.parse; print(urllib.parse.unquote('https%3A%2F%2Ftryhackme.com%2F'))"
```

---

## SOC Investigation Mindset

Don't trust a URL simply because it **looks legitimate**.

When investigating a suspicious link:

1. Identify the actual hostname.
2. Check for Punycode / `xn--`.
3. Check whether the URL is shortened.
4. Inspect the redirect chain.
5. Decode URL-encoded parameters.
6. Check proxy/DNS/web logs for user activity.
7. Investigate the final destination/domain.

### Key takeaway

**URL appearance ≠ actual destination.**

Always investigate the underlying domain, redirects, and encoded URL parameters.

## Host Artifacts

⇒ Host artifacts are the traces or observables that attackers leave on the system, such as registry values, suspicious process execution, attack patterns or IOCs (Indicators of Compromise), files dropped by malicious applications, or anything exclusive to the current threat.

---

anyrun

yellow zone

### Network Artifacts

A **network artifact** is a piece of information in network traffic that can help identify suspicious or malicious activity.