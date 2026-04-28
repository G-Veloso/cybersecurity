# Forensic Investigation: Insider Threat & Data Exfiltration Analysis
**Platform:** Splunk Enterprise (SIEM)  
**Dataset:** BOTS v2  
**Case ID:** 2026-INT-0423  
**Status:** Closed - Malicious Intent Confirmed  

---

## Executive Summary
This report documents a digital forensic investigation into the activities of **Amber Turing**, a former employee suspected of corporate espionage. By correlating disparate log sources (HTTP and SMTP) within the Splunk SIEM, I identified a clear **Cyber Kill Chain**—from initial reconnaissance of a competitor's infrastructure to the final exfiltration of a confidential biotechnology patent.

## Technical Methodology
The investigation utilized **Splunk Search Processing Language (SPL)** to pivot between network layers. The primary focus was identifying data movement across:
* **Application Layer (L7):** Analysis of HTTP URI paths and SMTP metadata.
* **Network Layer (L3):** Correlation of internal IP `10.0.2.101` with external adversarial domains.

---

## 🛡️ Phase 1: Web Reconnaissance (HTTP Analysis)
Initial triggers identified the internal host `10.0.2.101` engaging in persistent scanning of the competitor's web server (`www.berkbeer.com`). 

**Findings:**
The suspect successfully mapped the competitor's organizational structure by accessing an image directory containing executive contact details.
* **Target Artifact:** `/images/ceoberk.png`
* **HTTP Method:** `GET`

><img width="785" height="667" alt="image" src="https://github.com/user-attachments/assets/7a95081c-7c99-40bc-b68a-cad9b067f896" />


---

## 📧 Phase 2: Communication & Contact (SMTP Analysis)
Following the reconnaissance phase, the suspect initiated unauthorized contact with the competitor's leadership.

**Technical Challenge:** Standard SIEM field parsing failed to populate the `content_body` and `attach_filename` fields in the statistics table. I pivoted to **Raw Data Analysis** to extract the true nature of the communication.

**Identified Targets:**
* **Primary Contact:** Martin Berk (CEO) - `mberk@berkbeer.com`
* **Secondary Contact:** Harald Bernhard - `hbernhard@berkbeer.com`

> <img width="1067" height="706" alt="image" src="https://github.com/user-attachments/assets/321d3ab8-c5cb-43bd-9cca-699d7c8aa95a" />


---

## 🚀 Phase 3: Data Exfiltration (The "Smoking Gun")
The investigation reached its climax when I intercepted the transmission of a high-value intellectual property (IP) asset.

**Critical Findings:**
* **Exfiltrated File:** `Saccharomyces_cerevisiae_patent.docx`
* **Evasion Tactic:** The suspect attempted to bypass internal DLP (Data Loss Prevention) monitoring by utilizing a rogue personal email domain: `ambersthebest@yeastiebeastie.com`.

> <img width="784" height="357" alt="image" src="https://github.com/user-attachments/assets/4e6cfe30-7188-4c81-bc80-ec736505d7b5" />


---

## 🛑 Indicators of Compromise (IOCs)
The following indicators have been extracted to update the SIEM's correlation rules and Firewall blocklists:

| Type | Value |
| :--- | :--- |
| **Adversarial Domains** | `berkbeer.com`, `yeastiebeastie.com` |
| **Suspect Personal Email** | `ambersthebest@yeastiebeastie.com` |
| **Sensitive Artifacts** | `/images/ceoberk.png`, `Saccharomyces_cerevisiae_patent.docx` |
| **Compromised Internal Host** | `10.0.2.101` |

---

## 🛠️ Security Recommendations (Architecture Level)
Based on this investigation, the following architectural controls are recommended for implementation at **C6 Bank** (or similar financial/tech environments):

1. **DLP Policy Enforcement:** Implement content-aware Data Loss Prevention to block outgoing SMTP traffic containing keywords such as "patent" or scientific nomenclature (e.g., *Saccharomyces*).
2. **Behavioral Anomalies:** Configure SIEM alerts for "Sequence of Events": *Access to Competitor Domain* -> *Followed by* -> *External SMTP Transmission*.
3. **Domain Reputation Filtering:** Implement strict DNS filtering for newly registered or niche domains (e.g., `yeastiebeastie.com`) often used for exfiltration.

---
