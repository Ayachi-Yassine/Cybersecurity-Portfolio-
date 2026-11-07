# SOC Metrics and Objectives

o begin with, let's recall the main goal of a SOC - to protect the confidentiality, integrity, and availability of the organisation's digital assets. The SOC team performs its purpose by developing, receiving, and triaging alerts. The L1 analysts' role in this process is to reliably report True Positives to the higher level, to L2. This leads us to the first four metrics:

| **Metric** | **Formula** | **Measures** |
| --- | --- | --- |
| Alerts Count | `AC = Total Count of Alerts Received` | Overall load of SOC analysts |
| False Positive Rate | `FPR = False Positives / Total Alerts` | Level of noise in the alerts |
| Alert Escalation Rate | `AER = Escalated Alerts / Total Alerts` | Experience of L1 analysts |
| Threat Detection Rate | `TDR = Detected Threats / Total Threats` | Reliability of the SOC team |

fp max 80%

**Alerts Count                    : 5 to 30 alerts per day per L1 analyst is a good metric**

**False Positive Rate         :**A False Positive rate of **0% is an unachievable ideal, but 80% or higher is a serious problem**

**Alert Escalation Rate     :aimed to be below 50%, or even better below**

**Threat Detection Rate  : hould always be at 100%**

![image.png](image.png)

| **Metric** | **Common SLA** | **Description** |
| --- | --- | --- |
| SOC Team Availability | 24/7 | Working schedule of the SOC team, often Monday-Friday (8/5) or 24/7 mode |
| Mean Time to Detect (MTTD) | 5 minutes | Average time between the attack and its detection by SOC tools |
| Mean Time to Acknowledge (MTTA) | 10 minutes | Average time for L1 analysts to start triage of the new alert |
| Mean Time to Respond (MTTR) | 60 minutes | Average time taken by SOC to actually stop the breach from spreading |