# My Splunk Project: Configuring Splunk Universal Forwarder on Linux to Forward Logs to Splunk Enterprise on Windows

## Introduction
In this project, I set up **Splunk Enterprise** on my Windows machine and configured a **Splunk Universal Forwarder** on a Linux virtual machine (VirtualBox) to forward logs to Splunk. I also created a custom index called `linux` to store the forwarded logs. This document walks through the entire process step-by-step.

---
## Project Overview

- **Splunk Enterprise**: Installed on a Windows machine to act as the central log management system.
- **Splunk Universal Forwarder**: Installed on a Linux VM to collect and forward logs to Splunk Enterprise.
- **Custom Index**: Created a custom index named `linux` to store logs from the Linux VM.

---


## Step 1: Install Splunk Enterprise on Windows
### 1.1 Download Splunk Enterprise
- Signed up for a free account on the [Splunk website](https://www.splunk.com/).
- Downloaded the **Splunk Enterprise** installer for Windows.

### 1.2 Install Splunk Enterprise
- Located the downloaded `.msi` file and double-clicked to start installation.
- Followed the installation wizard:
  - Accepted the license agreement.
  - Added username and password.
  - Chose the default installation directory.
  - Selected **Start Splunk Enterprise after installation**.
  - Clicked **Finish**.
- Splunk started automatically.

### 1.3 Access Splunk Enterprise
- Opened a web browser and navigated to `http://localhost:8000`.
- Logged in with my credentials.

### 1.4 Create a Custom Index
- In Splunk Enterprise, navigated to **Settings > Indexes**.
- Clicked **New Index** and created an index named `linux`.

### 1.5 Configure Receiving Port
- Navigated to **Settings > Forwarding and Receiving**.
- Clicked **Configure Receiving** and added a new receiving port (`9997`).

---

## Step 2: Install Splunk Universal Forwarder on Linux (VirtualBox)
### 2.1 Download the Universal Forwarder
```bash
wget -O splunkforwarder.deb 'https://download.splunk.com/products/universalforwarder/releases/9.x.x/linux/splunkforwarder-9.x.x-xxxxxxx-linux-2.6-amd64.deb'
```

### 2.2 Install the Universal Forwarder
```bash
sudo dpkg -i splunkforwarder.deb
sudo apt-get install -f  # Resolve dependencies
```

### 2.3 Start the Universal Forwarder
```bash
cd /opt/splunkforwarder/bin
sudo ./splunk start
```

---

## Step 3: Configure the Universal Forwarder to Forward Logs
### 3.1 Set the Forward Server
- Allowed inbound traffic on Windows:
```cmd
C:\Windows\System32> netsh advfirewall firewall add rule name="Splunk Forwarder" dir=in action=allow protocol=TCP localport=9997
```
- Configured the Universal Forwarder to send logs to Windows:
```bash
sudo ./splunk add forward-server <windows_ip>:9997
```

### 3.2 Add Logs to Monitor
- Forward `/var/log/syslog` to Splunk:
```bash
sudo ./splunk add monitor /var/log/syslog -index linux
```

### 3.3 Restart the Universal Forwarder
```bash
sudo ./splunk restart
```

---

## Step 4: Verify Logs in Splunk Enterprise
### 4.1 Confirm Logs are Received
- Open Splunk Enterprise (`http://localhost:8000`).
- Go to **Search & Reporting**.
- Run the following search:
```bash
index="linux"
```
- If logs are missing, check for errors:
```bash
cat /opt/splunkforwarder/var/log/splunk/splunkd.log
```

---

## Troubleshooting Tips
- **Firewall Issues:** Ensure port `9997` is open on both Linux and Windows.
- **Permissions:** Ensure Universal Forwarder has read access to logs.
- **Network Connectivity:** Test connectivity with `ping` or `telnet`.

---

## Conclusion
This project enhanced my understanding of log management and analysis using Splunk. I:
- Installed and configured **Splunk Enterprise** on Windows.
- Set up **Splunk Universal Forwarder** on Linux.
- Configured a **custom index** for storing logs.
- Learned how to **troubleshoot** common Splunk setup issues.

### Next Steps
- Create **dashboards and alerts** for proactive monitoring.
- Learn **Splunk Search Processing Language (SPL)** for advanced queries.
- Integrate **Windows Event Logs and network devices** into Splunk.

This hands-on experience strengthened my technical skills and reinforced the importance of centralized log management in cybersecurity and IT operations. I look forward to further exploring Splunk's capabilities!
