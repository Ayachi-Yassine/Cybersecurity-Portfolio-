# SOC L1 Alert Reporting

#### ALETR Reporting

 : process if formaly describing alert details and findings

#### Alert Escalation

process of passing suspicious alerts to an L2 analyst for review

#### Communication

communicate with other department 

### Reporting Guide

| **Alert Report Purpose** | **Explanation** |
| --- | --- |
| Provide context for escalation | • A well-written report saves lots of time for L2 analysts
• Also, it helps them quickly understand what happened |
| Save findings for the records | • Raw SIEM logs are stored for 3-12 months, but alerts are kept indefinitely
• As a result, it's better to keep all the context inside the alert, just in case |
| Improve investigation skills | • If you can't explain it simply, you don't understand it well enough
• Report writing is a great way to boost L1 skills by summarising alerts |

## **Report Format**

![An example of good, structured report following the 5Ws approach](https://cdn-images.tryhackme.com/user-uploads/678ecc92c80aa206339f0f23/room-content/678ecc92c80aa206339f0f23-1743611080297.svg)

Imagine yourself as an L2 analyst, a DFIR team member, or an IT professional who needs to understand the alert. What would you want to see in the report? We recommend you follow the [**Five Ws(opens in new tab)**](https://en.wikipedia.org/wiki/Five_Ws) approach and include at least these items in the report:

- **Who**: Which user logs in, runs the command, or downloads the file
- **What**: What exact action or event sequence was performed
- **When**: When exactly did the suspicious activity start and ended
- **Where**: Which device, IP, or website was involved in the alert
- **Why**: The most important W, the reasoning for your final verdict

![678ecc92c80aa206339f0f23-1743611080297.svg](678ecc92c80aa206339f0f23-1743611080297.svg)

### Esclation

1. The alert is an indicator of a major cyberattack requiring deeper investigation or
    
    DFIR
    
2. Remediation actions like malware removal, host isolation, or password reset are required
3. Communication with customers, partners, management, or law enforcement agencies is required
4. You just do not fully understand the alert and need some help from more senior analysts

## **Communication Cases**

- **You need to escalate an urgent, critical alert, but L2 is unavailable and does not respond for 30 minutes.**
    
    Ensure you know where to find emergency contacts. First, try to call L2, then L3, and finally your manager.
    
- **The alert about Slack/Teams account compromise requires you to validate the login with the affected user.**
    
    Do not contact the user through the breached chat - use alternative contact methods like a phone call.
    
- **You receive an overwhelming number of alerts during a short period of time, some of which are critical.**
    
    Prioritise the alerts according to the workflow, but inform your L2 on shift about the situation.
    
- **After a few days, you realise that you misclassified the alert and likely missed a malicious action.**
    
    Immediately reach out to your L2 explaining your concerns. Threat actors can be silent for weeks before impact.
    
- **You can not complete the alert triage since the SIEM logs are not parsed correctly or are not searchable.**
    
    Do not skip the alert - investigate what you can and report the issue to your L2 on shift or SOC engineer.