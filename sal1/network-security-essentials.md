# Network Security Essentials

starting with splunk invistigation then we will be back to the files invistigation later 

**Question 1: Which external IP performed the most reconnaissance?**

in splank search 

```jsx
index="network_logs" sourcetype="firewall_logs" action="blocked"
or use
index="network_logs" sourcetype="firewall_logs" action="BLOCK"
| stats count by src_ip
| sort - count
```

![image.png](image%2060.png)

279 blocked from ip 203.0.113.45 : it is the the ip that performed the most recon

## Question 2 — Which internal host was targeted by scans?

now for this task we check the count on dst ips 

```jsx
index="network_logs" sourcetype="firewall_logs" action="BLOCK"
| stats count by dest_ip
| sort - count
```

we get this 

| Destination IP | Hostname | Blocked events | Criticality |
| --- | --- | --- | --- |
| `10.0.0.20` | FINANCE-SRV1 | 145 | High |
| `10.0.0.50` | VPN-GW | 145 | Critical |
| `10.0.0.60` | WORKSTATION-60 | 34 | Medium |
| `10.0.0.51` | APP-WEB-01 | 27 | High |

the answer is 10.0.0.20

so the scan targeted both .20 and .50 the most

## **Question 3 — Which username was targeted in VPN logs?**

```jsx
index="network_logs" sourcetype="vpn_logs"| stats count by username
| sort - count
```

![image.png](image%2061.png)

## **Question 4 — What internal IP was assigned after successful VPN login?**

usig this we see 10.0.0.23 was asigned twice 

```jsx
index="network_logs" sourcetype="vpn_logs" username="svc_backup" result="SUCCESS"
| stats count by assigned_ip
```

looking at the events we can see the suspicous ip as a src

![image.png](image%2062.png)

Let's understand the evidence:

- `src_ip = 203.0.113.45` → the external IP connecting to the VPN.
- `username = svc_backup` → the account used.
- `result = SUCCESS` → authentication succeeded.
- `assigned_ip = 10.8.0.23` → the internal VPN IP assigned to that client.

Your inventory identifies `10.8.0.23` as `VPN-CLIENT-ATTK`, which makes this worth investigating further.

## **Question 5 — SMB lateral movement**

```jsx
index="network_logs" sourcetype="firewall_logs" src_ip="10.8.0.23"
| stats count by dst_ip dst_port action
| sort - count

```

### Key findings

- `10.8.0.23` = `VPN-CLIENT-ATTK`.
- The client communicated with multiple internal hosts on port `445`.
- `ALLOW` = firewall permitted the traffic.
- `BLOCK` = firewall denied the traffic.
- `count` = number of matching events.

### SOC takeaway

Connections from one host to multiple internal systems over SMB may indicate lateral movement, but require further investigation.

We choose `sourcetype="firewall_logs"` because we're investigating network traffic between hosts, and firewall logs record traffic that the firewall allows or blocks.

Think of it this way:

| Log source | What it helps investigate |
| --- | --- |
| `firewall_logs` | Network connections, source/destination IPs, ports, allowed or blocked traffic |
| `vpn_logs` | VPN authentication attempts, usernames, login results, assigned VPN IPs |
| `ids_logs` | Suspicious activity detected by an Intrusion Detection System (IDS) |

For Question 5, we're looking for the port used for SMB lateral movement. Firewall logs can show which source contacted which destination on which port.

We also filter with:

```
dest_port=445
```

Port 445/TCP is the standard port for SMB (Server Message Block), commonly used for Windows file sharing.

One important detail: filtering on port 445 helps us find SMB traffic, but it doesn't automatically prove lateral movement was malicious. We'll examine the source, destination, and action to interpret it

## **Question 6 —** Identify the host beaconing to C2

```jsx
index="network_logs" sourcetype="ids_logs"
| head 10
```

we find 10.0.0.60

## 1. What do these alerts tell us?

You have two alert types:

- `ET TROJAN Possible C2 Beaconing` — the IDS detected network activity resembling a Trojan communicating with command-and-control infrastructure. This is our strongest lead for Question 6.
- `ET INFO Possible HTTP POST Large Upload` — the IDS detected a potentially large HTTP upload, which could indicate data exfiltration. We'll use that later for Question 8.

The `priority` field also helps us triage:

- `priority: 1` — higher priority than `priority: 2` in this dataset.

Notice the C2 alert appears at different times. Repeated alerts may indicate recurring communication, but we need the IP addresses and timestamps to investigate further.

## 2. Next step: identify the host

We need the source IP associated with the C2 beaconing alert. The source IP typically identifies the host initiating the suspicious communication, while the destination IP may be the C2 server

Why this query?

- We narrow the search to the specific alert instead of mixing it with the upload alerts.
- We display the source and destination IPs to distinguish the potentially compromised host from the remote server.
- We include the alert and priority to preserve the context for our conclusion.

```jsx
index="network_logs" sourcetype="ids_logs" "Possible C2 Beaconing"
| table _time src_ip dest_ip alert classification priority
```

## **Question 7 —** During the investigation, which IP was observed to be associated with C2?

We already know the potential victim host. Now we need the remote destination IP it communicated with during the C2 alerts.

Run:

```
index="network_logs" sourcetype="ids_logs" "Possible C2 Beaconing"
| stats count by src_ip dest_ip
| sort - count
```

This groups the C2 alerts by source and destination, showing which IP pair appears most often.

## **Question 8 — Which host showed exfiltration attempts?**

Let's apply the same reasoning to the `ET INFO Possible HTTP POST Large Upload` alert we noticed earlier.

An exfiltration attempt means a possible attempt to transfer data out of a system or network. Large HTTP POST uploads can be a clue, but we need to identify the source host in the actual events.

Run this query:

```
index="network_logs" sourcetype="ids_logs" "POST Large Upload"
| stats count by src_ip dst_ip
| sort - count
```