# Man-in-the-Middle Detection

## **Common Types of MITM Attacks**

- Packet sniffing: Capturing unencrypted data packets exchanged over a network, often on open Wi-Fi.
- Session hijacking: Stealing and using session tokens to impersonate users.
- SSL stripping: Downgrading HTTPS connections to insecure  to steal or alter data transferred.
    
    HTTP
    
- DNS spoofing: Redirecting legitimate website traffic to fraudulent domains by manipulating DNS responses.
- IP spoofing: Crafting malicious IP packets that appear to come from trusted systems.
- Rogue Wi-Fi access point: Creating fake networks to intercept user traffic.

# TryHackMe — Man-in-the-Middle Detection

**Difficulty:** Easy–Medium concepts

**Focus:** Network traffic analysis, ARP spoofing, DNS spoofing, SSL stripping, Wireshark

**Role:** SOC Analyst / Blue Team

**Status:** Completed

---

## 1. Room objectives

This room demonstrates how to detect a Man-in-the-Middle (MITM) attack by analyzing network traffic in Wireshark.

I learned to:

- Identify suspicious ARP replies and conflicting IP-to-MAC mappings.
- Detect potentially forged DNS responses.
- Identify HTTP traffic that may expose credentials.
- Correlate multiple indicators instead of investigating each packet in isolation.
- Explain findings using evidence and an analyst's reasoning.

## 2. What is a Man-in-the-Middle attack?

A **Man-in-the-Middle (MITM)** attack occurs when an attacker positions themselves between two communicating systems to intercept, redirect, monitor, or modify their traffic.

The victim may believe they are communicating directly with a legitimate gateway or website.

### Techniques covered

**ARP spoofing**

- ARP maps IPv4 addresses to MAC addresses on a local network.
- An attacker sends forged ARP messages to associate their MAC address with another device's IP address, such as the default gateway.
- The victim may send traffic through the attacker.

**DNS spoofing**

- DNS translates domain names into IP addresses.
- An attacker provides a false DNS response, potentially directing a victim to a malicious IP address instead of the legitimate destination.

**SSL stripping**

- HTTPS protects HTTP traffic using TLS encryption.
- In a simplified SSL-stripping attack, the victim's connection to the attacker remains HTTP while the attacker may maintain HTTPS to the legitimate server.
- Information sent over the victim's HTTP connection can be exposed in plaintext.

## 3. Lab findings — ARP spoofing

### Scenario

The legitimate gateway IP is `192.168.10.1`.

| Indicator | Lab finding |
| --- | --- |
| Gateway IP | `192.168.10.1` |
| Legitimate gateway MAC | `02:aa:bb:cc:00:01` |
| Suspicious MAC impersonating gateway | `02:fe:fe:fe:55:55` |
| ARP packets from legitimate gateway MAC | `10` |
| Gratuitous ARP replies for the gateway IP | `2` |
| Unique MAC addresses claiming the gateway IP | `2` |
| ARP spoofing packets attributed to attacker | `14` |

### Wireshark investigation

Find ARP traffic where the legitimate gateway MAC advertises the gateway IP:

```
arp && arp.src.proto_ipv4 == 192.168.10.1 && eth.src == 02:aa:bb:cc:00:01
```

Find ARP replies claiming the gateway IP:

```
arp.opcode == 2 && arp.src.proto_ipv4 == 192.168.10.1
```

Find gratuitous ARP packets associated with the gateway IP:

```
arp.isgratuitous && arp.src.proto_ipv4 == 192.168.10.1
```

Find ARP replies from the suspicious MAC:

```
arp.opcode == 2 && arp.src.proto_ipv4 == 192.168.10.1 && eth.src == 02:fe:fe:fe:55:55
```

### Interpretation

Two different MAC addresses claiming the same gateway IP indicate a conflicting ARP mapping. In the context of this capture, the suspicious MAC and other observed traffic support the ARP-spoofing hypothesis.

**Important:** Duplicate ARP claims alone do not prove an attack. High availability, virtual machines, interface changes, and legitimate network reconfiguration can also produce multiple claims.

## 4. Lab findings — DNS spoofing

### Scenario

The domain under investigation is:

`corp-login.acme-corp.local`

The expected DNS resolver given by the lab is `8.8.8.8`.

The suspicious DNS response directs the domain to `192.168.10.55`.

### Answers

| Question | Finding |
| --- | --- |
| DNS responses for the target domain | `211` |
| Responses from sources other than `8.8.8.8` | `2` |
| IP address returned by the forged DNS response | `192.168.10.55` |

The room's wording refers to suspicious requests, but the investigation filter selects **DNS responses** from unexpected source IP addresses.

### Wireshark investigation

Count responses for the target domain:

```
dns.flags.response == 1 && dns.qry.name == "corp-login.acme-corp.local"
```

Show responses from the expected resolver:

```
dns.flags.response == 1 && ip.src == 8.8.8.8 && dns.qry.name == "corp-login.acme-corp.local"
```

Show responses from other sources:

```
dns.flags.response == 1 && ip.src != 8.8.8.8 && dns.qry.name == "corp-login.acme-corp.local"
```

Inspect responses sent by the suspected attacker:

```
dns.flags.response == 1 && ip.src == 192.168.10.55 && dns.qry.name == "corp-login.acme-corp.local"
```

### Interpretation

A response from an unexpected source that directs the victim to a suspicious IP is an indicator of possible DNS spoofing.

To validate the finding, check the configured resolver, query and response correspondence, DNS transaction ID, timestamps, answer records, and the surrounding ARP/HTTP traffic.

An unusual DNS response source is suspicious, but it should not be treated as conclusive without context.

## 5. Lab findings — SSL stripping

### Scenario

The lab connects the earlier ARP and DNS findings to a potential credential-exposure attack.

| Asset | IP address |
| --- | --- |
| Victim | `192.168.10.10` |
| Suspected attacker | `192.168.10.55` |
| Target domain | `corp-login.acme-corp.local` |

### Answers

| Question | Finding |
| --- | --- |
| HTTP POST requests observed for the target domain | `1` |
| Password exposed in plaintext | `Secret123!` |

### Wireshark investigation

Find HTTP POST requests:

```
http.request.method == "POST"
```

Find HTTP traffic from the victim to the suspected attacker:

```
http && ip.src == 192.168.10.10 && ip.dst == 192.168.10.55
```

Find TLS Client Hello packets for the target domain:

```
tls.handshake.type == 1 && tls.handshake.extensions_server_name == "corp-login.acme-corp.local"
```

Find DNS responses sent by the suspected attacker:

```
dns.flags.response == 1 && ip.src == 192.168.10.55 && dns.qry.name == "corp-login.acme-corp.local"
```

### How to inspect a POST request

1. Apply the POST filter.
2. Select the relevant packet.
3. Right-click and choose **Follow → HTTP Stream**.
4. Inspect the HTTP request headers and body.
5. Look for submitted form fields, such as a username and password.
6. Determine whether the sensitive values are readable as plaintext.

A POST request is not inherently malicious. The important finding is that the submitted credentials are visible in an unencrypted HTTP request in this lab.

### Interpretation

The combination of a suspicious DNS response, victim-to-attacker HTTP traffic, and a readable login request supports the conclusion that the victim's credentials were exposed during the simulated attack.

A TLS Client Hello only proves that a client attempted TLS negotiation. It does not prove that TLS was successfully established. Similarly, missing TLS traffic alone does not prove SSL stripping.

## 6. Attack chain reconstructed

1. **ARP spoofing:** The attacker advertises a false MAC address for the gateway IP.
2. **Traffic redirection:** The victim may send traffic through the attacker.
3. **DNS spoofing:** The target domain resolves to the suspicious IP `192.168.10.55`.
4. **HTTP communication:** The victim communicates with the attacker over HTTP.
5. **Credential exposure:** A POST request contains a password readable in plaintext.
6. **Detection:** The analyst correlates ARP, DNS, HTTP, and TLS indicators to reconstruct the attack.

## 7. SOC analyst workflow

When investigating a suspected MITM attack:

1. **Establish context:** Identify the victim, gateway, DNS resolver, destination domain, and expected network behavior.
2. **Inspect ARP:** Look for conflicting IP-to-MAC claims, unexpected replies, and suspicious gratuitous ARP packets.
3. **Inspect DNS:** Compare response sources, query names, transaction IDs, timestamps, and returned IP addresses.
4. **Inspect application traffic:** Determine whether the victim communicates with an unexpected host over HTTP.
5. **Check for exposure:** Inspect relevant HTTP requests for sensitive data, following organizational procedures.
6. **Correlate evidence:** Build a timeline across ARP, DNS, HTTP, and TLS events.
7. **Assess impact:** Identify affected hosts, domains, credentials, and time ranges.
8. **Report and escalate:** Document the evidence, confidence level, impact, and recommended containment.

## 8. Defensive recommendations

- Use HTTPS everywhere and enforce HTTPS with HSTS where appropriate.
- Investigate unexpected DNS answers and secure DNS infrastructure.
- Use network protections such as Dynamic ARP Inspection where supported.
- Monitor for duplicate IP claims and unusual ARP reply patterns.
- Segment sensitive systems and restrict unnecessary lateral traffic.
- Treat potentially exposed credentials as compromised and rotate them through the approved incident-response process.
- Correlate packet captures with endpoint, DNS, DHCP, switch, firewall, and authentication logs when available.

## 9. Key lessons learned

- A single packet is rarely enough to prove an attack.
- Conflicting ARP claims can indicate a gateway impersonation attempt.
- Unexpected DNS response sources and suspicious answer IPs may indicate DNS spoofing.
- HTTP POST requests can expose credentials when the connection is unencrypted.
- A TLS Client Hello is not proof of a successful TLS session.
- Strong SOC findings combine multiple indicators into a consistent timeline.
- Good reporting includes evidence, context, impact, confidence, and recommended actions.

**Final takeaway:** Detecting MITM attacks requires correlating network-layer manipulation (ARP), name-resolution anomalies (DNS), and application-layer evidence (HTTP/TLS), rather than relying on one indicator alone.