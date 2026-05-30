# TryHackMe: Passive Reconnaissance Complete Write-up 🔍

- **Room Link**: [TryHackMe - Passive Reconnaissance](https://tryhackme.com)
- **Learning Path**: Jr Penetration Tester / Red Team Foundations
- **Difficulty**: Easy
- **Focus**: OSINT, Passive Information Gathering, DNS Enumeration, Banner Grabbing

---

## 📖 Room Overview
Passive reconnaissance involves gathering intelligence about a target infrastructure without directly interacting with their active systems or triggering defensive alerts. This write-up documents the step-by-step methodologies and tools used to complete the tasks in this room.

---

## 🛠️ Phase 1: Passive OSINT & DNS Enumeration

### 1. WHOIS Lookup
Executed to query public domain registries to identify ownership, registration dates, and target name servers.
```bash
whois tryhackme.com
```

### 2. DNS Interrogation via `nslookup` & `dig`
Used native CLI tools to map the target's subdomains, Mail Servers (MX), and IP bindings without touching the destination network.
```bash
# Finding Mail Exchange (MX) records
nslookup -type=MX tryhackme.com

# Digging all available records (ANY)
dig tryhackme.com ANY
```

---

## ⚔️ Phase 2: Active Reconnaissance & Banner Grabbing

In the practical lab tasks, we analyzed a live target machine to fingerprint the underlying web server infrastructure using two different approaches:

### Approach A: Manual Probing via Telnet
By establishing a raw TCP connection to Port 80, we grabbed the server header line manually.
```bash
telnet <MACHINE_IP> 80
```
**Request sent:**
```text
GET / HTTP/1.1
host: telnet
[Press Enter Twice]
```

**Captured Response (Leaked Data):**
```text
HTTP/1.1 200 OK
Server: nginx/1.6.2
Connection: keep-alive
```

### Approach B: Automated Scanning via Nmap (Alternative)
To achieve the same intelligence footprint programmatically, we can utilize the Nmap Scripting Engine (NSE):
```bash
nmap -sV --script=banner -p 80 <MACHINE_IP>
```

### 📊 Discovered Intelligence Matrix

| Target Component | Extracted Value |
| :--- | :--- |
| **Running Server Name** | `nginx` |
| **Software Version** | `1.6.2` |
| **HTTP Protocol Version** | `1.1` |
| **Connection Configuration** | `keep-alive` |

---

## 🛡️ Blue Team Defensive Remediation

Exposing exact web server names and software versions (`nginx/1.6.2`) allows threat actors to look up matching public CVEs on Exploit-DB for immediate initial access exploitation. 

### Hardening Steps Implemented:
1. **Disable Server Tokens**: In the Nginx configuration file (`nginx.conf`), set the following directive under the `http` context:
   ```nginx
   server_tokens off;
   ```
2. **Result**: This successfully obfuscates the version number from the HTTP response headers, mitigating information disclosure risks.
3.
