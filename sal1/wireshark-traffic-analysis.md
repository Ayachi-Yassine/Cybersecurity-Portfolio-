# Wireshark: Traffic Analysis

**Wireshark: Traffic Analysis**

| **Open TCP Port** | **Open TCP Port** | **Closed TCP Port** |
| --- | --- | --- |
| • SYN -->
• <-- SYN, ACK
• ACK --> | • SYN -->
• <-- SYN, ACK
• ACK -->
• RST, ACK --> | • SYN -->
• <-- RST, ACK |
|  |  |  |

# Nmap Scan

## Wireshark — Detecting TCP Port Scans

**Filter:**

```
tcp.flags.syn == 1 and tcp.flags.ack == 0 and tcp.window_size > 1024
```

**What it means:**

- `SYN == 1`: SYN flag is set; typically an initial TCP connection request.
- `ACK == 0`: ACK flag is not set in this packet; it does not tell us whether the target responded.
- `window_size > 1024`: Filters packets with a reported TCP window size greater than 1024. It is **not** the number of ports scanned.

**SOC investigation:** Look for one source IP sending SYN packets to many destination ports in a short time. This may indicate port scanning, but confirm using the wider traffic and responses.

**Remember:** SYN/ACK flags show connection behavior; repeated requests to many ports suggest a possible scan.

## **Nmap Scans**

Nmap is an industry-standard tool for mapping networks, identifying live hosts and discovering the services. As it is one of the most used network scanner tools, a security analyst should identify the network patterns created with it. This section will cover identifying the most common Nmap scan types.

- TCP connect scans
- SYN scans
- UDP scans

It is essential to know how Nmap scans work to spot scan activity on the network. However, it is impossible to understand the scan details without using the correct filters. Below are the base filters to probe Nmap scan behaviour on the network.

**TCP flags in a nutshell:**

| **Notes** | **Wireshark Filters** |
| --- | --- |
| Global search. | • `tcp`
• `udp` |
| • Only SYN flag.
• SYN flag is set. The rest of the bits are not important. | • `tcp.flags == 2`
• `tcp.flags.syn == 1` |
| • Only ACK flag.
• ACK flag is set. The rest of the bits are not important. | • `tcp.flags == 16`
• `tcp.flags.ack == 1` |
| • Only SYN, ACK flags.
• SYN and ACK are set. The rest of the bits are not important. | • `tcp.flags == 18`
• `(tcp.flags.syn == 1) and (tcp.flags.ack == 1)` |
| • Only RST flag.
• RST flag is set. The rest of the bits are not important. | 
• `tcp.flags == 4`
• `tcp.flags.reset == 1` |
| • Only RST, ACK flags.
• RST and ACK are set. The rest of the bits are not important. | • `tcp.flags == 20`
• `(tcp.flags.reset == 1) and (tcp.flags.ack == 1)` |
| • Only FIN flag
• FIN flag is set. The rest of the bits are not important. | • `tcp.flags == 1`
• `tcp.flags.fin == 1` |

## **TCP Connect Scans**

**TCP Connect Scan in a nutshell:**

- Relies on the three-way handshake (needs to finish the handshake process).
- Usually conducted with `nmap -sT` command.
- Used by non-privileged users (only option for a non-root user).
- Usually has a windows size larger than 1024 bytes as the request expects some data due to the nature of the protocol.

| **Open TCP Port** | **Open TCP Port** | **Closed TCP Port** |
| --- | --- | --- |
| • SYN -->
• <-- SYN, ACK
• ACK --> | • SYN -->
• <-- SYN, ACK
• ACK -->
• RST, ACK --> | • SYN -->
• <-- RST, ACK |

The images below show the three-way handshake process of the open and close TCP ports. Images and pcap samples are split to make the investigation easier and understand each case's details.

**Open TCP port (Connect):**

![Wireshark - tcp connect scan and open tcp port](https://cdn-images.tryhackme.com/user-uploads/66c44fd9733427ea1181ad58/room-content/66c44fd9733427ea1181ad58-1761652964480.png)

**Closed TCP port (Connect):**

![Wireshark - tcp connect scan and closed tcp port](https://cdn-images.tryhackme.com/user-uploads/66c44fd9733427ea1181ad58/room-content/66c44fd9733427ea1181ad58-1761652964467.png)

The above images provide the patterns in isolated traffic. However, it is not always easy to spot the given patterns in big capture files. Therefore analysts need to use a generic filter to view the initial anomaly patterns, and then it will be easier to focus on a specific traffic point. The given filter shows the TCP Connect scan patterns in a capture file.

`tcp.flags.syn==1 and tcp.flags.ack==0 and tcp.window_size > 1024`

![Wireshark - tcp connect scan pattern](https://cdn-images.tryhackme.com/user-uploads/66c44fd9733427ea1181ad58/room-content/66c44fd9733427ea1181ad58-1761652964562.png)

## **SYN Scans**

**TCP SYN Scan in a nutshell:**

- Doesn't rely on the three-way handshake (no need to finish the handshake process).
- Usually conducted with `nmap -sS` command.
- Used by privileged users.
- Usually have a size less than or equal to 1024 bytes as the request is not finished and it doesn't expect to receive data.

| **Open TCP Port** | **Close TCP Port** |
| --- | --- |
| • SYN -->
• <-- SYN,ACK
• RST--> | • SYN -->
• <-- RST,ACK |

**Open TCP port (SYN):**

![Wireshark - tcp syn scan and open port](https://cdn-images.tryhackme.com/user-uploads/66c44fd9733427ea1181ad58/room-content/66c44fd9733427ea1181ad58-1761652964475.png)

**Closed TCP port (SYN):**

![Wireshark - tcp syn scan and closed port](https://cdn-images.tryhackme.com/user-uploads/66c44fd9733427ea1181ad58/room-content/66c44fd9733427ea1181ad58-1761652964557.png)

The given filter shows the TCP SYN scan patterns in a capture file.

`tcp.flags.syn==1 and tcp.flags.ack==0 and tcp.window_size <= 1024`

![Wireshark - tcp syn scan pattern](https://cdn-images.tryhackme.com/user-uploads/66c44fd9733427ea1181ad58/room-content/66c44fd9733427ea1181ad58-1761652964520.png)

## **UDP Scans**

**UDP Scan in a nutshell:**

- Doesn't require a handshake process
- No prompt for open ports
- ICMP error message for close ports
- Usually conducted with `nmap -sU` command.

| **Open UDP Port** | **Closed UDP Port**strong> |
| --- | --- |
| • UDP packet --> | • UDP packet -->
• ICMP Type 3, Code 3 message. (Destination unreachable, port unreachable) |

**Closed (port no 69) and open (port no 68) UDP ports:**

![Wireshark - udp scan with open and closed ports](https://cdn-images.tryhackme.com/user-uploads/66c44fd9733427ea1181ad58/room-content/66c44fd9733427ea1181ad58-1761652964496.png)

The above image shows that the closed port returns an ICMP error packet. No further information is provided about the error at first glance, so how can an analyst decide where this error message belongs? The ICMP error message uses the original request as encapsulated data to show the source/reason of the packet. Once you expand the ICMP section in the packet details pane, you will see the encapsulated data and the original request, as shown in the below image.

![Wireshark - udp scan closed port and icmp packet encapsulation](https://cdn-images.tryhackme.com/user-uploads/66c44fd9733427ea1181ad58/room-content/66c44fd9733427ea1181ad58-1761652964616.gif)

The given filter shows the UDP scan patterns in a capture file.

`icmp.type==3 and icmp.code==3`

![Wireshark - udp port scan pattern](https://cdn-images.tryhackme.com/user-uploads/66c44fd9733427ea1181ad58/room-content/66c44fd9733427ea1181ad58-1761653529150.png)

Detecting suspicious activities in chunked files is easy and a great way to learn how to focus on the details. **Now use the `"~/Desktop/exercise-pcaps/nmap/Exercise.pcapng"` file to put your skills into practice against a single capture file and answer the questions below!**

QUESTIONS

What is the total number of the "TCP Connect" scans?

![image.png](image%2029.png)

How many "UDP close port" messages are there?

![image.png](image%2030.png)

which udp port in 55-70 is open 

![image.png](image%2031.png)

# **ARP Poisoning/Spoofing (A.K.A. Man In The Middle Attack)**

| **Notes** | **Wireshark filter** |
| --- | --- |
| Global search | • `arp` |
| **"ARP" options for grabbing the low-hanging fruits:**
• Opcode 1: ARP requests.
• Opcode 2: ARP responses.
• **Hunt:**Arp scanning
• **Hunt:**Possible ARP poisoning detection
• **Hunt:**Possible ARP flooding from detection: | • `arp.opcode == 1`
• `arp.opcode == 2`
• `arp.dst.hw_mac==00:00:00:00:00:00`
• `arp.duplicate-address-detected or arp.duplicate-address-frame`
• `((arp) && (arp.opcode == 1)) && (arp.src.hw_mac == target-mac-address)` |

How many arp request the attcker crafted

![image.png](image%2032.png)

What is the number of HTTP packets received by the attacker?

![image.png](image%2033.png)

What is the number of sniffed username&password entries?

![image.png](image%2034.png)

What is the password of the "Client986"?

![image.png](image%2035.png)

# **Identifying Hosts**

Protocols that can be used in Host and User identification:

- Dynamic Host Configuration Protocol () traffic
    
    DHCP
    
- NetBIOS (NBNS) traffic
- Kerberos traffic

## **DHCP Analysis**

**DHCP** protocol, or **D**ynamic **H**ost **C**onfiguration **P**rotocol **(DHCP),** is the technology responsible for managing automatic IP address and required communication parameters assignment.

**DHCP investigation in a nutshell:**

| **Notes** | **Wireshark Filter** |
| --- | --- |
| Global search. | • `dhcp`or`bootp` |
| Filtering the proper DHCP packet options is vital to finding an event of interest. 

• **"DHCP Request"**packets contain the hostname information
• **"DHCP ACK"**packets represent the accepted requests
• **"DHCP NAK"**packets represent denied requests
Due to the nature of the protocol, only "Option 53" ( request type) has predefined static values. You should filter the packet type first, and then you can filter the rest of the options by "applying as column" or use the advanced filters like "contains" and "matches". | • Request: `dhcp.option.dhcp == 3`
• ACK: `dhcp.option.dhcp == 5`
• NAK: `dhcp.option.dhcp == 6` |
| **"DHCP Request"**options for grabbing the low-hanging fruits:
• **Option 12:**Hostname.
• **Option 50:**Requested IP address.
• **Option 51:**Requested IP lease time.
• **Option 61:**Client's MAC address. | • `dhcp.option.hostname contains "keyword"` |
| **"DHCP ACK"**options for grabbing the low-hanging fruits:
• **Option 15:**Domain name.
• **Option 51:**Assigned IP lease time. | • `dhcp.option.domain_name contains "keyword"` |
| **"DHCP NAK"**options for grabbing the low-hanging fruits:
• **Option 56:**Message (rejection details/reason). | As the message could be unique according to the case/situation, It is suggested to read the message instead of filtering it. Thus, the analyst could create a more reliable hypothesis/result by understanding the event circumstances. |

![Wireshark - dhcp analysis](https://cdn-images.tryhackme.com/user-uploads/66c44fd9733427ea1181ad58/room-content/66c44fd9733427ea1181ad58-1761719722600.png)

## **NetBIOS (NBNS) Analysis**

**NetBIOS** or **Net**work **B**asic **I**nput/**O**utput **S**ystem is the technology responsible for allowing applications on different hosts to communicate with each other.

**NBNS investigation in a nutshell:**

| **Notes** | **Wireshark Filter** |
| --- | --- |
| Global search. | • `nbns` |
| **"NBNS"** options for grabbing the low-hanging fruits:
• **Queries:**Query details.
• Query details could contain **"name, Time to live (TTL) and IP address details"** | • `nbns.name contains "keyword"` |

![Wireshark - netbios analysis](https://cdn-images.tryhackme.com/user-uploads/66c44fd9733427ea1181ad58/room-content/66c44fd9733427ea1181ad58-1761719723566.png)

## **Kerberos Analysis**

**Kerberos** is the default authentication service for Microsoft Windows domains. It is responsible for authenticating service requests between two or more computers over the untrusted network. The ultimate aim is to prove identity securely.

**Kerberos investigation in a nutshell:**

| **Notes** | **Wireshark Filter** |
| --- | --- |
| Global search. | • `kerberos` |
| User account search:
• **CNameString:**The username.
**Note:** Some packets could provide hostname information in this field. To avoid this confusion, filter the **"$"**value. The values end with**"$"**are hostnames, and the ones without it are user names. | • `kerberos.CNameString contains "keyword"` 
• `kerberos.CNameString and !(kerberos.CNameString contains "$" )` |
| "Kerberos" options for grabbing the low-hanging fruits:
• **pvno:**Protocol version.
• **realm:**Domain name for the generated ticket.
• **sname:**Service and domain name for the generated ticket.
• **addresses:**Client IP address and NetBIOS name.
**Note: the "addresses" information is only available in request packets.** | • `kerberos.pvno == 5`
• `kerberos.realm contains ".org"` 
• `kerberos.SNameString == "krbtg"` |

![Wireshark - kerberos analysis](https://cdn-images.tryhackme.com/user-uploads/66c44fd9733427ea1181ad58/room-content/66c44fd9733427ea1181ad58-1761719722588.png)

-/—

QUESTIONS

What is the MAC address of the host "Galaxy A30"?

![image.png](image%2036.png)

How many NetBIOS registration requests does the "LIVALJM" workstation have?

![image.png](image%2037.png)

| Filter | Meaning |
| --- | --- |
| `nbns` | Show all NBNS packets |
| `nbns.flags.response == 0` | Requests only |
| `nbns.flags.response == 1` | Responses only |
| `nbns.name contains "LIVALJM"` | Packets containing this name |
| `nbns.flags.opcode == 5` | Registration packets |
| `nbns.name contains "LIVALJM" && nbns.flags.opcode == 5 && nbns.flags.response == 0` | Registration requests for `LIVALJM` |

Remember:

- Query = “Who has this name?”
- Registration = “I want to register this name.”
- Response `0` = Request; response `1` = Reply.

Which host requested the IP address "172.16.13.85"?

![image.png](image%2038.png)

What is the IP address of the user "u5"? (Enter the address in defanged format.)

![image.png](image%2039.png)

What is the hostname of the available host in the Kerberos packets?

![image.png](image%2040.png)

## Wireshark Kerberos Cheat Sheet

| Filter | Purpose |
| --- | --- |
| `kerberos` | Show Kerberos packets |
| `kerberos.CNameString contains "$"` | Find computer accounts |
| `kerberos.CNameString` | Inspect client name |
| `kerberos.SNameString` | Inspect service name |
| `kerberos.msg_type == 10` | Find AS-REQ packets |
| `kerberos.msg_type == 12` | Find AS-REP packets |

### Quick memory tips

- CNameString = Client name (user or computer account).
- SNameString = Service name.
- `$` at the end = Usually an Active Directory computer account.
- Hostname = Remove the trailing `$` from a computer account name, e.g. `PC01$` → `PC01`.

# **Tunnelling Traffic: ICMP and DNS**

| **Notes** | **Wireshark filters** |
| --- | --- |
| Global search | • `icmp` |
| **"ICMP" options for grabbing the low-hanging fruits:**
• Packet length.
• ICMP destination addresses.
• Encapsulated protocol signs in ICMP payload. | • `data.len > 64 and icmp` |

## **DNS Analysis**

Domain Name System (DNS) is designed to translate/convert IP domain addresses to IP addresses. It is also known as a phonebook of the internet. As it is the essential part of web services, it is commonly used and trusted, and therefore often ignored. Due to that, adversaries use it in data exfiltration and C2 activities.

**DNS analysis in a nutshell:**

Similar to ICMP tunnels, DNS attacks are anomalies appearing/starting after a malware execution or vulnerability exploitation. Adversary creates (or already has) a domain address and configures it as a C2 channel. The malware or the commands executed after exploitation sends DNS queries to the C2 server. However, these queries are longer than default DNS queries and crafted for subdomain addresses. <u>Unfortunately, these subdomain addresses are not actual addresses; they are encoded commands as shown below:</u>

**"encoded-commands.maliciousdomain.com"**

When this query is routed to the C2 server, the server sends the actual malicious commands to the host. As the DNS queries are a natural part of the networking activity, these packets have the chance of not being detected by network perimeters. A security analyst should know how to investigate the DNS packet lengths and target addresses to spot these anomalies.

| **Notes** | **Wireshark Filter** |
| --- | --- |
| Global search | • `dns` |
| **"DNS" options for grabbing the low-hanging fruits:**
• Query length.
• Anomalous and non-regular names in DNS addresses.
• Long DNS addresses with encoded subdomain addresses.
• Known patterns like dnscat and dns2tcp.
• Statistical analysis like the anomalous volume of DNS requests for a particular target.
**!mdns:** Disable local link device queries. | • `dns contains "dnscat"`
• `dns.qry.name.len > 15 and !mdns` |

![image.png](image%2041.png)

# Cleartext Protocol Analysis

## **FTP Analysis**

File Transfer Protocol (FTP) is designed to transfer files with ease, so it focuses on simplicity rather than security. As a result of this, using this protocol in unsecured environments could create security issues like:

- MITM attacks
- Credential stealing and unauthorised access
- Phishing
- Malware planting
- Data exfiltration

**FTP analysis in a nutshell:**

| **Notes** | **Wireshark Filter** |
| --- | --- |
| Global search | • `ftp` |
| **"FTP"** options for grabbing the low-hanging fruits:
• **x1x series:** Information request responses.
• **x2x series:** Connection messages.
• **x3x series:** Authentication messages.
**Note:** "200" means command successful. | **---** |
| **"x1x" series options for grabbing the low-hanging fruits:**
• **211:** System status.
• **212:** Directory status.
• **213:** File status | • `ftp.response.code == 211` |
| **"x2x" series options for grabbing the low-hanging fruits:**
• **220:** Service ready.
• **227:** Entering passive mode.
• **228:** Long passive mode.
• **229:** Extended passive mode. | • `ftp.response.code == 227` |
| **"x3x" series options for grabbing the low-hanging fruits:**
• **230:** User login.
• **231:** User logout.
• **331:** Valid username.
• **430:** Invalid username or password
• **530:** No login, invalid password. | • `ftp.response.code == 230` |
| **"FTP" commands for grabbing the low-hanging fruits:**
• **USER:** Username.
• **PASS:** Password.
• **CWD:** Current work directory.
• **LIST:** List. | • `ftp.request.command == "USER"`
• `ftp.request.command == "PASS"`
• `ftp.request.arg == "password"` |
| Advanced usages examples for grabbing low-hanging fruits:
• **Bruteforce signal:** List failed login attempts.
• **Bruteforce signal:** List target username.
• **Password spray signal:** List targets for a static password. | • `ftp.response.code == 530`
• `(ftp.response.code == 530) and (ftp.response.arg contains "username")`
• `(ftp.request.command == "PASS" ) and (ftp.request.arg == "password")` |

![Wireshark - ftp analysis](https://cdn-images.tryhackme.com/user-uploads/66c44fd9733427ea1181ad58/room-content/66c44fd9733427ea1181ad58-1761733955034.png)

QUESTIONS/ 

![image.png](image%2042.png)

![image.png](image%2043.png)

![image.png](image%2044.png)

# Http Analysis

## **HTTP Analysis**

Hypertext Transfer Protocol (HTTP) is a cleartext-based, request-response and client-server protocol. It is the standard type of network activity to request/serve web pages, and by default, it is not blocked by any network perimeter. As a result of being unencrypted and the backbone of web traffic, HTTP is one of the must-to-know protocols in traffic analysis. Following attacks could be detected with the help of HTTP analysis:

- Phishing pages
- Web attacks
- Data exfiltration
- Command and control traffic ()
    
    C2
    

**HTTP analysis in a nutshell:**

| **Notes** | **Wireshark Filter** |
| --- | --- |
| Global search
**Note:** HTTP2 is a revision of the HTTP protocol for better performance and security. It supports binary data transfer and request&response multiplexing. | • `http`
• `http2` |
| "HTTP **Request Methods"** for grabbing the low-hanging fruits:
• GET
• POST
• Request: Listing all requests | • `http.request.method == "GET"`
• `http.request.method == "POST"`
• `http.request` |
| **"HTTP Response Status Codes" for grabbing the low-hanging fruits:**
• **200 OK:** Request successful.
• **301 Moved Permanently:** Resource is moved to a new URL/path (permanently).
• **302 Moved Temporarily:** Resource is moved to a new URL/path (temporarily).
• **400 Bad Request:** Server didn't understand the request.
• **401 Unauthorised:** URL needs authorisation (login, etc.).
• **403 Forbidden:** No access to the requested URL. 
• **404 Not Found:** Server can't find the requested URL.
• **405 Method Not Allowed:** Used method is not suitable or blocked.
• **408 Request Timeout:**  Request look longer than server wait time.
• **500 Internal Server Error:** Request not completed, unexpected error.
• **503 Service Unavailable:** Request not completed server or service is down. | • `http.response.code == 200`
• `http.response.code == 401`
• `http.response.code == 403`
• `http.response.code == 404`
• `http.response.code == 405`
• `http.response.code == 503` |
| **"HTTP Parameters" for grabbing the low-hanging fruits:**
• **User agent:** Browser and operating system identification to a web server application.
• **Request URI:** Points the requested resource from the server.
• **Full *URI:** Complete URI information.
***URI:** Uniform Resource Identifier. | • `http.user_agent contains "nmap"`
• `http.request.uri contains "admin"`
• `http.request.full_uri contains "admin"` |
| **"HTTP Parameters" for grabbing the low-hanging fruits:**
• **Server:** Server service name.
• **Host:** Hostname of the server
• **Connection:** Connection status.
• **Line-based text data:** Cleartext data provided by the server.
• **HTML Form URL Encoded:** Web form information. | • `http.server contains "apache"`
• `http.host contains "keyword"`
• `http.host == "keyword"`
• `http.connection == "Keep-Alive"`
• `data-text-lines contains "keyword"` |

## **User Agent Analysis**

As the adversaries use sophisticated technics to accomplish attacks, they try to leave traces similar to natural traffic through the known and trusted protocols. For a security analyst, it is important to spot the anomaly signs on the bits and pieces of the packets. The "user-agent" field is one of the great resources for spotting anomalies in HTTP traffic. In some cases, adversaries successfully modify the user-agent data, which could look super natural. A security analyst cannot rely only on the user-agent field to spot an anomaly. Never whitelist a user agent, even if it looks natural. User agent-based anomaly/threat detection/hunting is an additional data source to check and is useful when there is an obvious anomaly. If you are unsure about a value, you can conduct a web search to validate your findings with the default and normal user-agent info ([**example site**](https://developers.whatismybrowser.com/useragents/explore/)).

**User Agent analysis in a nutshell:**

| **Notes** | **Wireshark Filter** |
| --- | --- |
| Global search. | • `http.user_agent` |
| **Research outcomes for grabbing the low-hanging fruits:**
• Different user agent information from the same host in a short time notice.
• Non-standard and custom user agent info.
• Subtle spelling differences. **("Mozilla" is not the same as  "Mozlilla" or "Mozlila")**
• Audit tools info like Nmap, Nikto, Wfuzz and sqlmap in the user agent field.
• Payload data in the user agent field. | • `(http.user_agent contains "sqlmap") or (http.user_agent contains "Nmap") or (http.user_agent contains "Wfuzz") or (http.user_agent contains "Nikto")` |

![Wireshark - http user agent analysis](https://cdn-images.tryhackme.com/user-uploads/66c44fd9733427ea1181ad58/room-content/66c44fd9733427ea1181ad58-1761745344894.png)

## **Log4j Analysis**

A proper investigation starts with prior research on threats and anomalies going to be hunted. Let's review the knowns on the "Log4j" attack before launching Wireshark.

**Log4j vulnerability analysis in a nutshell:**

| **Notes** | **Wireshark Filters** |
| --- | --- |
| **Research outcomes** for grabbing the low-hanging fruits:
• The attack starts with a "POST" request
• There are known cleartext patterns: "**jndi:ldap**" and "**Exploit.class**". | • `http.request.method == "POST"`
• `(ip contains "jndi") or ( ip contains "Exploit")`
• `(frame contains "jndi") or ( frame contains "Exploit")`
• `(http.user_agent contains "$") or (http.user_agent contains "==")` |

![Wireshark - http log4j analysis](https://cdn-images.tryhackme.com/user-uploads/66c44fd9733427ea1181ad58/room-content/66c44fd9733427ea1181ad58-1761745344924.png)

QUESTION

![image.png](image%2045.png)

![image.png](image%2046.png)

![image.png](image%2047.png)

# **Decrypting HTTPS Traffic**

![image.png](image%2048.png)

![image.png](image%2049.png)

![image.png](image%2050.png)

**ongratulations!** You just finished the "Wireshark: Traffic Analysis" room.

In this room, we covered how to use the Wireshark to detect anomalies and investigate events of interest at the packet level. Now, we invite you to complete the Wireshark challenge room: [**Carnage**](https://tryhackme.com/room/c2carnage), [**Warzone 1**](https://tryhackme.com/r/room/warzoneone) and [**Warzone 2**](https://tryhackme.com/r/room/warzonetwo).

Wireshark is a good tool for starting a network security investigation. However, it is not enough to stop the threats. A security analyst should have IDS/IPS knowledge and extended tool skills to detect and prevent anomalies and threats. As the attacks are getting more sophisticated consistently, the use of multiple tools and detection strategies becomes a requirement. The following rooms will help you step forward in network traffic analysis and anomaly/threat detection.

- [**NetworkMiner**](https://tryhackme.com/room/networkminer)
- [**Snort**](https://tryhackme.com/room/snort)
- [**Snort Challenge - The Basics**](https://tryhackme.com/room/snortchallenges1)
- [**Snort Challenge - Live Attacks**](https://tryhackme.com/room/snortchallenges2)
- [**Zeek**](https://tryhackme.com/room/zeekbro)
- [**Zeek Exercises**](https://tryhackme.com/room/zeekbroexercises)
- [**Brim**](https://tryhackme.com/room/brim)