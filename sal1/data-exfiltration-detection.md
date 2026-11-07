# Data Exfiltration Detection

## Task 3 — Data Exfil: Overview, Techniques, and Indicators

### Objective

Understand the motivations, lifecycle, techniques, and detection indicators associated with unauthorized data transfers.

### Why attackers exfiltrate data

- **Financial gain:** Sell stolen personal, financial, or corporate information.
- **Espionage:** Steal intellectual property, trade secrets, or sensitive government information.
- **Ransomware and extortion:** Steal data before encrypting systems and threaten to leak it.
- **Disruption and sabotage:** Damage an organization's operations or reputation.
- **Reconnaissance and future operations:** Obtain information useful for further attacks.

### Exfiltration lifecycle

1. Discovery and collection — identify and gather valuable files.
2. Staging — aggregate, compress, encrypt, or encode collected data.
3. Exfiltration transport — transfer data through a network, cloud service, removable media, or covert channel.
4. C2 coordination — direct and monitor the operation; this can overlap with other phases.

### Major techniques

| Technique | Potential indicators |
| --- | --- |
| DNS tunneling | Long or encoded-looking query names, unusual query volume, abnormal TXT queries |
| HTTP/HTTPS | Unusual uploads, large POST requests, unexpected destinations |
| FTP/SFTP/SCP | Unexpected accounts, suspicious file transfers, unusual sessions |
| ICMP covert channel | Unusual packet sizes, repeated echo requests, suspicious payloads |
| Cloud storage | Unexpected uploads, unusual API calls, external sharing |
| Removable media | Unexpected file copies or USB activity |

### Detection and triage

Collect and correlate:

- Source host, user, and timestamp.
- Destination IP, domain, service, and protocol.
- Outbound bytes, transfer duration, frequency, and request sizes.
- Process name, command line, and file-access activity.
- Related DNS, firewall, proxy, endpoint, authentication, and cloud audit events.

### Analyst reasoning

A high outbound data volume or unfamiliar domain is a clue, not proof of malicious activity. Establish a baseline, investigate the destination and initiating process, consider legitimate explanations, and correlate independent evidence.

### Practical lesson

Do not rely on one alert. Build a timeline connecting file access, staging activity, and outbound transfers. State what is observed, what is inferred, and what remains unconfirmed.

# TryHackMe — Data Exfiltration Detection

**Pathway:** SOC Level 1 (SAL1)

**Category:** Security Operations / Network Traffic Analysis

**Tools:** Wireshark, Splunk, PCAP analysis

**Status:** Room content reviewed; answer values to be verified in the lab

---

## 1. Learning objectives

- Understand the motivations and lifecycle of data exfiltration.
- Recognize network-based, host-based, cloud-based, and covert exfiltration techniques.
- Detect suspicious DNS tunneling, FTP transfers, HTTP uploads, and ICMP traffic.
- Investigate PCAP files using Wireshark display filters and TCP stream analysis.
- Use Splunk SPL to aggregate, filter, and investigate network logs.
- Correlate network and SIEM evidence.
- Distinguish suspicious activity from confirmed malicious activity.

## 2. Data exfiltration overview

### Definition

Data exfiltration is the unauthorized transfer of sensitive information from an organization to an external destination or another location controlled by an adversary.

It can occur through malware, compromised credentials, malicious insiders, cloud services, removable media, or covert communication channels.

### Why attackers exfiltrate data

- **Financial gain:** Sell personal records, credit card details, or corporate information.
- **Espionage:** Steal intellectual property, trade secrets, or classified information.
- **Ransomware and extortion:** Steal data before encrypting systems and threaten to publish it.
- **Disruption and sabotage:** Damage an organization's reputation or operations.
- **Reconnaissance and future attacks:** Obtain information useful for subsequent operations.

### Common phases

1. **Discovery and collection:** Locate and gather sensitive files.
2. **Staging:** Aggregate files and potentially compress, encrypt, or encode them.
3. **Exfiltration transport:** Transfer the data over a network, cloud service, removable media, or covert channel.
4. **Command and control (C2):** Direct the operation and coordinate transfers. C2 can overlap with every other phase.

### Example threat actors and techniques

| Threat actor/campaign | Technique described in the room |
| --- | --- |
| APT29 (Cozy Bear) | Exfiltration over HTTPS and legitimate domains |
| FIN7 | HTTP POST requests to command-and-control servers |
| Lunar Spider (Zloader) | Encrypted C2 channels and staged activity |
| DarkSide ransomware | Double extortion: data theft followed by encryption and leak threats |
| APT10 (Cloud Hopper) | Cloud-to-cloud transfers and cloud APIs |

These are examples of reported techniques, not unique signatures. The same protocols may be used by legitimate applications and other threat actors.

### Detection sources

| Source | Useful evidence |
| --- | --- |
| Firewall / NetFlow | Source and destination, ports, time, bytes transferred |
| DNS logs | Query names, query volume, record types |
| Proxy / web gateway | URLs, HTTP methods, destinations, upload sizes |
| Sysmon / EDR | Process execution, network connections, file activity |
| Cloud audit logs | Uploads, sharing events, account and API activity |
| Authentication logs | User and service-account activity |
| Removable-media logs | File-copy activity and USB use |

### General indicators of attack

- Unusually large or frequent outbound transfers.
- Long, encoded-looking DNS names or unusual query patterns.
- Suspicious process command lines and unexpected network connections.
- Unexpected uploads to cloud storage or external sharing.
- Many file-read events followed by an outbound connection.
- Repeated small transfers that may indicate low-and-slow exfiltration.

**Analyst principle:** An indicator creates a hypothesis. Correlated evidence and contextual investigation support a conclusion.

---

## 3. DNS tunneling investigation

### Objective

Identify potential data exfiltration through DNS queries and correlate packet-capture evidence with Splunk DNS logs.

### Why DNS is abused

DNS translates domain names into records such as A, AAAA, TXT, MX, and CNAME. Since DNS queries are common and DNS traffic is often permitted, attackers may encode data in query names or responses to establish a covert channel.

DNS normally uses UDP port 53, although TCP is also used, including for zone transfers and certain larger responses.

### Indicators

- High query volume directed to one domain.
- Long or unusual query names, particularly encoded-looking subdomains.
- High-entropy or Base32/Base64-like labels.
- Unusual TXT or other record-type activity.
- Large responses, unusual TCP use, or fragmentation.
- Repeated queries at regular intervals.
- Many queries without responses or frequent NXDOMAIN responses.

These are clues rather than proof; normal DNS failures and legitimate applications can produce similar patterns.

### Lab file

`/data_exfil/dns_exfil/dns_exfil.pcap`

### Wireshark investigation

**1. Inspect DNS traffic**

```
dns
```

**2. Show DNS queries**

```
dns.flags.response == 0
```

**3. Narrow the search to larger DNS frames**

```
dns && frame.len > 70
```

**4. Pivot to the suspicious domain**

```
dns.qry.name contains "suspected-domain"
```

Replace the placeholder with the domain identified from the PCAP.

### Splunk investigation

**Inspect the DNS dataset**

```
index="data_exfil" sourcetype="DNS_logs"
```

**Count events by source IP**

```
index="data_exfil" sourcetype="DNS_logs"
| stats count by src_ip
| sort - count
```

**Count events by query name**

```
index="data_exfil" sourcetype="DNS_logs"
| stats count by query
| sort - count
```

**Identify long query names**

```
index="data_exfil" sourcetype="DNS_logs"
| where len(query) > 30
```

### Findings described by the room

- A suspicious domain received repeated DNS queries.
- Multiple internal hosts appeared to send data in chunks through DNS tunneling.
- Unusually long queries and high request counts helped identify the activity.

### Questions and answers

- Suspicious receiving domain: **[Fill in verified answer]**
- Number of suspicious DNS tunneling events: **[Fill in verified answer]**
- Local IP with the highest number of suspicious requests: **[Fill in verified answer]**

### Analyst takeaway

Combine suspicious query names, query counts, source IPs, and response patterns. Count the correct subset of events when answering questions; total DNS events and domain-specific suspicious events are not necessarily the same.

---

## 4. FTP exfiltration investigation

### Objective

Identify suspicious FTP activity, credentials, file transfers, source hosts, and potentially exfiltrated sensitive documents.

### How attackers abuse FTP

- Use legitimate public or misconfigured internal FTP servers.
- Use compromised user or service-account credentials.
- Use unexpected accounts or temporary accounts.
- Use non-standard ports or tunneling to blend into normal traffic.

FTP is important to investigate because traditional FTP may expose usernames, passwords, commands, and transferred content in plaintext.

**Protocol distinction:** SFTP operates over SSH, while FTPS uses FTP with TLS. Their visibility and detection methods differ from unencrypted FTP.

### Indicators

- `USER` and `PASS` commands.
- `STOR` commands indicating upload attempts.
- `RETR` commands indicating download attempts.
- Large data connections to unusual external destinations.
- Suspicious accounts, unusual hours, and unexpected filenames.
- Passive FTP (PASV) data connections using separate ports.

### Lab file

`/data_exfil/ftp_exfil/ftp-lab.pcap`

### Wireshark investigation

**1. Isolate FTP control and data traffic**

```
ftp || ftp-data
```

**2. Inspect login attempts**

```
ftp.request.command == "USER" ||
ftp.request.command == "PASS"
```

**3. Look for upload commands**

```
ftp contains "STOR"
```

**4. Search for CSV-related evidence**

```
ftp contains "csv"
```

**5. Narrow down larger frames**

```
ftp && frame.len > 90
```

**6. Follow the session**

Right-click a relevant packet and select **Follow → TCP Stream**. Inspect the command sequence, account, filenames, and any visible transfer content.

A control connection contains FTP commands and responses; file data may travel over a separate data connection. Following the control stream alone may not reveal the complete file.

### Findings described by the room

- A suspicious connection used the `Guest` account.
- CSV files containing potentially sensitive information were transferred to a suspicious external IP.
- Activity involving the `root` account was associated with a customer-related file.
- A large payload from an internal host contained document content.
- A flag was hidden in the FTP stream transferring a CSV file.

### Questions and answers

- Number of connections observed from the Guest account: **[Fill in verified answer]**
- Customer-related filename transferred from the root account: **[Fill in verified answer]**
- Internal IP sending the largest payload to an external IP: **[Fill in verified answer]**
- Flag in the CSV transfer stream: **[Fill in verified flag]**

### Analyst takeaway

Inspect both FTP commands and data connections. A suspicious username alone does not prove exfiltration; confirm the transferred files, destination, payload evidence, and surrounding activity.

---

## 5. HTTP exfiltration investigation

### Objective

Identify suspicious HTTP uploads in Splunk, investigate large POST requests, and corroborate the activity in a PCAP.

### How attackers abuse HTTP

- POST stolen files or data to external servers.
- Encode small amounts of data in GET paths or query strings.
- Hide activity behind legitimate services or cloud platforms.
- Place data in custom headers.
- Split data into multiple or multipart requests.
- Use encrypted HTTPS channels that limit direct payload visibility.
- Stage or transfer data through cloud services.

### Indicators

- Unusually large POST requests to unexpected destinations.
- Repeated small requests or beaconing followed by larger uploads.
- Unfamiliar domains and low-reputation destinations.
- Large request bodies, multipart uploads, or suspicious headers.
- Correlation between endpoint activity and outbound transfers.

### Splunk investigation

Select **All Time** as the time range, as directed by the room.

**1. Inspect HTTP logs**

```
index="data_exfil" sourcetype="http_logs"
```

**2. Filter POST requests**

```
index="data_exfil" sourcetype="http_logs" method=POST
```

**3. Compare POST sizes by domain**

```
index="data_exfil" sourcetype="http_logs" method=POST
| stats count avg(bytes_sent) max(bytes_sent) min(bytes_sent) by domain
| sort - count
```

**4. Find large POST requests**

```
index="data_exfil" sourcetype="http_logs" method=POST bytes_sent > 600
| table _time src_ip uri domain dst_ip bytes_sent
| sort - bytes_sent
```

### Lab file

`/data_exfil/http_exfil/http_lab.pcap`

### Wireshark investigation

**1. Inspect HTTP traffic**

```
http
```

**2. Filter POST requests**

```
http.request.method == "POST"
```

**3. Narrow down larger frames**

```
http.request.method == "POST" && frame.len > 500
```

Then try:

```
http.request.method == "POST" && frame.len > 750
```

**4. Follow the HTTP stream**

Right-click the suspicious request and select **Follow → HTTP Stream** (or the appropriate stream-following option available in your Wireshark version). Inspect the host, URI, headers, and request body when available.

### Findings described by the room

- Splunk identified an unusually large POST request.
- Wireshark was used to find a matching request in the PCAP.
- Following the HTTP stream exposed content associated with a sensitive document being transferred externally.

### Questions and answers

- Internal compromised host used for exfiltration: **[Fill in verified answer]**
- Flag hidden in the exfiltrated data: **[Fill in verified flag]**

### Analyst takeaway

Correlate the SIEM record with the packet capture using timestamps, source IP, destination IP, domain, URI, and request size. A large request is suspicious in context, but it is not conclusive on its own.

**Important:** `bytes_sent` is a dataset-specific field. Verify its definition and units before comparing it with `frame.len`, which measures the size of an individual captured frame.

---

## 6. ICMP exfiltration investigation

### Objective

Investigate potential data exfiltration hidden inside ICMP packet payloads.

### How attackers abuse ICMP

ICMP supports diagnostic and control functions, including ping and TTL-exceeded messages. Attackers may misuse it as a covert channel by placing encoded data in packet payloads.

Common approaches include:

- Embedding Base64-like or hexadecimal chunks in echo requests or replies.
- Using uncommon ICMP types or codes.
- Splitting data across multiple packets.
- Sending repeated packets at regular intervals.
- Hiding data inside large or unusual payloads.

### Indicators

- Frequent ICMP traffic between an internal host and an unfamiliar external destination.
- Unusually large packet payloads.
- High-entropy or encoded-looking bytes.
- Repeated packet sizes and regular timing.
- Fragmentation or reassembly patterns.

### Lab file

`/data_exfil/icmp/exfil/icmp_lab.pcap`

### Wireshark investigation

**1. Inspect all ICMP traffic**

```
icmp
```

**2. Isolate echo requests**

```
icmp.type == 8
```

**3. Narrow down larger echo-request frames**

```
icmp.type == 8 && frame.len > 100
```

Inspect the packet bytes and ICMP payload, and follow the pattern across related packets. Determine whether the payload contains encoded or otherwise suspicious data.

### Findings described by the room

The exercise uses packet size and payload inspection to identify possible ICMP-based data exfiltration.

The room suggests that normal ping traffic is often relatively small. However, legitimate applications can also send larger packets, so a frame-size threshold is only a starting point.

### Question and answer

- Flag found in the ICMP exfiltrated data: **[Fill in verified flag]**

### Analyst takeaway

Do not classify ICMP as malicious solely because packets are large. Investigate the source, destination, frequency, packet types, payload contents, and surrounding activity.

---

## 7. Cross-protocol investigation workflow

When investigating possible exfiltration:

1. Identify the alert or suspicious activity.
2. Establish the time range and affected host.
3. Identify the destination, protocol, and transfer volume.
4. Filter the relevant traffic in Wireshark or Splunk.
5. Inspect requests, commands, DNS names, or packet payloads.
6. Correlate timestamps, IPs, domains, and accounts across data sources.
7. Check legitimate explanations and baseline behavior.
8. Record evidence and state what remains uncertain.
9. Recommend proportionate containment or escalation based on confidence and impact.

### Investigation report template

**Summary:** What happened and why is it suspicious?

**Affected host/user:** Who or what generated the activity?

**Timeline:** When did the relevant events occur?

**Destination:** Which external IP, domain, or service received the traffic?

**Protocol and technique:** DNS, FTP, HTTP, ICMP, or another channel.

**Evidence:** Query results, packet numbers, stream content, filenames, transfer sizes, and correlated events.

**Alternative explanations:** Could the activity be a legitimate backup, upload, or monitoring operation?

**Assessment:** Benign, suspicious, or confirmed malicious based on available evidence.

**Recommended action:** Further investigation, escalation, containment, or remediation.

---

## 8. Final review

- 
    
    I can explain the data-exfiltration lifecycle.
    
- 
    
    I can recognize common DNS tunneling indicators.
    
- 
    
    I can inspect FTP commands and follow TCP streams.
    
- 
    
    I can identify suspicious HTTP POST requests in Splunk.
    
- 
    
    I can correlate SIEM events with packet captures.
    
- 
    
    I can inspect ICMP payloads for potential covert data.
    
- 
    
    I can distinguish suspicious indicators from confirmed malicious activity.
    
- 
    
    I can produce an evidence-based investigation report.
    
- 
    
    I have verified all room answers and flags in the lab.