# 📝 DAY 5 – Endpoint Deployment & Log Forwarding to Splunk
# 👶 Super Simple Step-By-Step Guide

---

# 🧠 What Are We Building?

Think of this lab like this:

👨‍💻 Attacker → 🌍 Internet → ☁ AWS → 🖥 Windows Computer → 📦 Splunk → 🧑‍💼 SOC Analyst

We are making a small company network inside AWS and sending all computer logs to Splunk so we can monitor everything.

---

## 🖼 Lab Architecture

```
Attacker → Internet → AWS VPC → Endpoint → Splunk → SOC Analyst
```

---

## 📸 IMAGE 1 – Lab Architecture

```
![Lab Architecture](images/lab_architecture.png)
```

---

# 🛠 PART 1 – Create Windows Endpoint in AWS

---

## ✅ Step 1 – Login to AWS

1. Go to AWS Console
2. Click **EC2**
3. Click **Launch Instance**

---

## ✅ Step 2 – Choose Settings

Choose:

- OS: Windows 10 / Windows Server
- Instance type: t2.medium or higher
- Network: Your SOC VPC
- Subnet: Private Subnet
- Security Group:
  - Allow RDP (3389)

Click **Launch**

---

## 📸 IMAGE 2 – EC2 Running Instance

```
![EC2 Endpoint Running](images/ec2_endpoint_running.png)
```

---

## ✅ Step 3 – Connect to Windows

1. Select Instance
2. Click **Connect**
3. Use RDP file
4. Login as Administrator

Now you are inside your Windows endpoint 🎉

---

# 🏢 PART 2 – Join Domain (soc.local)

This makes the computer part of your company network.

---

## ✅ Step 1 – Open System Settings

1. Press `Win + R`
2. Type:

```
sysdm.cpl
```

3. Click **Change**
4. Select **Domain**
5. Enter:

```
soc.local
```

6. Enter Domain Admin credentials
7. Restart system

Now your endpoint is part of Active Directory.

---

# 📦 PART 3 – Install Splunk Universal Forwarder

This is the most important part.

Universal Forwarder = Log sender 📤

---

## ✅ Step 1 – Download Forwarder

On Windows Endpoint:

1. Open browser
2. Go to:
   https://www.splunk.com
3. Download:
   **Splunk Universal Forwarder for Windows**

---

## ✅ Step 2 – Install

Double-click installer:

✔ Accept license  
✔ Enter Splunk Server IP  
✔ Port: `9997`  
✔ Finish install  

---

## 📸 IMAGE 3 – Splunk Receiving Logs

```
![Splunk Receiving Logs](images/splunk_logs.png)
```

---

# ⚙ PART 4 – Configure Log Forwarding

---

## ✅ Step 1 – Open Services

Press:

```
services.msc
```

Make sure:

```
SplunkForwarder = Running
```

---

## ✅ Step 2 – Enable Windows Log Monitoring

Open Command Prompt as Admin:

```cmd
cd "C:\Program Files\SplunkUniversalForwarder\bin"
```

Enable Security Logs:

```cmd
splunk add monitor WinEventLog:Security
```

Enable System Logs:

```cmd
splunk add monitor WinEventLog:System
```

Enable Application Logs:

```cmd
splunk add monitor WinEventLog:Application
```

Restart forwarder:

```cmd
splunk restart
```

Now logs are being sent to Splunk 📡

---

# 🔍 PART 5 – Verify in Splunk

Go to Splunk Web.

Run this:

```spl
index="end_point"
```

You should see:

- EventCode=4688
- EventCode=4624
- EventCode=4625
- Security Logs
- System Logs

If logs appear → SUCCESS 🎉

---

# 🛡 What Can You Detect Now?

Now your SOC can detect:

| Attack | Event ID |
|--------|----------|
| Failed Login | 4625 |
| Successful Login | 4624 |
| Process Created | 4688 |
| Service Created | 7045 |
| User Added to Admin | 4732 |

Your lab is now a real SOC monitoring environment.

---

# 🌍 PART 6 – Add Linux Web Server (DVWA)

---

## ✅ Step 1 – Launch Linux EC2 (Public Subnet)

- OS: Ubuntu
- Open Port 80 (HTTP)

---

## ✅ Step 2 – Install Apache

SSH into server:

```bash
sudo apt update
sudo apt install apache2 -y
```

Open browser:

```
http://<public-ip>
```

Apache page should load.

---

## ✅ Step 3 – Install DVWA

```bash
sudo apt install php php-mysql mysql-server -y
```

Download DVWA:

```bash
git clone https://github.com/digininja/DVWA.git
```

Configure database and start Apache.

Now you have vulnerable web app 🎯

---

## ✅ Step 4 – Install Splunk Forwarder on Linux

Download:

```bash
wget splunkforwarder.tgz
```

Install:

```bash
tar -xvf splunkforwarder.tgz
```

Forward logs to Splunk server:

```bash
./splunk add forward-server <splunk-ip>:9997
```

Start Splunk:

```bash
./splunk start
```

Now Linux logs also go to Splunk.

---

# 🎯 What You Built

You now have:

✔ Windows Endpoint  
✔ Active Directory  
✔ Linux Web Server  
✔ DVWA  
✔ Splunk SIEM  
✔ Real-time Log Monitoring  

This is an **Enterprise SOC Simulation Lab**.

---

# 🧠 Why This Is Powerful

You are learning:

- Endpoint Monitoring
- Log Forwarding
- SIEM Configuration
- Detection Engineering
- Attack Simulation
- Blue Team Skills

This is real SOC work.

---

# 🏁 Final Check

If this works:

```spl
index=*
```

And you see logs from:

- Windows
- Linux
- Domain Controller

🔥 Your SOC Lab is Successfully Built.

---

# 📌 END OF DAY 5 – Endpoint Deployment & Log Forwarding
