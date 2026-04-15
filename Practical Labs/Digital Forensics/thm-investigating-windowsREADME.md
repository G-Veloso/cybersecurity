# Forensic Investigation Report: Case #2026-003
**Target System:** Windows Server 2016 (Investigation Room)
**Analyst:** [Your Name/GitHub User]
**Status:** Completed

## 1. Executive Summary
During the forensic analysis of the compromised Windows environment, a multi-stage attack was identified, ranging from initial web shell access to credential dumping and defense evasion via DNS manipulation.

## 2. Timeline of Compromise
| Event Type | Timestamp (UTC/Local) | Details |
| :--- | :--- | :--- |
| **Initial Access** | 03/02/2019 04:04:49 PM | First successful logon with special privileges (Event ID 4672). |
| **Exploitation** | 03/02/2019 04:37:25 PM | Web Shell execution and account password modifications. |
| **Data Exfiltration** | 03/02/2019 04:37:00 PM | Credential dump file `127.0.0.1` created in Downloads. |

## 3. Findings & Evidence

### A. Initial Access (Web Shell)
The attacker gained access through a web server (IIS) vulnerability.
* **Path:** `C:\inetpub\wwwroot`
* **Artifacts:** `b.jsp`, `tests.jsp`, and a disguised `shell.gif`.
* **Method:** Execution of JSP scripts to gain Remote Code Execution (RCE).

 <img width="652" height="196" alt="image" src="https://github.com/user-attachments/assets/4b332567-05ce-4033-95e6-ead72ecddc26" />


### B. Persistence & C2 Infrastructure
Persistence was established through scheduled tasks and hidden network listeners.
* **Persistence Mechanism:** Scheduled Task named `Clean file system`.
* **Malicious Script:** `C:\TMP\nc.ps1` (PowerShell Netcat).
* **Open Ports:** `1348` (Reverse Shell) and `1337` (Backdoor).
* **C2 Server IP:** `76.32.97.132`

  <img width="986" height="568" alt="image" src="https://github.com/user-attachments/assets/edf71af3-7bb6-4880-be32-85032f3c4528" />


### C. Credential Dumping
The tool **Mimikatz** was utilized to extract NTLM hashes.
* **Evidence:** A file named `127.0.0.1` was found containing hashes for the `Administrator`, `Guest`, and `trinity` accounts.
* **Observation:** The Administrator's hash indicated a weak or empty password policy during the breach.

<img width="789" height="567" alt="image" src="https://github.com/user-attachments/assets/7be46f20-c1c7-4916-b68f-0ed8f135493a" />


### D. Defense Evasion (DNS Poisoning)
The attacker performed local DNS poisoning by modifying the Windows `hosts` file to block security updates and redirect legitimate traffic.
* **Target Domain:** `google.com` / `www.google.com`
* **Redirect IP:** `76.32.97.132` (Attacker's Infrastructure)
* **Impact:** Redirecting search engine traffic to a malicious IP facilitates phishing or prevents the analyst from accessing online security tools (e.g., VirusTotal).

<img width="988" height="375" alt="image" src="https://github.com/user-attachments/assets/150f55ac-938b-4af5-8384-bdeba1c2335d" />


```text
# Excerpt from C:\Windows\System32\drivers\etc\hosts
76.32.97.132 google.com
76.32.97.132 [www.google.com](https://www.google.com)
127.0.0.1 [www.virustotal.com](https://www.virustotal.com)
