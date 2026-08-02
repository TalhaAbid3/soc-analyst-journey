# HTTP Traffic Analysis — Packet Analysis

## 🎯 Objective

To capture and analyze plaintext HTTP traffic using Wireshark, understand the structure of an HTTP request/response, and observe why unencrypted traffic is a security risk compared to HTTPS.

## 🧰 Setup

- **Tool used:** Wireshark
- **Environment:** Kali Linux VM (VirtualBox)
- **Target:** `http://neverssl.com` (a site intentionally built for testing plain HTTP, without HTTPS)
- **Filter applied:** `http`

## 🔍 Steps Performed

1. Opened Wireshark and started a capture on the `eth0` interface
2. Opened Firefox (inside Kali) and navigated to `http://neverssl.com`
3. Stopped the capture once the page loaded
4. Applied the filter `http` to isolate the HTTP request and response packets
5. Right-clicked the GET request → **Follow → HTTP Stream** to view the full plaintext exchange

## 📸 Findings

### Packet 153 — HTTP GET Request
- **Source IP / Port:** 10.0.2.15 / 54860
- **Destination IP / Port:** 34.223.124.45 / 80
- **Request Line:** `GET / HTTP/1.1`
- **Host:** `neverssl.com`
http-packet-list.png  
### Packet 156 — HTTP Response
- **Source IP / Port:** 34.223.124.45 / 80
- **Destination IP / Port:** 10.0.2.15 / 54860
- **Status Line:** `HTTP/1.1 200 OK`
- **Content-Type:** `text/html; charset=UTF-8`
- **Length:** 1027 bytes

### Follow HTTP Stream — Full Plaintext Exchange
http-get-request.png
**Request headers observed:**
```
GET / HTTP/1.1
Host: neverssl.com
User-Agent: Mozilla/5.0 (X11; Linux x86_64; rv:140.0) Gecko/20100101 Firefox/140.0
Accept: text/html,application/xhtml+xml,application/xml;q=0.9,*/*;q=0.8
Accept-Encoding: gzip, deflate
Connection: keep-alive
```
http-response.png 
**Response headers observed:**
```
HTTP/1.1 200 OK
Server: Apache/2.4.66
Content-Type: text/html; charset=UTF-8
Content-Length: 1900
```

Followed by the raw, readable HTML content of the page (`<html><head>...`).

## 🧠 Key Concepts Learned

- **Request line breakdown:** `GET / HTTP/1.1` — the method (GET), the requested path (`/`), and the protocol version
- **Host header:** identifies which site is being requested, since one server/IP can host multiple domains
- **User-Agent header:** reveals the client's browser and OS — useful for fingerprinting, and something a SOC analyst can use to spot unusual clients (e.g. scripted bots vs. real browsers)
- **Status codes:** `200 OK` means success. Other common codes to recognize during log/traffic analysis: `301/302` (redirect), `403` (forbidden), `404` (not found), `500` (server error) — repeated 403/404s from one source can indicate scanning activity
- **Server header (fingerprinting risk):** the response revealed `Server: Apache/2.4.66` — exposing the exact web server software and version is a known security weakness, since attackers can search for vulnerabilities specific to that version
- **Plaintext exposure:** every part of this exchange — headers, cookies (if present), and page content — was fully readable with no encryption. If this had been a login form, credentials would have appeared here in plaintext too

## 🔐 HTTP vs HTTPS — Why This Matters

This capture is a direct contrast to the earlier TLS/HTTPS capture I analyzed for the TCP handshake writeup, where the "Follow TCP Stream" output was unreadable encrypted data. Here, everything is human-readable plaintext.

This is the core reason HTTPS (TLS) matters: on an HTTP connection, anyone positioned on the network path — for example on shared/public Wi-Fi — can passively read the entire exchange, including any sensitive data submitted through forms. This is the basis of many **man-in-the-middle (MITM)** attacks.
http-stream.png 

## 🧠 Key Takeaway (SOC Relevance)

- Recognizing plaintext HTTP traffic in a network capture is an immediate red flag if sensitive data (credentials, tokens, personal info) is involved
- Server/software fingerprinting information leaking in headers is something SOC analysts flag during vulnerability assessments — it reduces the effort needed for an attacker to target known CVEs for that software version
- Establishing a baseline for normal HTTP request patterns (headers, User-Agents, request paths) helps in spotting anomalies like malformed requests, unusual User-Agents (common in bots/scanners), or unexpected status code spikes

## 📌 Notes

This is my third Wireshark writeup, following TCP handshake and DNS query analysis. Together, these three captures show the full picture of establishing a connection (TCP), resolving a domain (DNS), and exchanging data (HTTP) — plus why encrypting that last step with TLS/HTTPS is critical.
