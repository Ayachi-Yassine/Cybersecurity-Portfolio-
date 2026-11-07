# Elastic Stack: The Basics

**ELK Stack** is a collection of tools used to **collect, store, search, analyze, and visualize logs**.

- **E — Elasticsearch** → stores and searches large amounts of log/data.
- **L — Logstash** → collects, parses, transforms, and forwards logs.
- **K — Kibana** → creates dashboards and visualizes the data.

## **Beats**

Beats are host-based agents known as data-shippers that ship/transfer data from the endpoints to Elasticsearch. Each beat is a single-purpose agent that sends specific data to Elasticsearch. All available beats are shown below.

## **How they work together:**

Now that we have learned about all the components of the Elastic Stack, let's see how these components work together step-by-step:

- **Beats** collect data from multiple agents. For example, Winlogbeat collects Windows event logs, and Packetbeat collects network traffic flows.
- **Logstash** collects data from beats, ports, or files, parses/normalizes it into field value pairs, and stores them into Elasticsearch.
- **Elasticsearch** acts as a database used to search and analyze data.
- **Kibana** is responsible for displaying and visualizing the data stored in Elasticsearch. The data stored in Elasticsearch can easily be shaped into different visualizations, time charts, infographics, etc., using Kibana.

# KQL — Kibana Query Language

## What is KQL?

**KQL (Kibana Query Language)** is used in **Kibana** to search and filter logs/documents stored in **Elasticsearch**.

It is mainly used in the **Discover** search bar.

---

## 1. Free-text Search

Searches for a term **anywhere in the document**, regardless of the field.

```
security
```

Finds documents containing `security`.

### Exact terms

KQL searches for the **whole term/word**.

```
United
```

does NOT necessarily match:

```
United States
```

### Wildcards

Use `*` to match additional characters.

```
United*
```

Can match:

```
United
United States
United Kingdom
United Nations
```

---

# 2. Logical Operators

KQL supports:

- `AND` → both conditions must be true
- `OR` → either condition can be true
- `NOT` → exclude a condition

### AND

```
"United States" AND "Virginia"
```

Returns logs containing **both** terms.

### OR

```
"United States" OR "England"
```

Returns logs containing **either** term.

### NOT

```
"United States" AND NOT "Florida"
```

Returns United States logs while excluding Florida.

---

# 3. Field-Based Search

Instead of searching the entire document, specify a **field**.

### Syntax

```
Field: Value
```

Example:

```
UserName: Suleman
```

Searches for documents where the `UserName` field contains `Suleman`.

### Multiple fields

```
Source_ip: 238.163.231.224 AND UserName: Suleman
```

Means:

```
Source_ip = 238.163.231.224
        AND
UserName = Suleman
```

Both conditions must match.

---

# 4. Combining Field Searches

You can combine multiple fields with logical operators.

### AND

```
Source_Country: "United States" AND UserName: "James"
```

Means:

> Find James's events originating from the United States.
> 

### OR

```
UserName: "James" OR UserName: "Albert"
```

Means:

> Find events belonging to James OR Albert.
> 

### AND + OR

Use parentheses when combining conditions:

```
Source_Country: "United States" AND (UserName: "James" OR UserName: "Albert")
```

This means:

> Find **James or Albert**, but only when the source country is the United States.
> 

---

# 5. Important: Field vs Free Text

These are different:

```
UserName: Albert
```

Searches specifically in the **UserName** field.

While:

```
Albert
```

searches for `Albert` **anywhere in the document**.

Therefore, when investigating a specific field, use:

```
Field: Value
```

---

# 6. How to Find Available Fields

Click the **Kibana search bar**.

Kibana provides a list of available fields that can be used in queries.

Common SOC fields might include:

```
UserName
Source_ip
Source_Country
Destination_ip
EventType
```

Always check the available fields before assuming a field name.

---

# Quick Cheat Sheet

| Goal | KQL |
| --- | --- |
| Search anywhere | `security` |
| Wildcard | `United*` |
| Both terms | `"United States" AND "Virginia"` |
| Either term | `"United States" OR "England"` |
| Exclude term | `"United States" AND NOT "Florida"` |
| Search a field | `UserName: Suleman` |
| Multiple fields | `Source_ip: 1.2.3.4 AND UserName: Suleman` |
| Field OR | `UserName: James OR UserName: Albert` |
| Field + grouped OR | `Source_Country: "United States" AND (UserName: James OR UserName: Albert)` |

## SOC Investigation Rule

When investigating logs:

**1. Identify the field → 2. Filter the value → 3. Combine conditions → 4. Inspect matching events**

Example:

```
Source_Country: "United States" AND UserName: "James"
```