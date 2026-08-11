# MITRE ATT&CK Framework — Notes & Exploration

## 🎯 Objective

To understand the structure of the MITRE ATT&CK framework — how it categorizes real-world attacker behavior into Tactics and Techniques — and connect it to security events I've already investigated in earlier writeups (Wireshark captures, Windows Event Viewer, Splunk).

## 🧰 Resource Used

- **Website:** [attack.mitre.org](https://attack.mitre.org)
- **Matrix explored:** Enterprise

## 🔺 Core Concept: Tactics vs Techniques

- **Tactic** = the attacker's *goal* ("why" they're doing something)
- **Technique** = the attacker's *method* ("how" they achieve that goal)
- **Sub-technique** = a more specific variant of a technique (e.g. T1110 "Brute Force" has sub-techniques like T1110.001 "Password Guessing")

Every technique in the framework has a unique ID (e.g. `T1110`), a parent tactic, and details on detection and mitigation — making it a shared, standardized vocabulary across the security industry.

## 🗺️ Common Tactics (Simplified Attack Lifecycle)

| Tactic | Meaning |
|---|---|
| **Initial Access** | Getting into a system for the first time (e.g. phishing, exploiting a vulnerability) |
| **Execution** | Running malicious code on the target |
| **Persistence** | Maintaining access even after a restart or credential change |
| **Privilege Escalation** | Going from limited access to higher-level (admin/root) access |
| **Credential Access** | Stealing passwords, tokens, or other credentials |
| **Lateral Movement** | Moving from one compromised system to others across a network |
| **Exfiltration** | Extracting stolen data out of the environment |

## 📸 Techniques Explored

### T1110.001 — Brute Force: Password Guessing
- **Tactic:** Credential Access
- **Sub-technique of:** T1110 (Brute Force)
- **Description:** Adversaries with no prior knowledge of valid credentials systematically guess passwords to gain access. The technique notes that repeated guessing "could cause numerous authentication failures and account lockouts," depending on the organization's login policies.

**Direct connection to my own practice:** This matches exactly what I observed in my Splunk SIEM writeup — three failed login attempts for the same user (`guest`) from the same IP (`192.168.1.55`) within about 30 seconds, followed by a system-generated error. That pattern is a textbook example of this technique in action.

### T1566.003 — Phishing: Spearphishing via Service
- **Tactic:** Initial Access
- **Sub-technique of:** T1566 (Phishing)
- **Description:** Adversaries send phishing messages through third-party services (social media, personal webmail) rather than direct enterprise email channels, since these platforms typically have less strict security policies. A common example given is creating fake social media profiles to message employees with fake job offers to build rapport before delivering a malicious link or attachment.

**Why this is Initial Access, not Credential Access:** Phishing is about the *first* point of entry into an environment — even if the end goal might eventually be credential theft, the tactic classification reflects the attacker's immediate objective at that stage (getting a foothold), not the downstream result.

## 🧠 Key Concepts Learned

- The parent/sub-technique structure (e.g. `T1110` → `T1110.001`) allows the framework to be both broad (general technique categories) and specific (exact variants), useful for precise incident documentation
- Each technique page includes real detection guidance — for Brute Force, this includes monitoring authentication logs for excessive failed attempts, which is exactly the kind of log analysis I practiced in both Windows Event Viewer (Event ID 4625) and Splunk
- Tactics represent stages of an attack's lifecycle, roughly in order from Initial Access through to Impact/Exfiltration — this gives a mental model for "where in the attack" a given piece of suspicious activity might sit

## 🧠 Key Takeaway (SOC Relevance)

- MITRE ATT&CK gives SOC analysts a **common language** — instead of describing an alert in vague terms, an analyst can say "this looks like T1110 (Brute Force) under Credential Access," which is immediately understood by anyone else in the industry
- Mapping real detected activity (like the failed logins I found in Splunk) back to a specific MITRE technique is a standard part of incident documentation and threat intelligence reporting
- Understanding the tactic lifecycle helps anticipate an attacker's *next* likely move — e.g. after Credential Access (successful brute force), a reasonable next step to watch for would be Privilege Escalation or Lateral Movement

## 📌 Notes

This is my first MITRE ATT&CK writeup, tying together concepts from my earlier Wireshark, Windows Event Viewer, and Splunk practice. Next, I plan to look into Incident Response basics (the incident lifecycle) before starting hands-on practice with TryHackMe's SOC Level 1 path.
