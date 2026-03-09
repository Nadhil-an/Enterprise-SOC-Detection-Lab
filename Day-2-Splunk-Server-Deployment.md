# Setup-02 — Splunk Enterprise Deployment on AWS EC2

![AWS](https://img.shields.io/badge/Platform-AWS-FF9900?style=flat-square&logo=amazonaws)
![Splunk](https://img.shields.io/badge/SIEM-Splunk%20Enterprise-FF6B35?style=flat-square&logo=splunk)
![Ubuntu](https://img.shields.io/badge/OS-Ubuntu%2022.04-E95420?style=flat-square&logo=ubuntu)
![Status](https://img.shields.io/badge/Status-Completed-brightgreen?style=flat-square)

---

## 📋 Overview

Splunk Enterprise was deployed on an Ubuntu EC2 instance inside the custom SOC-VPC to act as the central SIEM for the Enterprise SOC Lab. All logs from Active Directory, Windows endpoints, and the web server are forwarded to this instance for real-time monitoring, detection, and investigation.

---

## 🏗️ Architecture



```
Windows Endpoint  ──┐
Active Directory  ──┼──► Splunk Universal Forwarder ──► Splunk Server (EC2) ──► SOC Analyst
Ubuntu Web Server ──┘                                     Port 9997 (receiving)    Port 8000 (UI)
```

<p align="center">
  <img src="assets/splunk-architecture.png" width="800">
</p>

---

## ⚙️ EC2 Instance Configuration

| Setting | Value |
|---|---|
| AMI | Ubuntu Server 22.04 LTS |
| Instance Type | t2.micro (Free Tier) |
| Network | SOC-VPC |
| Subnet | SOC-Public-Subnet |
| Auto-assign Public IP | Enabled |
| Storage | 30GB gp2 |

---

## 🔐 Security Group — Inbound Rules

| Port | Protocol | Purpose |
|---|---|---|
| 22 | TCP | SSH access for administration |
| 8000 | TCP | Splunk Web Interface |
| 9997 | TCP | Log receiving from Universal Forwarders |

---

## ⚙️ Deployment Steps

### Step 1 — Connect to EC2 via SSH

```bash
ssh -i your-key.pem ubuntu@<EC2-Public-IP>
```

---

### Step 2 — Update System Packages

```bash
sudo apt update && sudo apt upgrade -y
```

---

### Step 3 — Download Splunk Enterprise

```bash
wget -O splunk.tgz "https://download.splunk.com/products/splunk/releases/9.x.x/linux/splunk-9.x.x-linux-amd64.tgz"
```

> Get the latest download URL from: splunk.com/en_us/download/splunk-enterprise.html

---

### Step 4 — Extract and Install

```bash
tar -xvzf splunk.tgz
sudo mv splunk /opt/
cd /opt/splunk/bin
```

---

### Step 5 — Start Splunk and Configure Admin Account

```bash
sudo ./splunk start
```

- Accept license agreement → type `y`
- Set admin username
- Set admin password

---

### Step 6 — Enable Auto-Start on Boot

```bash
sudo ./splunk enable boot-start
```

---

### Step 7 — Verify Splunk is Running

```bash
sudo ./splunk status
```

Expected output:

```
splunkd is running (PID: XXXXX)
```

---

### Step 8 — Access Splunk Web Interface

Open browser and navigate to:

```
http://<EC2-Public-IP>:8000
```

Login with the admin credentials created in Step 5.

---

### Step 9 — Enable Log Receiving on Port 9997

Inside Splunk Web UI:

```
Settings → Forwarding and Receiving → Configure Receiving → New Receiving Port → 9997
```

This opens port 9997 to receive logs from Universal Forwarders installed on endpoint machines.

---

### Step 10 — Create SOC Lab Indexes

```
Settings → Indexes → New Index
```

Create the following indexes used across all detection labs:

| Index Name | Purpose |
|---|---|
| `end_point` | Windows endpoint logs |
| `windows` | Active Directory / Domain Controller logs |
| `ad_logs` | AD-specific event logs |
| `web_logs` | Apache web server logs from Ubuntu EC2 |

---

## ✅ Deployment Summary

| Component | Status |
|---|---|
| Ubuntu EC2 launched | ✅ |
| Security group configured | ✅ |
| Splunk Enterprise installed | ✅ |
| Web interface accessible on port 8000 | ✅ |
| Log receiving enabled on port 9997 | ✅ |
| Boot-start configured | ✅ |
| SOC indexes created | ✅ |

---

## 🔗 Next Steps

- [Setup-03 → Active Directory Setup](03_Active_Directory_Setup.md)
- [Setup-04 → Web Server (DVWA) Deployment](04_Web_Server_Setup.md)
- [Endpoint Attacks → E1 Malware Execution](../endpoint-attacks/E1_Malware_Execution_4688.md)

---

*SOC Lab Infrastructure | Splunk Enterprise 9.x | Ubuntu 22.04 | AWS Free Tier | Author: [Nadil](https://github.com/Nadhil-an)*
