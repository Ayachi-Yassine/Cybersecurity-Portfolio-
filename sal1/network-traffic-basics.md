# Network Traffic Basics

## What is the Purpose of Network Traffic Analysis

Scenario:

**DNS Tunneling and Beaconing**

You are an SOC analyst, and you receive an alert stating that an unusual number of DNS queries are coming from a host named WIN-016 with IP 192.168.1.16. The DNS logs on the firewall show multiple DNS queries going to the same TLD, each time using a different subdomain.

2025-10-03 09:15:23    SRC=192.168.1.16      [QUERY=aj39skdm.malicious-tld.com](http://query=aj39skdm.malicious-tld.com/)    QTYPE=A

2025-10-03 09:15:31    SRC=192.168.1.16      [QUERY=msd91azx.malicious-tld.com](http://query=msd91azx.malicious-tld.com/)    QTYPE=A

2025-10-03 09:15:45    SRC=192.168.1.16      [QUERY=cmd01.malicious-tld.com](http://query=cmd01.malicious-tld.com/)       QTYPE=TXT

2025-10-03 09:15:45    SRC=192.168.1.16      [QUERY=cmd01.malicious-tld.com](http://query=cmd01.malicious-tld.com/)       QTYPE=TXT

we can retrieve the following information:

- Query and querytype
- Subdomain and top-level domain: We can check tools like abuseDB or VirusTotal to check if the domain is malicious
- Host IP: We can identify the system sending out the DNS queries
- Destination IP: We can use tools like [AbuseIPDB(opens in new tab)](https://www.abuseipdb.com/) or [VirusTotal(opens in new tab)](https://virustotal.com/) to verify if the IP is flagged as malicious
- Timestamp: We can build a timeline mapping out the different suspicious queries

The DNS logs don't contain more information than that

From a SOC perspective, network traffic analysis helps:

- Detecting suspicious or malicious activity
- Reconstructing attacks during incident response
- Verifying and validating alerts

Below are two more scenarios that illustrate the importance of network traffic analysis:

- Based on the logs for an end-user system, the system began to deviate from its normal behavior around 4 PM . Analyzing the network traffic going to and from this system, we found a suspicious HTTP request and were able to extract a suspicious ZIP-file
    
    UTC
    
- We received an alert that an end-user system is sending many DNS requests in comparison to baseline of the network. After inspecting the DNS requests, we discovered that data was being exfiltrated using a technique called DNS tunneling

to evade ids , attackers use fragmentation attack , frament large packets to look legitimate 

**Session hijacking** can be detected by analyzing the **sequence numbers** included in the header. If the sequence numbers are suddenly far apart, further investigation is warranted.