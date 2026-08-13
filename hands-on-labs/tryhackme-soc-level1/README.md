# TryHackMe — SOC Level 1 Path (Hands-On Practice)

## 🎯 Objective

To apply the networking, OS, security theory, SIEM, MITRE ATT&CK, and incident response knowledge from earlier in this repo to realistic, simulated SOC analyst scenarios through TryHackMe's SOC Level 1 learning path.

## 🧰 Platform

- **Platform:** [TryHackMe](https://tryhackme.com) — SOC Level 1 path
- **Rooms completed so far:** 3

---

## Room 1: Junior Security Analyst Intro

**Objective:** Get a first hands-on look at a day in the life of a Security Analyst, using a simulated SIEM dashboard.

### What I Did
- Reviewed a simulated **SIEM Dashboard** showing live alerts with severity levels
- Identified a **Critical** alert: unauthorized SSH login attempts followed by a successful login from a suspicious IP (`221.181.185.159`) — a classic brute-force pattern
- Used the **IP Hunter** tool to investigate/verify the malicious IP before taking further action
- **Escalated** the alert to the SOC team lead (Will) for review
- Used the **Firewall** tab to block the malicious IP, receiving a confirmation flag after successful containment
  ![SIEM Dashboard with alerts](room1-siem-dashboard.png)

![Firewall block confirmation](room1-firewall-block.png)

### Key Concept Reinforced
This mirrored the exact **Identification → Escalation → Containment** flow I documented conceptually in my Incident Response Basics writeup — except this time performed hands-on, in order, against a live simulated dashboard.

---

## Room 2: SOC Role in Blue Team

**Objective:** Understand the different roles within a SOC/Blue Team and match each to the type of incident they're responsible for.

### What I Did
An interactive challenge required matching 7 security professionals to 7 different incident scenarios:

| Scenario | Correct Role |
|---|---|
| SIEM alert about firewall brute-force — needs triage | **SOC L1 Analyst** |
| Phishing malware — needs deep analysis | **SOC L2 Analyst** |
| Ransomware hit — needs immediate response | **CERT Lead** |
| PCI DSS compliance audit needed | **GRC Auditor** |
| Check new software version for vulnerabilities | **Penetration Tester** |
| SIEM unavailable due to storage limit | **SOC Engineer** |
| Threat group (FIN7) actively targeting the company | **Threat Researcher** |
![Role matching challenge](room2-role-matching.png)
### Key Concept Reinforced
- **L1 vs L2:** L1 handles initial triage; L2 handles deeper investigation
- Major incidents (like ransomware) go to a dedicated **emergency response team (CERT)**, not L1
- Infrastructure/tooling issues go to **engineers**, not analysts
- Understanding **threat actor tactics** (like FIN7) is a Threat Intelligence function — this directly connects to the MITRE ATT&CK framework covered in my earlier writeup

---

## Room 3: SOC L1 Alert Triage

**Objective:** Learn and apply a systematic approach to triaging SOC alerts, from prioritization through to closing the investigation.

### The Alert Triage Workflow
This room outlined the full process an L1 analyst follows:

```
Prioritize alerts → Assign to self → Move to "In Progress" →
Read alert details (name, description, host, IP, user) →
Investigate in SIEM (follow a workbook/playbook if one exists) →
Make a verdict (True Positive / False Positive) →
Escalate if needed → Add analysis comment → Close the alert
```

### What I Did
Working with a simulated alert queue:

| Alert | Severity | Status |
|---|---|---|
| Double-Extension File Creation | High | Awaiting action |
| **Potential Data Exfiltration** | **Critical** | *(selected — highest priority)* |
| Download from GitHub Repository | Low | Awaiting action |
| Unusual VPN Login Location | Medium | Closed (False Positive) |
| Bruteforce Attack from External | Medium | Closed (True Positive) |

- Identified **"Potential Data Exfiltration"** as the correct first-priority alert based on Critical severity and "Awaiting action" status
- Assigned the alert to myself and moved it to **"In Progress"**
- Reviewed alert fields (host, process, user, target file) to understand the reported activity
- Investigated the activity in the simulated SIEM before making a verdict
- Successfully triaged the alert and received a completion flag
![Alert queue](room3-alert-queue.png)

![Triage workflow diagram](room3-workflow-diagram.png)
### Key Concept Reinforced
- **Not every alert is a real threat** — the "Unusual VPN Login Location" alert in the queue had already been closed as a **False Positive**, while "Bruteforce Attack from External" was a confirmed **True Positive**. Distinguishing between the two is one of the most important daily judgment calls an L1 analyst makes
- **Prioritization by severity** (Critical > High > Medium > Low) determines what gets worked first when multiple alerts are open at once — directly reflecting real SOC workload management

---

## 🧠 Overall Key Takeaway

These three rooms, taken together, walk through the complete lifecycle of a SOC L1 analyst's core responsibilities:
1. **Understand your team structure** (Room 2) — know who to escalate to and why
2. **Detect and respond to a live threat** (Room 1) — investigate, escalate, contain
3. **Systematically triage a queue of alerts** (Room 3) — prioritize, investigate, verdict, close

Every concept applied here connects back to earlier writeups in this repo: the brute-force pattern matches what I found in Splunk and mapped to MITRE ATT&CK (T1110), and the escalation/containment steps mirror the PICERL incident response lifecycle I documented conceptually beforehand.

## 📌 Notes

This writeup will be updated as I complete additional rooms in the SOC Level 1 path (and potentially LetsDefend) going forward.
