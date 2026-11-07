# Introduction to EDR

Endpoint detection and response (EDR) is a series of tools that monitor devices for activity that could indicate a threat.

Below are some of the EDR solutions in the market:

- [CrowdStrike Falcon(opens in new tab)](https://www.crowdstrike.com/wp-content/uploads/2022/03/crowdstrike-falcon-insight-data-sheet.pdf)
- [Symantec EDR(opens in new tab)](https://docs.broadcom.com/doc/endpoint-detection-and-response-atp-endpoint-en)
- [SentinelOne EDR(opens in new tab)](https://sentinelone.com/resources/datasheets/assets/usecase/sentinel-one-active-#page=1)
- [Microsoft Defender for Endpoint](https://learn.microsoft.com/en-us/defender-endpoint/microsoft-defender-endpoint)
    
    edr uses agents (sensors) collect data from endpoints and send it to edr console 
    
    this data is called  Telemetry and it contain  everything necessary for detection and investigation.
    

## EDR – Endpoint Telemetry

EDR continuously collects detailed telemetry from endpoints to **detect, investigate, and respond to threats**.

### What EDR monitors

- **Process activity:** Tracks process creation/termination and parent-child relationships to detect suspicious executions and malware.
- **Network connections:** Monitors connections to detect **C2 communication, unusual ports, data exfiltration, and lateral movement**.
- **Command-line activity:** Records CMD, PowerShell, etc. to detect malicious or obfuscated commands.
- **Files & folders:** Tracks file creation/modification/deletion, useful for detecting **malware drops, data staging, and ransomware**.
- **Registry:** Monitors Windows Registry changes that may indicate malicious activity or persistence.

### Why EDR telemetry matters

Attackers often use legitimate tools, making individual actions look harmless. EDR **correlates multiple activities** using detection logic and machine learning to identify suspicious behavior.

For SOC analysts, this telemetry helps to:

- Reconstruct the **attack timeline**
- Identify the **root cause**
- Understand the **full attack chain**
- Determine whether activity is malicious or legitimate
- Investigate and respond to alerts