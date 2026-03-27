# splunk-siem-lab
Created a splunk dashboard showing failed logins and activity
# 🔐 Splunk SIEM SOC Lab – Attack Simulation & Detection

## 📌 Overview

This project demonstrates a hands-on cybersecurity home lab where I built a small **Security Operations Center (SOC)** environment using Splunk. I simulated real-world attacks from a Kali Linux machine and detected them using centralized log collection, dashboards, and alerts.

The goal of this lab was to gain practical experience in:

* Log analysis
* Threat detection
* SIEM configuration
* Incident investigation

---

## 🧱 Lab Architecture

```
Kali Linux (Attacker)
        ↓
Windows 10 Target + Splunk Universal Forwarder
        ↓
Splunk Enterprise SIEM (Windows 10)
```

* **SIEM:** Splunk Enterprise
* **Forwarder:** Splunk Universal Forwarder
* **Attacker:** Kali Linux
* **Log Sources:** Windows Event Logs, PowerShell Logs, Sysmon

---

## ⚙️ Setup & Configuration

### Splunk SIEM

* Installed Splunk Enterprise on Windows 10
* Configured receiving on port **9997**
* Verified ingestion using internal logs (`index=_internal`)

### Forwarder Configuration

* Installed Splunk Universal Forwarder on target machine
* Configured `inputs.conf` to collect:

  * Security logs
  * System logs
  * Application logs
  * PowerShell Operational logs
  * Sysmon logs
* Forwarded logs to SIEM via TCP port 9997

---

## ⚔️ Attack Simulation

I simulated a realistic attack chain from Kali Linux:

### 🔑 Brute Force Login Attempts

* Generated multiple failed login attempts
* Detected via Event ID **4625**

### ⚡ PowerShell Execution

* Executed commands (`whoami`, `ipconfig`, remote script execution)
* Captured using PowerShell logging

### 🔍 Reconnaissance (Discovery)

* Enumerated system information:

  * `net user`
  * `net localgroup administrators`
* Simulated attacker discovery phase

### 👤 Persistence (Account Creation)

```
net user hacker Pass123! /add
```

* Detected via Event ID **4720**

---

## 🔎 Detection & SPL Queries

### Failed Login Detection

```
index=main EventCode=4625
```

### Brute Force Detection

```
index=main EventCode=4625 
| bucket _time span=1m 
| stats count by _time, Source_Network_Address 
| where count > 5
```

### PowerShell Activity

```
index=main sourcetype="WinEventLog:Microsoft-Windows-PowerShell/Operational"
```

### Account Creation Detection

```
index=main EventCode=4720
```

### Recon Command Detection

```
index=main EventCode=1 
| search CommandLine="*net user*" OR CommandLine="*whoami*" OR CommandLine="*ipconfig*"
```

---

## 📊 SOC Dashboard

Created a **SOC Monitoring Dashboard** with the following panels:

* Failed logins over time
* Top source IP addresses
* Most targeted user accounts
* PowerShell activity monitoring
* New user account creation
* Suspicious command execution (recon activity)

This dashboard provides real-time visibility into attacker behavior and system activity.

---

## 🚨 Alerting

### Brute Force Detection Alert

* Trigger: >5 failed logins within 1 minute
* Frequency: Every 1 minute
* Purpose: Detect credential-based attacks in real time

---

## 🔍 Investigation Workflow

Example investigation process:

1. Identify spike in failed logins
2. Pivot to source IP address
3. Analyze timeline of events
4. Detect suspicious PowerShell activity
5. Identify persistence via account creation

---

## 🧠 MITRE ATT&CK Mapping

| Tactic         | Technique           | ID    |
| -------------- | ------------------- | ----- |
| Initial Access | Brute Force         | T1110 |
| Execution      | PowerShell          | T1059 |
| Discovery      | Account Enumeration | T1087 |
| Persistence    | Account Creation    | T1136 |

---

## 🛠 Skills Demonstrated

* SIEM configuration (Splunk Enterprise)
* Log ingestion & forwarder setup
* Windows Event Log analysis
* Threat detection using SPL
* Dashboard creation & visualization
* Alert development
* Incident investigation & log correlation
* Understanding of MITRE ATT&CK

---

## ⚠️ Challenges & Lessons Learned

* Resolved log ingestion issues caused by incorrect index configuration
* Fixed forwarder authentication errors (default credentials restriction)
* Debugged data pipeline using `_internal` logs and input status
* Learned importance of proper log source configuration

---

## 🚀 Conclusion

This lab simulates a real-world SOC workflow by combining attack simulation, detection engineering, and incident response. It demonstrates practical, hands-on cybersecurity skills relevant to entry-level SOC and security analyst roles.

---
