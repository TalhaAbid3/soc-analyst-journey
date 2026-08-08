# Windows Event Viewer — Security Log Analysis

## 🎯 Objective

To explore Windows Event Viewer, understand the structure of the Security log, and learn to filter and interpret key Event IDs relevant to security monitoring — such as logons, failed logons, and process creation.

## 🧰 Environment

- **OS:** Windows 10/11 (host machine)
- **Tool:** Event Viewer (`eventvwr.msc`)

## 🔍 Steps Performed

1. Opened Event Viewer and reviewed the **Overview and Summary** page to understand the volume of events being logged (Audit Success, Audit Failure, Warning, Information counts over the last hour / 24 hours / 7 days)
2. Opened the **Security** log under Windows Logs (33,000+ total events recorded)
3. Used **Filter Current Log** to isolate specific Event IDs one at a time
4. Reviewed event details (General tab) for each filtered result

## 📸 Findings

### Overview Summary
The Overview page showed a healthy pattern for a normal personal machine:
- **Audit Success:** 22,402 events over 7 days (routine logons, privilege assignments, account checks)
- **Audit Failure:** only 2 events over 7 days — a very low number, consistent with no brute-force or unauthorized access attempts

### Event ID 4672 — Special Logon
- **Description:** "Special privileges assigned to new logon"
- **Task Category:** Special Logon
- Occurs whenever a user with administrative privileges logs on. Appeared frequently, always paired closely in time with a 4624 event — expected behavior for an admin account logging in normally.

### Event ID 4624 — Successful Logon
- Confirms a successful login occurred
- Appeared alongside 4672 events, consistent with normal admin session starts

### Event ID 4625 — Failed Logon
- Filtered specifically for this ID: **0 results found**
- This is a good sign — it means no failed login/authentication attempts were recorded, so there's no evidence of brute-force attempts or incorrect credential attempts on this machine

### Event ID 4798 — User Account Management
- **Description:** "A user's local group membership was enumerated"
- Appeared frequently as routine background activity — Windows periodically checks group memberships as part of normal account management processes, not something inherently suspicious on its own

### Event ID 4688 — Process Creation
- Filtered for this ID: only **11 events** found out of 33,000+ total Security events
- **Description:** "A new process has been created"
- All 11 events were clustered within the same few seconds (around 1:39 AM on a single date) — consistent with a burst of processes launching together, such as during a software install/update or scheduled task, rather than isolated suspicious activity

## 🧠 Key Concepts Learned

- **Filtering by Event ID** is the primary way to cut through a large volume of logs (33,000+ events) and focus on what matters
- Event IDs are grouped into **Task Categories** (Logon, Special Logon, Process Creation, User Account Management, etc.) which makes it easier to understand what kind of activity is being recorded
- Windows separates logs into **Application, Security, Setup, System, and Forwarded Events** — Security is generally the most relevant log for SOC-type investigation
- The **"Includes/Excludes Event IDs"** filter field accepts comma-separated IDs or ranges, allowing multiple event types to be checked at once

## 🧠 Key Takeaway (SOC Relevance)

- **Event ID 4625 (failed logon)** is one of the most important IDs to monitor — a sudden spike in failed logons, especially against multiple usernames or from unusual times, is a classic indicator of a brute-force attack
- **Event ID 4672 + 4624 pairing** is normal for legitimate admin logins, but this pattern occurring at unusual hours, or for an unfamiliar account, would be worth investigating
- **Event ID 4688 (process creation)** is heavily used in malware/incident investigations — an unexpected or unfamiliar process name appearing here, especially spawned by another suspicious process, is a common early indicator of compromise
- Establishing what "normal" looks like (as done here) — low audit failures, expected admin-login pairing, clustered legitimate process creation — is the baseline a SOC analyst needs before being able to recognize what's *abnormal*

## 📌 Notes

This is my first Windows-focused writeup, following the completion of the Linux fundamentals section. Next, I plan to explore basic PowerShell commands and continue building toward Active Directory concepts, before moving on to Security Fundamentals (CIA Triad, common attack types) in my learning path.
