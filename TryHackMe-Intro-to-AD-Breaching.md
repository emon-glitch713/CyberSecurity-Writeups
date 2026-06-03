# # TryHackMe: Intro to AD Breaching – Comprehensive Defensive & Mitigation Guide 🔍

* **Room Link:** [Intro to AD Breaching](https://tryhackme.com)
* **Learning Path:** Red Team Foundations / Active Directory Hardening
* **Difficulty:** Medium
* **Focus:** Mitigations, Secrets Management, NTLM Hardening, Device Hardening

---

## 💻 Room Overview
Active Directory (AD) is the primary target for attackers in an enterprise network. Breaching an AD environment often relies on minor misconfigurations, weak password hygiene, or unhardened legacy protocols. This write-up provides an in-depth defensive blueprint mapped against common AD breaching techniques like Password Spraying, LDAP Passback, File-based Coercion, and NTLM relay attacks.

---

## ## 🛡️ Phase 1: Secrets Management (Defending CI/CD & Repositories)
During active red team engagements, hardcoded credentials in public or internal repositories (Git, CI/CD logs, configuration files) offer the easiest initial access vectors.

### Defensive Measures:
* **Dedicated Secrets Vault:** Never hardcode credentials in source code. Move all secrets to enterprise vaults like **HashiCorp Vault**, **Azure Key Vault**, or **AWS Secrets Manager**.
* **Pre-commit Hooks:** Implement automation tools like `TruffleHog` or `Gitleaks` within the developer workflow to scan code and block commits containing plaintext secrets.
* **Continuous Auditing:** Regularly audit repository commit history. Simply deleting a secret in the latest commit does not remove it from Git history; the credential must be explicitly rotated and invalidated immediately.
* **Log Redaction:** Mask or redact sensitive tokens and variables within CI/CD build logs to restrict access from unauthorized personnel.

---

## ## 🔑 Phase 2: Enterprise Password Policies & Account Lockout
Password spraying allows attackers to authenticate against thousands of corporate accounts using a few common passwords (e.g., `Summer2026!`), effectively bypassing traditional account lockout policies.

### Defensive Measures:
* **Length Over Complexity:** Enforce a minimum password length of **14 characters**. Long passphrases provide stronger cryptographic resistance than short, complex passwords.
* **Custom Banned Password Lists:** Deploy solutions like **Azure AD Password Protection** to block easily guessable patterns (e.g., `CompanyName+Year`).
* **Calibrated Lockout Thresholds:** A threshold that is too strict (e.g., 3 attempts) creates operational disruption via Denial of Service (DoS). A balanced threshold (e.g., 5-10 attempts with a 30-minute observation window) thwarts mass spraying while keeping operations smooth.
* **SIEM Monitoring:** Configure alerts for distributed authentication failures across multiple distinct accounts, which is a classic signature of a password spraying campaign.

---

## ## 🖨️ Phase 3: Device Hardening (Preventing LDAP Passback Attacks)
Attackers frequently exploit network devices like rogue printers or scanners via the **LDAP Passback** technique to capture plaintext service account credentials.

### Defensive Measures:
* **Credential Rotation:** Change all default factory administrative credentials on printers, scanners, and IoT devices prior to network deployment.
* **Enforce LDAPS (Port 636):** Completely transition from plaintext LDAP (Port 389) to encrypted **LDAPS (Port 636)**. Encryption ensures that even if an attacker intercepts a session, they capture encrypted traffic instead of plaintext credentials.
* **Interface Isolation:** Restrict access to device administration interfaces via dedicated management VLANs and strict IP access control lists (ACLs).
* **Least Privilege Service Accounts:** Use dedicated, low-privilege service accounts for LDAP directory lookups. These accounts must never belong to the *Domain Admins* group and should only have read-only permissions for necessary directory objects.

---

## ## 📁 Phase 4: File Share Security & NTLM Hardening
File-based coercion attacks trick a victim's machine into automatically executing an NTLM authentication request towards an attacker-controlled listener (e.g., using `Responder`).

### Defensive Measures:
* **Principle of Least Privilege:** Strictly restrict write permissions on network file shares. Users should only have access to directories vital to their specific role.
* **File Extension Auditing:** Use File Integrity Monitoring (FIM) or Endpoint Detection and Response (EDR) rules to monitor and alert on suspicious file types like `.url`, `.lnk`, `.scf`, and `desktop.ini` appearing on shared drives.
* **Disable NTLMv1:** Force the network to reject old LM and NTLMv1 responses. Enforce **NTLMv2** as the bare minimum authentication level via Group Policy Object (GPO):
  * **Path:** `Network Security: LAN Manager authentication level`
  * **Setting:** Set to *"Send NTLMv2 response only. Refuse LM & NTLM."*
* **Enforce SMB Signing:** Mandatory SMB signing on all domain controllers and workstations mitigates SMB Relay attacks completely.
* **Egress Firewall Rules:** Block outbound SMB traffic (**TCP Port 445**) at the perimeter firewall. Workstations rarely have a legitimate business need to establish external SMB connections.

---

## ## 🌐 Phase 5: Network Segmentation & Access Control
Flat network architectures allow attackers to move laterally across the domain instantly once an initial foothold is secured.

### Defensive Measures:
* **Management VLAN Isolation:** Isolate critical management interfaces (e.g., ESXi consoles, printer admin panels, Jenkins dashboards, Git servers) inside dedicated management VLANs accessible only to authorized administrators via secure jump boxes.
* **Network Access Control (NAC):** Restrict access to internal services that do not require broad exposure across the corporate network.
* **Enforce Multi-Factor Authentication (MFA):** Implement robust MFA on all internet-facing portals, VPN gateways, and critical internal infrastructure. MFA ensures that a compromised password alone is insufficient for an attacker to pivot into the environment.

---

## ## 📝 Lab Assessment Answers

For completion of this module, the following configurations solve the assessment questions:
1. **What Group Policy setting can be configured to enforce NTLMv2 and refuse older LM and NTLM responses?**
   `Network Security: LAN Manager authentication level`
2. **What port should be used instead of port 389 to ensure LDAP traffic is encrypted?**
   `636`

---

## ## 🎯 Conclusion
Defending an Active Directory environment requires shifting from a reactive mindset to a proactive, hardened security posture. By restricting legacy NTLM protocols, enforcing strict network segmentation, securing CI/CD pipelines, and forcing encrypted LDAPS communications, organizations can systematically dismantle the attack paths utilized by modern red teams and real-world threat actors.
