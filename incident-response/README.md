# Incident Response Basics — The IR Lifecycle

## 🎯 Objective

To learn the structured process a SOC analyst follows when responding to a security incident, and connect each phase to a realistic scenario based on the brute-force pattern I identified in my earlier Splunk practice.

## 🔺 The Incident Response Lifecycle (PICERL)

A security incident isn't handled with an ad-hoc reaction — it follows a structured, repeatable process with six phases:

### 1. Preparation
Everything done **before** an incident occurs: SIEM tools configured (like the Splunk setup from my earlier writeup), alert rules defined, an incident response plan documented, and the team/roles already established. This phase happens continuously, not during an active incident.

### 2. Identification (Detection)
Recognizing that an incident is actually happening. This is the step I practiced directly — spotting the "Multiple failed login attempts detected" pattern in Splunk logs was an act of identification.

### 3. Containment
Taking fast action to stop the damage from spreading further, without necessarily fixing the root cause yet.
- **Short-term containment:** immediate action, e.g. blocking the offending IP address at the firewall, or temporarily disabling the targeted account
- **Long-term containment:** a temporary but more stable fix while a permanent solution is prepared

### 4. Eradication
Removing the threat completely — addressing the root cause, not just the symptoms. Examples: removing malware from a system, patching the vulnerability that was exploited, resetting any compromised credentials.

### 5. Recovery
Carefully restoring affected systems back to normal operation, with close monitoring to confirm the threat doesn't reappear.

### 6. Lessons Learned (Post-Incident Review)
Reviewing what happened after the incident is resolved: what worked, what could have been detected faster, and what changes (to tools, policies, or training) should be made going forward. This is also where formal reporting and team debriefs happen — not during the active incident.

## 📸 Applying the Lifecycle to My Splunk Scenario

Using the brute-force pattern from my earlier SIEM practice writeup (repeated failed logins for user `guest` from `192.168.1.55`), here's how each phase would apply:

| Phase | Action in this scenario |
|---|---|
| **Preparation** | Splunk already configured to ingest and search authentication logs |
| **Identification** | Searching `source="sample.log" ERROR` surfaces the "Multiple failed login attempts detected" event |
| **Containment** | Block `192.168.1.55` at the firewall, or temporarily disable the `guest` account |
| **Eradication** | Investigate whether the account was compromised; reset credentials if needed; address any misconfiguration (e.g. why a `guest` account with weak protections existed at all) |
| **Recovery** | Re-enable the account (if legitimate) with monitoring in place, or confirm the block is holding if malicious |
| **Lessons Learned** | Document the incident, consider whether account lockout policies or MFA should be added to prevent recurrence |

## 🧠 Key Concepts Learned

- Containment and Eradication are often confused, but they're distinct: **Containment stops the spread** (fast, immediate), while **Eradication removes the threat entirely** (root cause fix, can take longer)
- Preparation isn't a one-time step — it's the ongoing foundation (tools, playbooks, trained team) that makes every other phase possible to execute quickly
- Lessons Learned is not optional — skipping it means the same type of incident is likely to happen again without any improvement to detection or defenses

## 🧠 Key Takeaway (SOC Relevance)

- Following a structured lifecycle instead of reacting randomly ensures nothing gets missed under pressure — this matters because real incidents often involve time pressure and incomplete information
- Every earlier piece of this learning path connects here: **Identification** relies on the log analysis skills from Wireshark/Event Viewer/Splunk, and mapping an incident to a **MITRE ATT&CK technique** (like T1110 Brute Force) makes the Identification and reporting steps more precise and standardized
- Understanding this lifecycle is also directly useful for interviews — "walk me through how you'd respond to X" is a common SOC analyst interview question, and PICERL gives a clear structure to answer it

## 📌 Notes

This is my final conceptual/theory writeup before moving into hands-on practice. Next step: starting the TryHackMe "SOC Level 1" learning path to apply everything covered so far (networking, packet analysis, OS fundamentals, security concepts, SIEM search, MITRE ATT&CK, and now incident response) in realistic, simulated SOC scenarios.
