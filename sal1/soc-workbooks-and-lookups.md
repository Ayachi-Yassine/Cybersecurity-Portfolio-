# SOC Workbooks and Lookups

1. Identity Inventory = "Who is this user?”

2. Asset Inventory = "What is this computer/server?”

3. Access information = "Should this person be able to do this?”

### 3. Access information = "Should this person be able to do this?"

Now combine the information:

**Identity inventory**

> G.Baker → CFO → Finance access → UK
> 

**Asset inventory**

> HQ-FINFS-02 → Financial file server → UK
> 

**Alert**

> G.Baker → logged into HQ-FINFS-02 → downloaded financial report
> 

You can now ask:

> Does the user's role and permissions match the resource they're accessing?
> 

# **workbook**

### 1. Who uses the workbook?

The question asks:

> **Which SOC role would use workbooks the most?**
> 

Answer:

**SOC L1 Analyst**

Why?

L1 analysts are usually the **first people handling alerts**. They need a standardized procedure so they don't forget important investigation steps.

For example:

```
L1 Analyst receives alert
        ↓
Follow workbook
        ↓
Check user
        ↓
Check IP
        ↓
Check host
        ↓
Check SIEM logs
        ↓
Make verdict
        ↓
Close OR escalate to L2
```

Senior analysts create/help maintain these procedures, but **L1 analysts use them heavily**.

---

### 2. What is "enrichment"?

The question asks:

> **What is the process of gathering user, host, or IP context using TI and lookups?**
> 

Answer:

**Enrichment**

This is an important SOC word.

Basically:

> **Enrichment = taking a basic alert and adding useful information to it.**
> 

For example, the SIEM gives you:

```
User: G.Baker
IP: 185.x.x.x
Host: HQ-FINFS-02
```

That's not much context.

You enrich it:

```
G.Baker
→ CFO
→ UK employee
→ Finance access

185.x.x.x
→ Germany
→ VPN provider
→ Not previously seen for this user

HQ-FINFS-02
→ Financial file server
→ UK datacenter
→ Finance department
```

Now the alert has **context**.

So remember:

> **Enrichment = "Add context to the alert."**
> 

---

### 3. Identity inventory source

The workbook diagram uses:

**BambooHR**

BambooHR is an **HR system**, and in this scenario it acts as the identity information source.

So the answer is:

> **BambooHR**
> 

---

## The whole lesson in one picture

```
                🚨 ALERT
                   │
                   ▼
             ENRICHMENT
          "Who/what is involved?"
                   │
          ┌────────┼────────┐
          ▼        ▼        ▼
       Identity   Asset     IP/TI
       lookup    lookup    lookup
          │        │        │
          └────────┼────────┘
                   ▼
             INVESTIGATION
          "Does it make sense?"
                   │
                   ▼
              VERDICT
             /       \
            /         \
       Expected     Suspicious
          │             │
          ▼             ▼
        CLOSE        ESCALATE
                       │
                       ▼
                       L2
```

### The 3 words I would memorize from this room

| Term | Simple meaning |
| --- | --- |
| **Lookup** | Find information about a user/host/IP |
| **Enrichment** | Add that information to the alert |
| **Workbook / Playbook** | Step-by-step instructions for investigating the alert |

And the overall SOC workflow is:

**Alert → Enrichment → Investigation → Verdict → Close/Escalate**

That's really what this section wants you to understand.

![678ecc92c80aa206339f0f23-1743455620681.svg](678ecc92c80aa206339f0f23-1743455620681.svg)