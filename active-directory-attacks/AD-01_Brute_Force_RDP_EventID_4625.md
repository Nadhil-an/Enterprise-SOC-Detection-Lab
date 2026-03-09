# AD-01 — Brute Force Attack Detection via RDP

![MITRE](https://img.shields.io/badge/MITRE-T1110-red?style=flat-square)
![EventID](https://img.shields.io/badge/Event%20ID-4625%20%7C%204624-blue?style=flat-square)
![Severity](https://img.shields.io/badge/Severity-High-orange?style=flat-square)
![Platform](https://img.shields.io/badge/Platform-Active%20Directory-purple?style=flat-square)
![Tool](https://img.shields.io/badge/SIEM-Splunk-FF6B35?style=flat-square)

---

## 📋 Executive Summary

A brute force attack was simulated against an Active Directory Administrator account via RDP. Multiple failed login attempts were generated from the attacker machine, producing Event ID 4625 (Failed Logon) in Windows Security logs. Splunk SIEM successfully detected the attack pattern by correlating repeated failures from a single source IP and identified a subsequent successful login (Event ID 4624) — confirming unauthorized access. This lab demonstrates end-to-end detection capability for credential-based attacks in a domain environment.

---

## 🧩 Lab Environment

| Component | Details |
|---|---|
| Attacker Machine | Analyst Laptop |
| Target Server | Active Directory Domain Controller (AWS EC2) |
| Protocol | RDP (Remote Desktop Protocol) — Port 3389 |
| SIEM | Splunk (index = windows) |
| Log Source | Windows Security Event Log |

---

## 🎯 Objectives

- Simulate a brute force credential attack against an AD account via RDP
- Generate and capture Event ID 4625 (Failed Logon) in Windows Security logs
- Detect repeated failures from a single source IP using Splunk
- Correlate failed attempts with a successful login (Event ID 4624)
- Perform a full SOC analyst investigation

---

## 🔴 Attack Simulation

### Phase 1 — Generate Failed Login Attempts

From the attacker machine, RDP was opened and connection was attempted to the AD server IP. Wrong passwords were entered repeatedly (6–10 times) against the Administrator account.

```
Target: Active Directory Server
Username: Administrator
Password: [incorrect — repeated]
Attempts: 6–10
```

Each failed attempt generates the following in Windows Security logs:

```
Event ID  : 4625
Log Source: Security
Category  : Logon
Reason    : Bad Password / Unknown Username
```

**Screenshot — Failed RDP Login Attempt:**


<p align="center">
  <img src="../assets/ad-login.png" width="700">
</p>


---

### Phase 2 — Successful Login (Escalation Scenario)

After repeated failures, the correct password was entered to simulate a successful brute force completion.

```
Event ID  : 4624
Log Source: Security
Category  : Logon
Type      : Interactive / Network
```

**Screenshot — Successful AD Login (Event ID 4624):**

<p align="center">
  <img src="../assets/ad-success.jpg" width="700">
</p>

---

## 🔍 Indicators of Compromise (IOCs)

| Type | Value | Context |
|---|---|---|
| Source IP | 192.168.x.x | Attacker machine — RDP origin |
| Target Account | Administrator | Account under brute force |
| Target Server | AD Domain Controller | RDP target |
| Event ID | 4625 | Failed logon — repeated |
| Event ID | 4624 | Successful logon — post brute force |
| Protocol | RDP / TCP 3389 | Attack vector |
| Failure Reason | Bad Password | Credential guessing confirmed |

---

## 📊 Splunk Detection

### Query 1 — Detect Failed Logins by Source IP

```spl
index=windows EventCode=4625
| stats count by Account_Name, Source_Network_Address
| sort - count
```

**What this reveals:**
- Which account is being targeted
- Which source IP is responsible
- Total number of failed attempts per IP

**Screenshot — Splunk Detection (Event ID 4625):**

<p align="center">
  <img src="../assets/ad-bruteforce.jpg" width="700">
</p>

---

### Query 2 — Correlate Failed + Successful Login (Critical)

```spl
index=windows (EventCode=4625 OR EventCode=4624)
| stats count by Account_Name, Source_Network_Address, EventCode
| sort - count
```

**What this reveals:**
- Same source IP generating both 4625 and 4624
- Confirms brute force succeeded
- Identifies the compromised account

---

### Query 3 — Timeline of Attack

```spl
index=windows (EventCode=4625 OR EventCode=4624)
| table _time Account_Name Source_Network_Address EventCode
| sort _time
```

**What this reveals:**
- Exact sequence of failed then successful logins
- Time window of the attack
- Whether access was gained

---

## 🚨 Detection Alert Rule

**Alert Name:** Brute Force — Multiple Failed RDP Logons  
**Trigger:** EventCode=4625 count > 5 within 2 minutes from same Source IP  
**Escalation Trigger:** EventCode=4624 from same Source IP after 4625 cluster  
**Severity:** High  
**Recommended Action:** Block source IP, reset target account, investigate post-login activity

```spl
index=windows EventCode=4625
| bucket _time span=2m
| stats count by _time, Account_Name, Source_Network_Address
| where count > 5
| sort - count
```

---

## 🕒 Attack Timeline

| Time | Event | Event ID | Notes |
|---|---|---|---|
| T+00:00 | RDP connection initiated | — | Attacker connects to AD server |
| T+00:10 | Failed login attempt #1 | 4625 | Wrong password |
| T+00:20 | Failed login attempt #2–6 | 4625 | Repeated credential guessing |
| T+00:45 | Successful login | 4624 | Correct password found |
| T+01:00 | Splunk alert triggered | — | Correlation rule fires |

---

## 🗺️ MITRE ATT&CK Mapping

| Tactic | Technique | ID | Observation |
|---|---|---|---|
| Credential Access | Brute Force | T1110 | Repeated failed RDP logins |
| Credential Access | Password Guessing | T1110.001 | Manual/automated guessing |
| Initial Access | Valid Accounts | T1078 | Successful login post-brute force |
| Lateral Movement | Remote Services (RDP) | T1021.001 | RDP used as attack vector |

---

## ⚠️ Risk Assessment

| Factor | Value |
|---|---|
| Attack Type | Brute Force via RDP |
| Target | Administrator Account |
| Severity | **High** |
| Impact | Unauthorized Domain Access |
| Lateral Movement Risk | High — Administrator privileges |
| Data Exposure Risk | High — Full domain access if successful |

**Why this is critical:**
- Administrator account targeted — full domain compromise if successful
- RDP provides interactive session — attacker can move laterally
- No account lockout policy = unlimited attempts possible
- Automated tools can attempt thousands of passwords per minute

---

## 🛡️ SOC Analyst Investigation Checklist

When Event ID 4625 cluster is detected, a SOC analyst should:

- [ ] Identify source IP — internal or external?
- [ ] Check source IP reputation via ThreatLens / VirusTotal / AbuseIPDB
- [ ] Determine targeted account — privileged or standard?
- [ ] Count total failed attempts and time window
- [ ] Check for Event ID 4624 from same source — was login successful?
- [ ] If successful — check for Event ID 4648, 4672 (privileged login)
- [ ] Look for lateral movement indicators post-login
- [ ] Check if account lockout policy is configured (Event ID 4740)
- [ ] Block source IP at firewall if external
- [ ] Reset compromised account credentials
- [ ] Escalate to Tier 2 if successful login confirmed

---

## 🔧 Recommended Defensive Actions

| Action | Priority |
|---|---|
| Enable Account Lockout Policy (3–5 attempts) | 🔴 Immediate |
| Restrict RDP access — VPN only | 🔴 Immediate |
| Block source IP at firewall | 🔴 Immediate |
| Enable MFA for RDP / privileged accounts | 🟠 High |
| Deploy Splunk alert rule for 4625 clustering | 🟠 High |
| Review all logins from source IP (Event ID 4624) | 🟠 High |
| Rename default Administrator account | 🟡 Medium |
| Deploy honeypot account to detect guessing | 🟡 Medium |

---

## 🧾 Investigation Summary

A brute force attack was simulated against an Active Directory Administrator account via RDP from an attacker-controlled machine. The attack generated multiple Event ID 4625 (Failed Logon) entries within a short timeframe, all originating from the same source IP address.

Splunk SIEM detected the attack pattern through statistical analysis of failed logon events, identifying the source IP responsible and the targeted account. A subsequent Event ID 4624 (Successful Logon) from the same source IP confirmed that the brute force attempt succeeded, resulting in unauthorized interactive access to the domain controller.

This simulation confirms that Windows Security logging and Splunk detection rules can effectively identify brute force attacks — however, the absence of an Account Lockout Policy allowed unlimited attempts, which represents a critical defensive gap.

**Lab Status:** ✅ Attack Simulated → ✅ Logged → ✅ Detected in Splunk → ✅ Investigated

---

## 🔗 Related Labs

- [AD-02 — Account Lockout Detection (Event ID 4740)](../AD-02_Account_Lockout_4740.md)
- [AD-03 — Backdoor User & Group Creation (Event ID 4720/4727/4728)](../AD-03_User_Group_Creation.md)
- [E4 — Local Privilege Escalation via Administrators Group](../../endpoint-attacks/E4_Privilege_Escalation_4732.md)

---

*Lab environment: Active Directory on AWS EC2 Windows Server | Splunk Free | Author: [Nadil](https://github.com/Nadhil-an)*
