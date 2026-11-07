# Introduction to SIEM

SEIM : 

Security Information and Event Management system

Two type of logs ;

**1) Host-Centric Log Sources :** 

**These log sources capture events that occurred within or related to the host. Devices that generate host-centric logs include Windows, Linux, servers, etc.**

**1) Host-Centric Log Sources :**

 **Network-related logs are generated when the hosts communicate with each other or access the internet to visit a website. Devices that generate network-centric logs are firewalls, IDS/IPS, routers, etc**

## **Log Ingestion**

All these logs provide a wealth of information and can help identify security issues. Each SIEM solution has its own way of ingesting the logs. Some common methods used by these SIEM solutions are explained below:

1. Agent / ForwarderThese SIEM solutions provide a lightweight tool called an agent (forwarder by ) that gets installed on the Endpoint. It is configured to capture and send all the important logs to the SIEM server.
    
    Splunk
    
2. **Syslog**Syslog is a widely used protocol to collect data from various systems like web servers, databases, etc., and send real-time data to the centralized destination.
3. **Manual Upload**Some SIEM solutions, like Splunk, , etc., allow users to ingest offline data for quick analysis. Once the data is ingested, it is normalized and made available for analysis.
    
    ELK
    
4. **Port-Forwarding**SIEM solutions can also be configured to listen on a certain port, and then the endpoints forward the data to the SIEM instance on the listening port.

## **Linux Machine**

Linux OS stores all the related logs, such as events, errors, warnings, etc. These are then ingested into SIEM for continuous monitoring. Some of the common locations where Linux stores logs are:

- /var/log/httpd: Contains HTTP Request / Response and error logs.
- /var/log/cron: Events related to cron jobs are stored in this location.
- /var/log/auth.log and /var/log/secure: Stores authentication-related logs.
- /var/log/kern: This file stores kernel-related events.

**Windows Machine**

event viewer

rooms ro do 

- [Junior Security Analyst Intro](https://tryhackme.com/room/jrsecanalystintrouxo)
- [Splunk: The Basics](https://tryhackme.com/room/splunk101)
- [Incident Handling with Splunk](https://tryhackme.com/room/splunk201)
- [Benign](https://tryhackme.com/room/benign)
- [Investigating with Splunk](https://tryhackme.com/room/investigatingwithsplunk)
- [Investgating with ELK](https://tryhackme.com/room/investigatingwithelk101)
- [ItsyBitsy](https://tryhackme.com/room/itsybitsy)