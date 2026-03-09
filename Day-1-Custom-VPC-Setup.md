# Setup-01 — Custom VPC & Subnet Configuration on AWS

![AWS](https://img.shields.io/badge/Platform-AWS-FF9900?style=flat-square&logo=amazonaws)
![VPC](https://img.shields.io/badge/Service-VPC-232F3E?style=flat-square)
![Status](https://img.shields.io/badge/Status-Completed-brightgreen?style=flat-square)

---

## 📋 Overview

This document covers the setup of an isolated AWS cloud network environment to host the Enterprise SOC Lab infrastructure. A custom VPC with a public subnet, internet gateway, and routing table was configured to simulate a real enterprise network — providing the foundation for deploying Active Directory, Splunk SIEM, endpoint machines, and a web server.

---

## 🏗️ Architecture

```
Internet
    │
    ▼
Internet Gateway (SOC-IGW)
    │
    ▼
SOC-VPC (10.0.0.0/16)
    └── SOC-Public-Subnet (10.0.1.0/24)
              ├── Active Directory Server
              ├── Splunk SIEM Server
              ├── Endpoint Machine (Windows)
              └── Web Server (Ubuntu/DVWA)
```

---

## ⚙️ Configuration Steps

### Step 1 — Create Custom VPC

**Navigate:** AWS Console → VPC → Your VPCs → Create VPC

| Setting | Value |
|---|---|
| Resource | VPC Only |
| Name | SOC-VPC |
| IPv4 CIDR Block | 10.0.0.0/16 |
| IPv6 | None |
| Tenancy | Default |

> `10.0.0.0/16` provides 65,536 IP addresses — sufficient for all SOC lab components with room to scale.

---

### Step 2 — Create Public Subnet

**Navigate:** VPC → Subnets → Create Subnet

| Setting | Value |
|---|---|
| VPC | SOC-VPC |
| Subnet Name | SOC-Public-Subnet |
| Availability Zone | Any |
| IPv4 CIDR Block | 10.0.1.0/24 |

> `10.0.1.0/24` provides 256 IP addresses — dedicated to all SOC lab EC2 instances.

---

### Step 3 — Create & Attach Internet Gateway

**Navigate:** VPC → Internet Gateways → Create Internet Gateway

| Setting | Value |
|---|---|
| Name | SOC-IGW |

After creation → **Actions → Attach to VPC → Select SOC-VPC**

> Without an internet gateway, EC2 instances cannot reach the internet for log forwarding, package installation, or API calls.

---

### Step 4 — Configure Route Table

**Navigate:** VPC → Route Tables → Select SOC-VPC route table → Edit Routes

**Add route:**

| Destination | Target |
|---|---|
| 0.0.0.0/0 | SOC-IGW (Internet Gateway) |

**Associate subnet:**

Route Table → Subnet Associations → Edit → Select `SOC-Public-Subnet`

---

### Step 5 — Enable Auto-Assign Public IP

**Navigate:** Subnets → SOC-Public-Subnet → Edit Subnet Settings

- ✅ Enable Auto-assign public IPv4 address

> This ensures every EC2 instance launched in this subnet automatically receives a public IP — required for RDP/SSH access and internet connectivity.

---

## ✅ Infrastructure Summary

| Component | Value | Status |
|---|---|---|
| VPC Name | SOC-VPC | ✅ Created |
| VPC CIDR | 10.0.0.0/16 | ✅ Configured |
| Subnet Name | SOC-Public-Subnet | ✅ Created |
| Subnet CIDR | 10.0.1.0/24 | ✅ Configured |
| Internet Gateway | SOC-IGW | ✅ Attached |
| Route Table | 0.0.0.0/0 → SOC-IGW | ✅ Configured |
| Auto-assign Public IP | Enabled | ✅ Active |

---

## 🔗 Next Steps

This VPC forms the foundation for all subsequent lab components:

- [Setup-02 → Splunk Server Deployment](02_Splunk_Deployment.md)
- [Setup-03 → Active Directory Setup](03_Active_Directory_Setup.md)
- [Setup-04 → Web Server (DVWA) Deployment](04_Web_Server_Setup.md)

---

*SOC Lab Infrastructure | AWS Free Tier | Author: [Nadil](https://github.com/Nadhil-an)*
