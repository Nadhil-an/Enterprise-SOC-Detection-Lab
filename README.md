# 🛡️ Enterprise SOC Detection Lab — Splunk + Windows + Active Directory + Web

![Splunk](https://img.shields.io/badge/SIEM-Splunk-FF6B35?style=flat-square&logo=splunk)
![MITRE ATT&CK](https://img.shields.io/badge/Framework-MITRE%20ATT%26CK-red?style=flat-square)
![Windows](https://img.shields.io/badge/Platform-Windows-0078D6?style=flat-square&logo=windows)
![Active Directory](https://img.shields.io/badge/Active%20Directory-soc.local-purple?style=flat-square)
![AWS](https://img.shields.io/badge/Cloud-AWS-FF9900?style=flat-square&logo=amazonaws)
![DVWA](https://img.shields.io/badge/Web-DVWA%20Labs-orange?style=flat-square)
![Labs](https://img.shields.io/badge/Detection%20Labs-13%20Completed-brightgreen?style=flat-square)

> A fully cloud-based Enterprise SOC lab deployed on AWS, covering **13 real-world attack detection scenarios** across three domains — Windows Endpoint, Active Directory, and Web Application attacks. Each lab includes full attack simulation, Windows Event Log and Apache log analysis, Splunk SPL detection queries, MITRE ATT&CK mapping, and a complete SOC investigation report.

---

## 🏗️ SOC Architecture

```
                        ┌─────────────────────────────────┐
                        │         Attacker Machine         │
                        └────────────────┬────────────────┘
                                         │
                                         ▼
                              Internet Gateway (SOC-IGW)
                                         │
                        ┌────────────────▼────────────────┐
                        │        SOC-VPC (10.0.0.0/16)    │
                        │                                  │
                        │  ┌─────────────────────────┐    │
                        │  │  Active Directory (DC)   │    │
                        │  │  Windows Server 2022     │    │
                        │  │  Domain: soc.local       │    │
                        │  └──────────┬──────────────┘    │
                        │             │ Splunk Forwarder   │
                        │  ┌──────────▼──────────────┐    │
                        │  │  Windows Endpoint        │    │
                        │  │  Domain-Joined Workstation│   │
                        │  └──────────┬──────────────┘    │
                        │             │ Splunk Forwarder   │
                        │  ┌──────────▼──────────────┐    │
                        │  │  Ubuntu Web Server       │    │
                        │  │  Apache2 + DVWA          │    │
                        │  └──────────┬──────────────┘    │
                        │             │ Splunk Forwarder   │
                        │  ┌──────────▼──────────────┐    │
                        │  │   Splunk Enterprise      │    │
                        │  │   Central SIEM           │    │
                        │  │   Port 8000 (UI)         │    │
                        │  │   Port 9997 (Receiving)  │    │
                        │  └──────────┬──────────────┘    │
                        │             │                    │
                        └─────────────┼────────────────────┘
                                      │
                                      ▼
                               SOC Analyst
```
<div>
  <p align="center">
    <img src="assets/soc-architecture.png" width="900">
  </p>
</div>


---

## 📋 Detection Lab Index

### 🖥️ Endpoint Attacks — `index=end_point`

| Lab | Attack | Event ID | MITRE | Severity |
|---|---|---|---|---|
| [E1](endpoint-attacks/E1_Malware_Execution_4688.md) | Malware Execution Simulation | 4688 | T1204, T1059 | 🔴 High |
| [E2](endpoint-attacks/E2_PowerShell_Encoded_4688.md) | Suspicious PowerShell Encoded Command | 4688 | T1059.001, T1027 | 🔴 High |
| [E3](endpoint-attacks/E3_Registry_Persistence_4657.md) | Persistence via Registry Run Key | 4657 | T1547.001 | 🔴 High |
| [E4](endpoint-attacks/E4_Privilege_Escalation_4732.md) | Privilege Escalation via Administrators Group | 4720, 4732 | T1098 | 🔴 Critical |
| [E5](endpoint-attacks/E5_Service_Creation_7045.md) | Malicious Service Creation | 7045 | T1543.003 | 🔴 Critical |

### 🏢 Active Directory Attacks — `index=windows` / `index=ad_logs`

| Lab | Attack | Event ID | MITRE | Severity |
|---|---|---|---|---|
| [AD-01](active-directory-attacks/AD-01_Brute_Force_RDP_4625.md) | Brute Force via RDP | 4625, 4624 | T1110 | 🔴 High |
| [AD-02](active-directory-attacks/AD-02_Account_Lockout_4740.md) | Account Lockout Detection | 4740, 4625 | T1110 | 🟡 Medium |
| [AD-03](active-directory-attacks/AD-03_User_Group_Creation.md) | Backdoor User & Group Creation | 4720, 4727, 4728 | T1136, T1098 | 🔴 Critical |

### 🌐 Web Application Attacks — `index=web_logs`

| Lab | Attack | Payload | MITRE | Severity |
|---|---|---|---|---|
| [W1](web-attacks/W1_SQL_Injection.md) | SQL Injection | `1' OR '1'='1` | T1190 | 🔴 High |
| [W2](web-attacks/W2_Brute_Force_Login.md) | Brute Force Login | Credential Stuffing | T1110 | 🔴 High |
| [W3](web-attacks/W3_Command_Injection.md) | Command Injection (RCE) | `127.0.0.1;whoami` | T1059 | 🔴 High |
| [W4](web-attacks/W4_File_Upload_WebShell.md) | File Upload / Web Shell | `shell.php?cmd=whoami` | T1505.003 | 🔴 Critical |
| [W5](web-attacks/W5_Local_File_Inclusion.md) | Local File Inclusion (LFI) | `../../../../etc/passwd` | T1006 | 🔴 High |

---

## 🏗️ Infrastructure Setup

| Step | Document | Purpose |
|---|---|---|
| 01 | [VPC Setup](setup/01_VPC_Setup.md) | Custom AWS VPC, subnet, internet gateway |
| 02 | [Splunk Deployment](setup/02_Splunk_Deployment.md) | Splunk Enterprise on Ubuntu EC2 |
| 03 | [Active Directory](setup/03_Active_Directory_Setup.md) | Windows Server DC, domain soc.local |
| 04 | [Endpoint Setup](setup/04_Endpoint_Deployment.md) | Windows endpoint, domain join, log forwarding |
| 05 | [Web Server Setup](setup/05_Web_Server_Setup.md) | Apache + DVWA on Ubuntu EC2 |

---

## 🗺️ MITRE ATT&CK Coverage

| Tactic | Technique | ID | Labs |
|---|---|---|---|
| Execution | Command & Scripting Interpreter | T1059 | E1, E2, W3 |
| Execution | PowerShell | T1059.001 | E2 |
| Execution | User Execution | T1204 | E1 |
| Defense Evasion | Obfuscated Files | T1027 | E2 |
| Persistence | Registry Run Keys | T1547.001 | E3 |
| Persistence | Windows Service | T1543.003 | E5 |
| Persistence | Create Account | T1136 | AD-03 |
| Privilege Escalation | Account Manipulation | T1098 | E4, AD-03 |
| Credential Access | Brute Force | T1110 | AD-01, AD-02, W2 |
| Initial Access | Exploit Public-Facing App | T1190 | W1 |
| Initial Access | Web Shell | T1505.003 | W4 |
| Discovery | Path Traversal | T1006 | W5 |

---

## 🔗 Attack Chain Correlation

### Endpoint Attack Chain
```
Malware Execution (4688)
    ↓
Encoded PowerShell (4688) ← Defense Evasion
    ↓
Registry Run Key (4657) ← Persistence
    ↓
Admin Group Escalation (4732) ← Privilege Escalation
    ↓
Service Creation (7045) ← SYSTEM-level Persistence
```

### Active Directory Attack Chain
```
RDP Brute Force (4625 × N)
    ↓
Successful Login (4624) ← OR ← Account Lockout (4740)
    ↓
Backdoor User Created (4720)
    ↓
Security Group Created (4727)
    ↓
User Added to Group (4728) ← Full Domain Persistence
```

---

## 📊 Splunk Index Reference

| Index | Source | Labs |
|---|---|---|
| `end_point` | Windows Endpoint Security & System logs | E1–E5 |
| `windows` | Active Directory Domain Controller logs | AD-01, AD-02 |
| `ad_logs` | AD-specific PowerShell provisioned logs | AD-03 |
| `web_logs` | Apache access & error logs (Ubuntu EC2) | W1–W5 |

---

## 📁 Repository Structure

```
Enterprise-SIEM-SOLUTION/
├── README.md
├── setup/
│   ├── 01_VPC_Setup.md
│   ├── 02_Splunk_Deployment.md
│   ├── 03_Active_Directory_Setup.md
│   ├── 04_Endpoint_Deployment.md
│   └── 05_Web_Server_Setup.md
├── endpoint-attacks/
│   ├── E1_Malware_Execution_4688.md
│   ├── E2_PowerShell_Encoded_4688.md
│   ├── E3_Registry_Persistence_4657.md
│   ├── E4_Privilege_Escalation_4732.md
│   └── E5_Service_Creation_7045.md
├── active-directory-attacks/
│   ├── AD-01_Brute_Force_RDP_4625.md
│   ├── AD-02_Account_Lockout_4740.md
│   └── AD-03_User_Group_Creation.md
├── web-attacks/
│   ├── W1_SQL_Injection.md
│   ├── W2_Brute_Force_Login.md
│   ├── W3_Command_Injection_RCE.md
│   ├── W4_File_Upload_WebShell.md
│   └── W5_Local_File_Inclusion.md
├── splunk-queries/
│   └── all_detection_queries.spl
└── assets/
    └── soc-architecture.png
```

---

## 🗺️ Roadmap

- [ ] Rewrite all 13 labs in standardised SOC report format
- [ ] Build unified Splunk dashboard covering all 3 attack categories
- [ ] Add Kerberoasting and Pass-the-Hash AD labs
- [ ] Add XSS and CSRF web attack labs
- [ ] Write automated Splunk correlation alert rules
- [ ] Add SOAR playbook responses per attack type

---

## 👤 Author

**Nadil** — Cybersecurity Student, Lovely Professional University
[![LinkedIn](https://img.shields.io/badge/LinkedIn-nadhilan-blue?style=flat-square&logo=linkedin)](https://www.linkedin.com/in/nadhilan/)
[![GitHub](https://img.shields.io/badge/GitHub-Nadhil--an-black?style=flat-square&logo=github)](https://github.com/Nadhil-an)

---


