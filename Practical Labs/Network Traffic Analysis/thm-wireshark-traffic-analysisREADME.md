# Network Security Analysis: MITM & Traffic Interception Lab

## 📌 Executive Summary
This report documents a technical investigation of a Man-in-the-Middle (MITM) attack via ARP Poisoning, conducted in a controlled environment. The analysis focuses on identifying attacker footprints, quantifying intercepted traffic, and extracting sensitive data from unencrypted HTTP sessions.

---

## 1. Tactical Intelligence & Methodology

### Attacker Identification (Layer 2)
The first stage of the investigation involved identifying the attacker's presence on the local network. By analyzing ARP traffic, we identified an abnormal volume of requests originating from a single MAC address.

**Wireshark Filter:** `arp.opcode == 1 && eth.src == 00:0c:29:e2:18:b4`

* **Metric:** 284 ARP Requests crafted by the attacker.
* **Analyst Mindset:** A high frequency of ARP requests in a short time window is a classic Indicator of Compromise (IoC) for network scanning or the initiation of an ARP Poisoning attack.

<img width="993" height="501" alt="image" src="https://github.com/user-attachments/assets/79cf4af3-9fe9-4d2d-a9e1-df67798093b8" />

> *Caption: Identification of ARP Scanning originated by the attacker's MAC address.*

---

## 2. Traffic Diversion Analysis

### HTTP Interception (Layer 7)
Once the ARP table was successfully poisoned, the victim's traffic was rerouted through the attacker's machine. We quantified the volume of application-layer data intercepted.

**Wireshark Filter:** `http && eth.dst == 00:0c:29:e2:18:b4`

* **Metric:** 90 HTTP packets received by the attacker.
* **Analyst Mindset:** The presence of HTTP traffic destined for the attacker's MAC confirms a successful MITM state. This proves that the logical destination (IP) and physical destination (MAC) are mismatched on the network.

<img width="991" height="499" alt="image" src="https://github.com/user-attachments/assets/75936b4e-fe5d-4d5e-b22b-4a1900922525" />


> *Caption: HTTP traffic flow being diverted to the attacker's hardware.*

---

## 3. Data Exfiltration & Credential Harvesting

### Credential Sniffing
The analysis focused on `POST` requests directed at the `/userinfo.php` endpoint. Since the protocol used was standard HTTP (Port 80), all data was transmitted in plain text.

**Wireshark Filter:** `http.request.method == "POST"`

* **Metric:** 6 unique sniffed username & password entries.
* **Analyst Mindset:** The lack of TLS/SSL encryption allows an attacker to perform Deep Packet Inspection (DPI) and extract credentials directly from "HTML Form URL Encoded" fields.

> <img width="889" height="506" alt="image" src="https://github.com/user-attachments/assets/40513567-37b4-4395-8c26-38f814319b2e" />
> <img width="525" height="95" alt="image" src="https://github.com/user-attachments/assets/980d2762-a780-4b5a-a5fc-185cea5fab1a" />
> <img width="544" height="110" alt="image" src="https://github.com/user-attachments/assets/23f9d89d-20d9-4bdc-908b-c1598a93db97" />

> *Caption: Visualizing clear-text credentials (uname/pass) within the HTTP payload.*

---

## 4. Targeted Forensic Search

### Case Study: Client986
To demonstrate surgical data extraction, a targeted search was performed for a specific user using a binary string search within the packet details.

* **Target:** `Client986`
* **Extracted Password:** `clientnothere!`

> <img width="996" height="464" alt="image" src="https://github.com/user-attachments/assets/b03e59ff-b784-48d6-9359-da349d8693e3" />

> *Caption: Specific password recovery using binary string search (Ctrl+F).*

### Case Study: Client354
Further investigation into privacy leaks revealed that non-credential data (comments/messages) was also fully exposed.

* **Target:** `Client354`
* **Extracted Comment:** `Nice work!`

> <img width="990" height="434" alt="image" src="https://github.com/user-attachments/assets/0f3fc3b1-e075-4d93-8943-7d011ccf9865" />


> *Caption: Intercepting form messages (comment field).*

---

## 5. Mitigation & Defensive Strategy (Blue Team)

To mitigate this type of attack vector, the following defenses are recommended:

1.  **Enforce HTTPS:** Implementation of TLS/SSL certificates would render intercepted payloads unreadable.
2.  **Dynamic ARP Inspection (DAI):** Configure switches to validate ARP packets against a trusted database (DHCP Snooping).
3.  **Static ARP:** For critical gateways, use static ARP entries to prevent table overwrites.
4.  **Network Monitoring:** Implement IDS/IPS signatures to alert on ARP storms or rapid MAC address changes associated with a single IP.

---
*Report developed as part of a Professional Cybersecurity Portfolio.*
