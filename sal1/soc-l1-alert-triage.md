# SOC L1 Alert Triage

L1 Role in Alert Tirage

soc l1 analyst are the first line of defence, wrk with alerts

- **SOC L1 analysts:**  Review the alerts, distinguish bad from good, and notify L2 analysts in case of a real threat
- **SOC L2 analysts:**  Receive the alerts escalated by L1 analysts and perform deeper analysis and remediation
- **SOC engineers:**  Ensure the alerts contain enough information required for efficient alert triage
- **SOC manager:**  Track speed and quality of alert triage to ensure that real attacks won't be missed

### Alerts Properties

| **№** | **Property** | **Description** | **Examples** |
| --- | --- | --- | --- |
| **1** | Alert Time | Shows alert creation time. Alert usually triggersa few minutes after the actual event | • Alert Time: March 21, 15:35
• Event Time: March 21, 15:32 |
| **2** | Alert Name | Provides a summary of what happened,based on the detection rule's name | • Unusual Login Location
• Email Marked as Phishing
• Windows RDP Bruteforce
• Potential Data Exfiltration |
| **3** | Alert Severity | Defines the urgency of the alert,initially set by detection engineers,but can be altered by analysts if needed | • (🟢) Low / Informational
• (🟡) Medium / Moderate
• (🟠) High / Severe
• (🔴) Critical / Urgent |
| **4** | Alert Status | Informs if somebody is working on the alertor if the triage is done | • (🆕) New / Unassigned
• (🔄) In Progress / Pending
• (✅) Closed / Resolved
• And often other custom statuses |
| **5** | Alert Verdict | Also called alert classification,explains if the alert is a real threat or noise | • (🔴) True Positive / Real Threat
• (🟢) False Positive / No Threat
• And often other custom verdicts |
| **6** | Alert Assignee | Shows the analyst that was assignedor assigned themselves to review the alert | • Assignee can sometimes be called alert owner
• Assignee takes responsibility for their alerts |
| **7** | Alert Description | Explains what the alert is about,usually in three sections on the right | • The logic of the alert generating rule
• Why this activity can indicate an attack
• Optionally, how to triage this alert |
| **8** | Alert Fields | Provides SOC analysts' commentsand values on which the alert was triggered | • Affected Hostname
• Entered Commandline
• And many more, depending on the alert |

## **Picking the Right Alert**

Every SOC team decides on its own prioritisation rules and usually automates them by setting the appropriate alert sorting logic in SIEM or EDR. Below, you may see the generic, simplest, and most commonly used approach:

1. **Filter the alerts**Make sure you don't take the alert that other analysts have already reviewed, or that is already being investigated by one of your teammates. You should only take new, yet unseen and unresolved alerts.
2. **Sort by severity**Start with critical alerts, then high, medium, and finally low. This is because detection engineers design rules so that critical alerts are much more likely to be real, major threats and cause much more impact than medium or low ones.
3. **Sort by time**Start with the oldest alerts and end with the newest ones. The idea is that if both alerts are about two breaches, the hacker from the older breach is likely already dumping your data, while the "newcomer" has just started the discovery.

### Alert Triage

![678ecc92c80aa206339f0f23-1773962582664.svg](678ecc92c80aa206339f0f23-1773962582664.svg)

**Alert triage = quickly investigating a security alert to determine:**

> **Is it a real threat, how serious is it, and what should we do about it?**
> 

In short:

**Alert → Investigate → Classify → Prioritize → Act/Escalate**