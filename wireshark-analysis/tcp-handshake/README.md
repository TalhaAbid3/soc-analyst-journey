# TCP 3-Way Handshake — Packet Analysis

## 🎯 Objective

To capture and analyze the TCP 3-way handshake process using Wireshark, and understand how a connection is established between a client and a server before data is exchanged.

## 🧰 Setup

- **Tool used:** Wireshark
- **Environment:** Kali Linux VM (VirtualBox)
- **Target:** Visited a website in Firefox while capturing traffic (TLS connection over HTTPS)
- **Filter applied:** `tcp.stream eq 0`

## 🔍 Steps Performed

1. Opened Wireshark and started a capture on the `eth0` interface
2. Opened Firefox (inside Kali) and navigated to a website
3. Stopped the capture once the page loaded
4. Applied the filter `tcp.stream eq 0` to isolate a single TCP conversation
5. Identified the first three packets of the stream to observe the handshake

## 📸 Findings

### Packet 1 (No. 5) — SYN (Client → Server)
- **Source IP / Port:** 10.0.2.15 / 36082
- **Destination IP / Port:** 140.248.129.91 / 443
- **Flag:** SYN
- **Sequence Number:** Seq = 0

![SYN Packet](screenshot-syn.png)

The client (Kali VM) sends a SYN packet to the server, requesting to open a connection and proposing an initial sequence number.

### Packet 2 (No. 12) — SYN, ACK (Server → Client)
- **Source IP / Port:** 140.248.129.91 / 443
- **Destination IP / Port:** 10.0.2.15 / 36082
- **Flags:** SYN, ACK
- **Sequence / Acknowledgment Number:** Seq = 0, Ack = 1

![SYN-ACK Packet](screenshot-syn-ack.png)

The server acknowledges the client's SYN and responds with its own SYN, confirming it's ready to establish the connection.

### Packet 3 (No. 13) — ACK (Client → Server)
- **Source IP / Port:** 10.0.2.15 / 36082
- **Destination IP / Port:** 140.248.129.91 / 443
- **Flag:** ACK
- **Sequence / Acknowledgment Number:** Seq = 1, Ack = 1

![ACK Packet](screenshot-ack.png)

The client acknowledges the server's response, completing the 3-way handshake. The TCP connection is now established.

## 🔐 Bonus Observation

Right after the handshake completed, packet No. 15 showed a **TLS Client Hello** (`SNI=ads.mozilla.org`), which marks the start of the TLS handshake used to encrypt the connection (since this was an HTTPS/port 443 connection). This confirms that:

**TCP handshake (connection setup) → TLS handshake (encryption setup) → Encrypted Application Data**

is the correct order of operations for a secure HTTPS session.

## 🧠 Key Takeaway (SOC Relevance)

Understanding the normal TCP handshake pattern is important for a SOC analyst because:
- Abnormal patterns (e.g. repeated SYN packets without a completed handshake) can indicate a **SYN flood / DoS attack**
- Unexpected RST (reset) packets can indicate blocked or refused connections, useful in detecting port scanning activity
- Recognizing legitimate handshake and TLS negotiation behavior helps quickly spot what's *not* normal in captured traffic (e.g. malformed handshakes, downgrade attacks)

## 📌 Notes

This was my first hands-on packet capture and analysis using Wireshark inside a Kali Linux VM. Next, I plan to analyze a DNS query and an HTTP (non-encrypted) request to compare with this TLS-secured connection.
