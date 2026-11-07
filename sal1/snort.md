# snort

### IDS vs IPS — the most important concept

#### IDS

An IDS monitors activity and detects suspicious behavior.

When it detects something suspicious, it generates an alert.

It does not automatically block the traffic.

Think of it as a security camera: it observes, detects, and reports.

#### IPS

An IPS monitors activity and actively prevents threats.

When it identifies malicious traffic, it can block or drop packets.

Think of it as a security guard: it observes suspicious activity and intervenes.

| IDS | IPS |
| --- | --- |
| Detects the malicious request | Detects the malicious request |
| Generates an alert | Generates an alert |
| Traffic continues | Traffic can be blocked |

Remember this:

- IDS = Detect and alert.
- IPS = Detect and prevent.

### Network-based vs Host-based

#### **A. Network-based systems**

NIDS — Network Intrusion Detection System

Monitors network traffic across a network or subnet.

Example: A sensor monitors traffic passing through a company's network and detects suspicious communication between computers.

NIPS — Network Intrusion Prevention System

Similar monitoring scope, but it can actively block malicious network traffic.

Example: An IPS blocks a malicious connection before it reaches an internal server.

#### **B. Host-based systems**

HIDS — Host Intrusion Detection System

Monitors activity on an individual machine.

Example: Monitoring a Linux server for suspicious activity or unauthorized changes.

HIPS — Host Intrusion Prevention System

Actively protects an individual machine by preventing suspicious or unauthorized activity.

Example: Blocking a suspicious operation on a protected endpoint.

### The easiest way to remember all four

| System | Scope | Main function |
| --- | --- | --- |
| NIDS | Network | Detect |
| NIPS | Network | Prevent |
| HIDS | Individual host | Detect |
| HIPS | Individual host | Prevent |

#### Other IPS types you should recognize

| System | What it protects |
| --- | --- |
| WIPS | Wireless networks |
| HIPS | Individual computers |
| NBA | Network behavior |

| System | Scope | Function |
| --- | --- | --- |
| NIDS | Network | Detect |
| NIPS | Network | Prevent |
| HIDS | Individual host | Detect |
| HIPS | Individual host | Prevent |
| WIPS | Wireless network | Prevent |
| NBA | Network behavior | Identify anomalies |

## 3. Detection Techniques

- **Signature-based:** Identifies known malicious patterns using predefined rules.
- **Behaviour-based:** Identifies abnormal activity by comparing it with normal behavior.
- **Policy-based:** Identifies activities that violate security policies.

## 4. NBA and Baselining

Baselining is the training period during which a system learns normal network behavior. It subsequently identifies deviations that may indicate threats.

An inaccurate baseline can cause false positives or allow malicious activity to be considered normal.

## 5. Snort

Snort is an open-source, rule-based Network Intrusion Detection and Prevention System (NIDS/NIPS).

Main capabilities:

- Live traffic analysis
- Attack and probe detection
- Packet logging
- Protocol analysis
- Real-time alerting

Three operating models:

1. Sniffer Mode
2. Packet Logger Mode
3. IDS/IPS Mode

## 6. Key Takeaways

- IDS detects; IPS prevents.
- NIDS/NIPS operate at network level.
- HIDS/HIPS operate at host level.
- Signature detection identifies known patterns.
- Behaviour detection identifies anomalies.
- Policy detection identifies policy violations.
- Baselining means learning normal behavior.

## SNORT Sniffer Mode

## The four important options

- `v` — Verbose mode

Packet headers

Displays packet information, such as source and destination IP addresses.

```
sudo snort -v
```

- `d` — Display packet data

Payload

Displays the packet's data (payload), in addition to packet headers.

```
sudo snort -d
```

- `e` — Display link-layer headers

Ethernet

Displays link-layer information, such as Ethernet source and destination MAC addresses.

```
sudo snort -e
```

- `X` — Display packet details in hexadecimal

Hex + ASCII

Displays packet data in hexadecimal and ASCII format.

```
sudo snort -X
```