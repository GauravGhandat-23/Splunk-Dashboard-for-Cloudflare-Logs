# 🚀 Splunk Dashboard for Cloudflare Logs  
### Real-Time Web Traffic Monitoring & WAF Security Analytics with Splunk SIEM

![Splunk](https://img.shields.io/badge/SIEM-Splunk-green?style=for-the-badge&logo=splunk)
![Cloudflare](https://img.shields.io/badge/Logs-Cloudflare-orange?style=for-the-badge&logo=cloudflare)
![JSON](https://img.shields.io/badge/Data-JSON-blue?style=for-the-badge&logo=json)
![Security](https://img.shields.io/badge/Focus-WAF%20Monitoring-red?style=for-the-badge)
![Status](https://img.shields.io/badge/Project-Completed-success?style=for-the-badge)

---

## 📌 Overview

This project demonstrates how to build a **real-time Splunk dashboard** to monitor **Cloudflare HTTP request logs** and **WAF (Web Application Firewall) events** for both **web performance visibility** and **security analytics**.

Using a structured **JSON Cloudflare dataset**, the dashboard provides insights into:

- 🌐 Website traffic volume  
- ✅ Successful HTTP responses  
- 🛡️ WAF challenges and blocked requests  
- 📍 Geographic distribution of client traffic  
- 🔍 Most targeted URIs and active IPs  
- 🚨 Suspicious activity indicators (XSS, SQLi, scans, recon)

This project is ideal for:

- **SOC Analyst portfolio projects**
- **Blue Team labs**
- **Splunk SIEM home lab demonstrations**
- **Cybersecurity student projects**
- **Web security monitoring practice**

---

# 🎯 Project Objective

Build a **real-time Splunk dashboard** to visualize **Cloudflare web traffic** and **WAF events** for:

- Monitoring website performance
- Detecting suspicious requests
- Tracking WAF challenges and blocks
- Understanding user behavior by URI and IP
- Visualizing client traffic geographically

---

# 🚨 Problem Statement

Modern web applications receive high volumes of traffic that may include:

- Legitimate user requests
- Search engine crawlers
- Automated scanners
- Malicious probes
- Injection attempts
- WAF-triggered attacks

Without centralized visibility, it becomes difficult to:

- Detect suspicious traffic patterns quickly
- Monitor healthy vs unhealthy HTTP responses
- Identify repeatedly targeted endpoints
- Investigate attacker origins
- Correlate WAF actions with specific requests

### ✅ Solution

Ingest **Cloudflare JSON logs** into **Splunk Enterprise** and build a **visual monitoring dashboard** to support:

- Real-time operational visibility
- Threat hunting
- WAF event tracking
- Traffic behavior analysis
- Geo-based anomaly detection

---

# 🛠️ Tech Stack

| Category | Tool / Technology |
|---------|--------------------|
| SIEM Platform | Splunk Enterprise / Splunk SIEM |
| Data Source | Cloudflare HTTP Request Logs |
| Data Format | JSON |
| Visualization | Dashboard Studio / Classic Dashboard |
| Query Language | SPL (Search Processing Language) |
| Geo Visualization | `iplocation`, `geom` |
| Interface | Splunk Web UI |

---

# 📂 Dataset Details

### 📄 Log File
`cloudflare_http_requests_full_.json`

### 🔑 Key Fields Used

- `ClientIP`
- `RayID`
- `EdgeStartTimestamp`
- `Method`
- `URI`
- `QueryString`
- `Status`
- `UserAgent`
- `Referer`
- `OriginHost`
- `OriginIP`
- `CacheStatus`
- `WAFAction`

### 📌 Why this dataset is powerful

This dataset contains a mix of:

- Normal browsing traffic
- Bot traffic
- WAF challenges
- WAF blocks
- Suspicious request patterns
- Recon and injection attempts

This makes it highly suitable for a **SOC-style dashboard project**.

---

# 🧪 Lab Setup

Before creating the dashboard, make sure the following prerequisites are completed:

- [x] Splunk is installed and running
- [x] Splunk SIEM Home Lab is already configured
- [x] `cloudflare_http_requests_full_.json` is available
- [x] Access to:
  - **Search & Reporting App**
  - **Settings → Add Data**
  - **Dashboard creation options**

---

# 📥 Step 1: Ingest Cloudflare Logs into Splunk

## Steps

1. Open **Splunk Enterprise**
2. Click **Settings**
3. Select **Add Data**
4. Choose **Upload**
5. Click **Select File**
6. Upload:
   ```text
   cloudflare_http_requests_full_.json
    ````

7. Click **Next**
8. Set **Source Type** to:

   ```text
   _json
   ```
9. Confirm the preview shows correctly parsed JSON events
10. Click **Next**
11. Review and complete ingestion

---

# 🔎 Step 2: Validate Field Extraction

After ingestion, verify the following:

### Expected Source Metadata

* **Source** = `cloudflare_http_requests_full_.json`
* **Host** = `splunk`
* **Sourcetype** = `_json`

### Important Fields to Validate

* `ClientIP`
* `URI`
* `Status`
* `WAFAction`
* `OriginIP`

### Validation Query

```spl
source="cloudflare_http_requests_full_.json" host="splunk" sourcetype="_json"
| head 20
```

If the data is correct, Splunk should display structured fields in the sidebar or event view.

---

# 📊 Step 3: Create the Dashboard

Create a dashboard named:

```text
Cloudflare Web Traffic & WAF Monitoring Dashboard
```

You can use either:

* **Dashboard Studio**
* **Classic Dashboard**

---

# 🕒 Task 0: Shared Time Range Filter

## 🎯 Goal

Create a **shared time filter** so all panels use the same selected time range.

## Steps

1. Open the dashboard in **Edit Mode**
2. Click **Add Input**
3. Select **Time**
4. Click the **pencil/edit icon**
5. Set:

   * **Label** = `Time Range`
   * **Token** = `time_range`
6. Click **Apply**
7. Again click **Add Input**
8. Select **Submit**

> ⚠️ **Important:** Use `time_range` in **every future panel** for consistent filtering.

---

# 📌 Task 1: Web Activities Panels

## 🎯 Goal

Create quick summary panels using **Single Value** visualizations.

---

## 1️⃣ Total Requests

**Visualization:** Single Value
**Title:** `Total Requests`
**Time Picker:** `time_range`

```spl
source="cloudflare_http_requests_full_.json" host="splunk" sourcetype="_json"
| stats count AS "Total Requests"
```

### 🔍 What it shows

Displays the total number of log events in the selected time window.

---

## 2️⃣ Successful Response

**Visualization:** Single Value
**Title:** `Successful Response`
**Time Picker:** `time_range`

```spl
source="cloudflare_http_requests_full_.json" host="splunk" sourcetype="_json" Status=200
| stats count AS "200 Responses"
```

### 🔍 What it shows

Counts all HTTP `200 OK` responses to measure healthy web traffic.

---

## 3️⃣ WAF Challenges

**Visualization:** Single Value
**Title:** `WAF Challenges`
**Time Picker:** `time_range`

```spl
source="cloudflare_http_requests_full_.json" host="splunk" sourcetype="_json" WAFAction=challenge
| stats count AS "WAF Challenges"
```

### 🔍 What it shows

Shows how many requests triggered a **Cloudflare WAF challenge**.

---

## 4️⃣ WAF Blocks

**Visualization:** Single Value
**Title:** `WAF Block`
**Time Picker:** `time_range`

```spl
source="cloudflare_http_requests_full_.json" host="splunk" sourcetype="_json" WAFAction=block
| stats count AS "WAF Block"
```

### 🔍 What it shows

Shows how many requests were **blocked by the WAF**, which often indicates malicious or high-confidence suspicious traffic.

---

# 📈 Task 2: Web Statistics Panels

## 🎯 Goal

Create bar charts to visualize traffic patterns and activity concentration.

---

## 1️⃣ Top Requested URIs

**Visualization:** Bar Chart
**Title:** `Top Requested URIs`
**Time Picker:** `time_range`

```spl
source="cloudflare_http_requests_full_.json" host="splunk" sourcetype="_json"
| stats count AS Hits by URI
| sort -Hits
| head 10
```

### 🔍 What it shows

Displays the **top 10 most requested URIs** in the selected time range.

### 💡 Why it matters

Helps identify:

* Popular pages / endpoints
* Frequently targeted resources
* API hot paths
* Reconnaissance targets

---

## 2️⃣ Top Users by IP Address

**Visualization:** Bar Chart
**Title:** `Top Users by IP Address`
**Time Picker:** `time_range`

### ✅ Recommended (Actual Client/User IPs)

```spl
source="cloudflare_http_requests_full_.json" host="splunk" sourcetype="_json"
| stats count AS Hits by ClientIP
| sort -Hits
| head 10
```

### ⚠️ Original Project Query Correction

If you want to use **OriginIP**, the corrected query is:

```spl
source="cloudflare_http_requests_full_.json" host="splunk" sourcetype="_json"
| stats count AS Hits by OriginIP
| sort -Hits
| head 10
```

> **Important Fix:**
> In the original material, `| sort -Requests` is incorrect because `Requests` does not exist.
> It must be `| sort -Hits`.

### 🔍 What it shows

Identifies the most active IP addresses in the dataset.

### 💡 Why it matters

Useful for spotting:

* High-frequency clients
* Bots and crawlers
* Repeated scanners
* Suspicious IP concentration

---

# 🌍 Task 3: Geo Map Panel

## 🎯 Goal

Visualize client traffic by geographic location.

### Visualization

**Choropleth Map**

### Title

`Web Traffic by Client IP Location`

### Time Picker

`time_range`

```spl
source="cloudflare_http_requests_full_.json" host="splunk" sourcetype="_json"
| table ClientIP
| iplocation ClientIP
| stats count by Country
| geom geo_countries featureIdField="Country"
```

### 🔍 What it shows

Maps traffic volume by **country** based on `ClientIP`.

### 💡 Why it matters

Helps identify:

* Geographic traffic distribution
* Unexpected foreign traffic
* Possible botnet concentration
* Region-based attack patterns

---

# 🧠 Dashboard Insights

The completed dashboard gives you visibility into:

## 🔹 Operational Metrics

* Total requests
* Successful responses
* Endpoint popularity
* Client distribution

## 🔹 Security Metrics

* WAF challenges
* WAF blocks
* Suspicious request patterns
* Recon / attack-heavy URIs
* Suspicious IP concentration

---

# 🚨 Security Findings in Dataset

This dataset includes multiple suspicious and malicious request patterns, making it ideal for threat monitoring practice.

## Example Attack Indicators

### 🔥 XSS Attempts

* `/search.php?q=<script>alert(1)</script>`
* `/comments.php?msg=%3Cscript%3Ealert(1)%3C/script%3E`

### 💉 SQL Injection Attempts

* `/index.php?id=1' OR '1'='1`
* `/product.php?id=-1 UNION SELECT null--`
* `/search.php?q=UNION SELECT username,password FROM users`

### 📂 Path Traversal / Sensitive File Access

* `/download.php?file=../../../../etc/shadow`
* `/etc/passwd`
* `/.git/HEAD`

### 🛠️ Admin / Reconnaissance Probing

* `/wp-admin`
* `/phpmyadmin`
* `/server-status`

### 🤖 Suspicious User Agents Seen in Logs

* `sqlmap/1.7.0`
* `Nikto/2.1.6`
* `curl/7.88.1`
* `python-requests/2.31.0`
* `DirBuster-1.0-RC1`

---

# 🎓 Learning Outcomes

By completing this project, you will learn how to:

* Ingest structured JSON logs into Splunk
* Use `_json` sourcetype for automatic field extraction
* Build Splunk dashboard panels using SPL
* Create **single-value** and **bar chart** visualizations
* Perform **WAF event monitoring**
* Use **IP geolocation** with `iplocation`
* Create **geo maps** with `geom`
* Detect suspicious traffic patterns from web logs
* Build a practical **SOC analyst portfolio project**

---

# 💼 Use Cases

This project is highly valuable for:

* 🔵 **SOC Analyst Portfolio**
* 🛡️ **Blue Team Monitoring Practice**
* 🧪 **Splunk SIEM Home Lab**
* 🎓 **Cybersecurity Student Mini Project**
* 📊 **Web Traffic Monitoring Demonstrations**
* 🔍 **Threat Hunting Practice**
* 💻 **Interview / Resume Project Showcase**

---

# 🏆 Why This Project Stands Out

Unlike a basic Splunk dashboard, this project combines:

* **Operational visibility** (traffic & status monitoring)
* **Security analytics** (WAF challenges / blocks)
* **Threat indicators** (SQLi, XSS, scans, recon)
* **Geo-based intelligence**
* **Realistic Cloudflare log structure**

This makes it a **strong cybersecurity portfolio project** for:

* **SOC Analyst**
* **SIEM Engineer**
* **Blue Team Intern**
* **Security Operations Intern**
* **Detection Engineering Beginner Projects**

---

# 📜 License

This project is intended for **educational, lab, and portfolio use only**.

You are free to:

* Use it for learning
* Extend the dashboard
* Add new panels
* Reuse it in your home lab
* Showcase it in your cybersecurity portfolio

---

# 🙌 Final Thoughts

This project transforms raw **Cloudflare JSON logs** into a practical **Splunk SIEM dashboard** for:

* Real-time traffic monitoring
* WAF event analysis
* Suspicious request detection
* Attack surface visibility
* Geographic traffic intelligence

It is an excellent **beginner-to-intermediate Splunk cybersecurity project** that demonstrates both:

* **Operational monitoring**
* **Security-focused analysis**

---

## ⭐ If you like this project, don't forget to star the repo!

```


If you want, I can do the **full repo package next** 🔥
```
