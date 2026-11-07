# Vulnerabilities 101

---

| **Vulnerability** | **Description** |
| --- | --- |
| Operating System | These types of vulnerabilities are found within Operating Systems (OSs) and often result in privilege escalation. |
| (Mis)Configuration-based | These types of vulnerability stem from an incorrectly configured application or service. For example, a website exposing customer details. |
| Weak or Default Credentials | Applications and services that have an element of authentication will come with default credentials when installed. For example, an administrator dashboard may have the username and password of "admin". These are easy to guess by an attacker. |
| Application Logic | These vulnerabilities are a result of poorly designed applications. For example, poorly implemented authentication mechanisms that may result in an attacker being able to impersonate a user. |
| Human-Factor | Human-Factor vulnerabilities are vulnerabilities that leverage human behaviour. For example, phishing emails are designed to trick humans into believing they are legitimate. |

## Scoring Vulnerabilities (CVSS & VPR)

- Common Vulnerability Scoring System (CVSS)
- <u>**Vulnerability Priority Rating (VPR)**</u>

## Vulnerability Databases

- [NVD (National Vulnerability Databas](https://nvd.nist.gov/vuln)e)
- [Exploit-DB](http://exploit-db.com/)

| **Term** | **Definition** |
| --- | --- |
| Vulnerability | A vulnerability is defined as a weakness or flaw in the design, implementation or behaviours of a system or application. |
| Exploit | An exploit is something such as an action or behaviour that utilises a vulnerability on a system or application. |
| Proof of Concept (PoC) | A PoC is a technique or tool that often demonstrates the exploitation of a vulnerability. |
|  |  |

# 📝 Vulnerability: Version Disclosure

**Category:** Information Disclosure → Version Disclosure

**Definition:**

A **Version Disclosure** vulnerability occurs when an application, service, or software **reveals its name and version number** to an external user. This information can be used to identify **specific vulnerabilities** for that version.

---

**Why it matters:**

- Software versions correlate to known vulnerabilities (CVEs).
- Attackers can search vulnerability databases (e.g., **Exploit-DB, NVD**) using this information.
- What seems like minor information leakage can lead to **high-value exploits**.

---

**Example:**

- Application response shows: `Apache Tomcat 9.0.17`
- Using this version info, you search Exploit-DB → find multiple applicable exploits.

---

**Pentester Mindset:**

1. Always collect **information leakage** carefully.
2. Map **small findings → bigger exploit potential**.
3. Chain vulnerabilities: Minor leaks → version → exploit → access.

---

**Key Takeaways:**

- **Original vulnerability:** Version Disclosure
- **Category:** Information Disclosure
- **Next step:** Use version info to search CVEs / Exploit-DB
- **Remember:** Information leaks are the **first step in enumeration and exploi**