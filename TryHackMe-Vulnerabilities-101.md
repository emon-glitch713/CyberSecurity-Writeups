# TryHackMe: Vulnerabilities 101 — Lab Walkthrough & Write-up

## 📝 Overview
This repository contains a comprehensive, professional write-up for the **Vulnerabilities 101** room on TryHackMe. The objective of this lab is to understand how vulnerabilities are classified, researched using public databases, and exploited within an enterprise infrastructure. It simulates a real-world penetration testing engagement following standard industry methodologies.

---

## 🛑 Task 4: Vulnerability Research & Intelligence Gathering

In this section, public threat intelligence and vulnerability databases were used to analyze global vulnerability statistics and exploit ownership.

### 🔍 Tools Used:
* **NVD (National Vulnerability Database):** Used for analyzing CVE (Common Vulnerabilities and Exposures) metrics and tracking security flaws.
* **Exploit-DB:** A CVE-compliant database tailored for public exploits and shellcode, maintained for security researchers.

### 🏁 Questions & Answers:
1. **Question:** Using NVD, how many CVEs were published in July 2021?
   * **Answer:** `1585`
2. **Question:** Who is the author of Exploit-DB?
   * **Answer:** `OffSec`

---

## 🎯 Task 6: Showcase — Exploiting ACKme's Application

### 📋 Scenario & Engagement Scope
Acting as a Junior Penetration Tester at **ThePentestingCo**, we shadowed a Senior Engineer to conduct an authorized infrastructure assessment against **ACKme IT Service's** web application. 

### ⚙️ Methodology & Step-by-Step Solution

#### Phase 1: Information Gathering & Reconnaissance
* **Action:** Manual banner grabbing and source code inspection were conducted on the target web infrastructure.
* **Discovery:** The application was found to be running an outdated portal software: **`ACKMe Portal v1.5.2`**.

#### Phase 2: Vulnerability Research (Threat Modeling)
* **Action:** The discovered software and version components were cross-referenced against public exploit databases (such as *Vulnerability Bank™*).
* **Result:** A known **Remote Code Execution (RCE)** vulnerability was identified matching `ACKMe Portal v1.5.2`. This flaw allows unauthenticated remote attackers to execute arbitrary system commands on the host OS.

#### Phase 3: Exploitation (Gaining Access)
* **Action:** A tailored exploit script targeting the specific RCE vulnerability was loaded. The script was configured with the target host IP (`240.228.189.136`).
* **Execution:** Run the custom exploit tool from the attacker's terminal environment using the following command syntax:

```bash
user@thepentestingco:~$ run exploit -u http://240.228.189.136
```

* **Output Analysis:** The exploit successfully bypassed input validation controls on the web server, dropped the payload, and returned a stable interactive reverse shell:
```text
Running exploit!
Exploit complete! Launching shell...
```

#### Phase 4: Post-Exploitation & Privilege Escalation Verification
* **Action:** Once the initial access shell was established, local system enumeration was performed to determine the security context and privilege level.
* **Command Executed:**
```bash
administrator@ackmeitservices:~\$ whoami
```
* **Result:** The terminal output returned `ACKME\Administrator`. This indicates that the web server process was improperly configured running with local system administrator privileges, completely compromising the target Windows Active Directory domain node without requiring further privilege escalation.

---

### 🏁 Flag Capture & Remediation Solution

* **Question:** Follow along with the showcase of exploiting ACKme's application to the end to retrieve a flag. What is this flag?
* **Answer:** `THM{ACKME_ENGAGEMENT}`

### 🛡️ Recommended Remediation / Mitigation
To secure the ACKme IT Service application against this specific cyber attack vector, the following security patches should be implemented immediately:
1. **Patch Management:** Upgrade `ACKMe Portal` from legacy version `v1.5.2` to the latest secure vendor release to patch the RCE vulnerability.
2. **Principle of Least Privilege (PoLP):** Reconfigure the web application service account. It should run under a low-privileged local service account (`NT AUTHORITY\LOCAL SERVICE`) rather than as a full Domain/Local `Administrator`.
3. **Web Application Firewall (WAF):** Deploy a WAF in front of the web application to detect and block automated exploit payloads and command injection attempts.

---
## 🏆 Conclusion
* **Lab Status:** 100% Completed
* **Skills Demonstrated:** Vulnerability Research, RCE Exploitation, Infrastructure Enumeration, Privilege Verification.
*
