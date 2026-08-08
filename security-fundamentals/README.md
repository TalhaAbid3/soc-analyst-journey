# Security Fundamentals — Core Concepts

## 🎯 Objective

To learn and document the foundational security concepts every SOC analyst needs: the CIA Triad, common attack types, and the difference between encryption and hashing.

## 🔺 The CIA Triad

Every security control or attack can be mapped to one (or more) of these three principles:

### Confidentiality
Ensuring only authorized people can access data.
- **Achieved through:** encryption, access controls, multi-factor authentication (MFA)
- **Broken by:** data breaches, eavesdropping (e.g. reading plaintext HTTP traffic — as seen in my HTTP traffic analysis writeup)

### Integrity
Ensuring data is not altered without authorization — what's sent is what's received.
- **Achieved through:** hashing, digital signatures, checksums
- **Broken by:** data tampering, man-in-the-middle (MITM) attacks

### Availability
Ensuring systems/data are accessible when needed by authorized users.
- **Achieved through:** backups, redundancy, DDoS protection
- **Broken by:** DDoS attacks, ransomware, hardware failure

### Practice Scenarios (Self-Test)

| Scenario | Property Broken |
|---|---|
| Attacker floods a server with traffic until it crashes | **Availability** |
| Attacker intercepts and modifies a bank transfer's account number | **Integrity** |
| Attacker steals a password and reads someone's private email | **Confidentiality** |

## ⚔️ Common Attack Types

### Phishing
Fraudulent emails/messages designed to trick someone into revealing sensitive information or clicking a malicious link.
- **Spear phishing** — targeted at a specific individual, more convincing
- **Whaling** — targets high-level executives
- **Smishing** — phishing via SMS
- **Vishing** — phishing via phone call
- **Recognizable pattern:** impersonating a trusted brand + creating urgency + directing to a fake login page

### Malware
Any software designed to cause harm.
- **Virus** — attaches itself to files to spread
- **Worm** — self-replicates across a network without needing a host file
- **Trojan** — disguises itself as legitimate software
- **Spyware** — secretly monitors activity (keylogging, screen capture)
- **Adware** — displays unwanted ads (lower severity, still unwanted)

### Ransomware
Encrypts a victim's files and demands payment for the decryption key. A well-known real-world example is WannaCry (2017), which affected hospitals and organizations globally.

### DDoS (Distributed Denial of Service)
A botnet (network of compromised devices) floods a target with traffic to make it unavailable to legitimate users. Directly attacks the **Availability** leg of the CIA Triad.

### SQL Injection
Malicious SQL code inserted into input fields (e.g. login forms) to manipulate or extract data from a backend database. Example: entering `' OR '1'='1` into a login field to attempt an authentication bypass.

### Man-in-the-Middle (MITM)
An attacker secretly positions themselves between two communicating parties to intercept or alter the traffic. This is exactly why unencrypted protocols (like plain HTTP, seen in my HTTP analysis writeup) are risky — anyone on the network path can read or modify the data.

## 🔐 Encryption vs Hashing

These two are commonly confused but serve very different purposes.

### Encryption
Converts data into an unreadable format that can be **reversed** back to its original form using a key.

- **Symmetric encryption:** one key used for both encryption and decryption (fast, but the key must be shared securely). Example: AES
- **Asymmetric encryption:** a public key (shareable) encrypts, and only the matching private key can decrypt (slower, but avoids the key-sharing risk). Example: RSA — this is the mechanism behind TLS/HTTPS, which I observed as the "Client Hello" step right after the TCP handshake in my Wireshark captures

### Hashing
Converts data into a fixed-length, unique "fingerprint." Hashing is **one-way (irreversible)** — there is no key that turns a hash back into the original input.

- **Used for:** securely storing passwords (a site stores a hash of the password, not the password itself) and verifying data integrity (if a file's hash changes, the file has been altered)
- **Common algorithms:** MD5 (outdated, considered weak), SHA-256 (modern, widely used)

### Quick Comparison

| | Encryption | Hashing |
|---|---|---|
| Reversible? | Yes, with the correct key | No, one-way only |
| Purpose | Securely transmit/store data | Verify integrity, store passwords securely |
| Example use | HTTPS traffic, encrypted files | Password storage, file integrity checks |

## 🧠 Key Takeaway (SOC Relevance)

- The CIA Triad gives a shared vocabulary for classifying *what* an incident actually compromised — a SOC analyst's first question during triage is often "which part of CIA is affected here?"
- Recognizing the common attack types by their behavioral patterns (e.g. phishing's urgency + impersonation pattern, or a sudden traffic spike suggesting DDoS) speeds up initial incident classification
- Understanding that passwords should appear as **hashes**, not plaintext or reversible encryption, in logs or databases is a basic sanity check when investigating whether a system has been configured securely
- Since HTTPS relies on asymmetric encryption during the TLS handshake and then switches to faster symmetric encryption for the actual session, recognizing this in packet captures (as seen in my TCP handshake writeup) connects directly to this theory

## 📌 Notes

This is my first purely conceptual writeup (not tool-based), following the completion of Linux and Windows OS fundamentals. Next, I plan to move into SIEM tools (Splunk/ELK) to start applying these concepts to real log analysis and alert triage — the core daily work of a SOC analyst.
