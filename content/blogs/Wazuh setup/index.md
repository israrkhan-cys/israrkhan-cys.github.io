---
title: "Setting Wazuh in my SOC HomeLab"
date: 2026-06-24
draft: false
tags: ["wazuh", "security Ops", "networking"]
summary: "Setting up wazuh siem tool in my SOC home Lab"
coverImage: "cover.png"
---

## Introduction

In today's cybersecurity landscape, understanding Security Information and Event Management (SIEM) tools is essential for anyone pursuing a career in security operations. This blog post documents my journey of building a complete Security Operations Center (SOC) home lab using **Wazuh** . an open-source SIEM platform.

The goal was simple: Create a functional security monitoring environment that mimics enterprise setups, enabling hands-on learning with real security data and alerts.


## Hardware & Software Requirements

### My Setup

| Component | Specification |
|-----------|---------------|
| **Host Machine** | Arch Linux (Daily Driver) |
| **Virtualization** | VirtualBox |
| **SIEM Server VM** | Ubuntu Server 22.04 LTS |
| **Endpoint VM** | Arch Linux (Host Machine) |
| **RAM Allocated** | 4GB (Server) + Host Resources |
| **Storage** | 50GB (Server) + Host Storage |

### Software Stack
- **SIEM Platform:** Wazuh 4.14.5
- **Operating System (Server):** Ubuntu Server 22.04.4 LTS
- **Operating System (Endpoint):** Arch Linux and ubuntu (with Wazuh Agent)
- **Dashboard:** Wazuh Dashboard (Built-in)
- **Search Engine:** Wazuh Indexer (Elasticsearch-based)
- **Database:** Wazuh Manager (SQLite)

---

## Step 1: Setting Up Ubuntu Server

### Downloading the ISO
First, I downloaded the Ubuntu Server ISO from the official website:
```bash
# Navigate to ubuntu.com/download/server
# Select Ubuntu Server 22.04.4 LTS or 24.04 LTS
```

### Creating the Virtual Machine

1. **Open VirtualBox** and create a new VM:
   - **Name:** `Wazuh-Server`
   - **Type:** Linux
   - **Version:** Ubuntu (64-bit)
   - **Memory:** 4096 MB (4GB minimum)
   - **Storage:** 50 GB dynamically allocated

2. **Network Configuration:**
   - **Adapter 1:** NAT (for internet access)
   - **Adapter 2:** Host-Only (for local access)
   - *Optional:* Set a static IP using `netplan` for consistent access

### Installing Ubuntu Server

During installation, I configured:
- **Username:** `admin` (or your preferred username)
- **Hostname:** `wazuh-server`
- **SSH Server:** Enabled (for remote management)
- **Minimal installation:** Selected to reduce resource usage

### Post-Installation Setup

```bash
# Update the system
sudo apt update && sudo apt upgrade -y

# Install essential tools
sudo apt install curl wget net-tools -y

# Check IP address for dashboard access
ip a
```

---

## Step 2: Installing Wazuh SIEM

### The All-in-One Installation

Wazuh provides an installation assistant that handles everything automatically. This was the most exciting part of the process!

```bash
# Download the installation script
curl -sO https://packages.wazuh.com/4.14/wazuh-install.sh

# Make it executable
chmod +x wazuh-install.sh

# Run the all-in-one installation
sudo ./wazuh-install.sh -a
```

### What the Installer Does

The script automatically installs three main components:

| Component | Function |
|-----------|----------|
| **Wazuh Manager** | Processes security events and analyzes logs |
| **Wazuh Indexer** | Stores and indexes data (Elasticsearch-based) |
| **Wazuh Dashboard** | Provides the web interface for monitoring |

### Installation Complete

After about 5-10 minutes, I received the completion message:

```
INFO: Installation finished.
INFO: Wazuh dashboard: https://192.168.1.100
INFO: Username: admin
INFO: Password: // the auto password generated 
```

** Remember:** Always save the generated password or retrieve it later using:
```bash
sudo tar -O -xvf wazuh-install-files.tar wazuh-install-files/wazuh-passwords.txt
```

---

## Step 3: First Access & Configuration

### Accessing the Dashboard

1. Open a web browser on the host machine
2. Navigate to `https://[ubuntu-server-IP]`
3. Accept the security warning (self-signed SSL certificate is normal)
4. Login with credentials saved from installation

### Initial Dashboard View

The first login showed the Wazuh Overview, including:
- Security events in real-time
- Agent status
- System health

---

## Step 4: Deploying Agents on Endpoints

### Understanding Wazuh Architecture

Before deploying agents, it's crucial to understand how Wazuh works:

```
Agents → Wazuh Manager → Wazuh Indexer → Dashboard
  ↑            ↑               ↑            ↑
Collects     Processes      Stores      Visualizes
Logs         Alerts         Data        Events
```

### Adding an Agent Through the Dashboard

The easiest method is using the Wazuh dashboard:

1. Navigate to **Agents** → **Deploy New Agent**
2. Fill in:
   - **Agent Name:** `Home` (or any descriptive name)
   - **Server Address:** `YOUR_SERVER_IP`
3. Copy the generated command

### Deploying on Arch Linux (The Hard Way)

Arch Linux isn't officially supported by Wazuh's DEB/RPM packages, so I used the AUR:

```bash
# Install dependencies
sudo pacman -S --needed autoconf automake base-devel brotli cmake curl expect fakeroot gawk gcc gnupg inetutils libsigsegv libtool make nodejs perl python sudo

# Build from AUR
git clone https://github.com/mranv/wazuh-agent-archlinux
cd wazuh-agent-archlinux
export WAZUH_MANAGER="YOUR_SERVER_IP"
export WAZUH_AGENT_NAME="Home"
makepkg -si
```

**💡 Alternative:** Use the AUR helper `yay` for a simpler install:
```bash
WAZUH_MANAGER="YOUR_SERVER_IP" WAZUH_AGENT_NAME="Home" yay -S wazuh-agent
```

### Registering the Agent

After installation, register the agent with the server:

```bash
sudo /var/ossec/bin/manage_agents
```

**Registration Process:**
1. Type **A** to add an agent
2. Enter the agent name
3. Enter the server IP
4. Copy the generated key
5. Type **I** to import the key
6. Paste the key
7. Type **Q** to quit

### Starting the Agent

```bash
sudo systemctl daemon-reload
sudo systemctl enable wazuh-agent
sudo systemctl start wazuh-agent
```

### Verification

Check the agent status:
```bash
sudo journalctl -u wazuh-agent -f
```

Expected output:
```
INFO: (4101): Connected to the server at YOUR_SERVER_IP:1514
```

Back in the dashboard, the agent appeared as **Active** — a truly satisfying moment!

---

## Step 5: Creating Custom Alerts

### Why Custom Rules Matter

While Wazuh ships with thousands of rules, custom rules allow for:
- Organization-specific threat detection
- Insider threat monitoring
- Compliance requirements
- Early warning for specific software misuse

### Example 1: Monitoring Suspicious Commands

#### Step 5a: Install auditd on Arch

```bash
sudo pacman -S audit
```

#### Step 5b: Add Audit Rules

```bash
sudo nano /etc/audit/audit.rules
```

Add:
```bash
-a always,exit -F arch=b64 -S execve -k audit-wazuh-c
```

Restart services:
```bash
sudo systemctl restart auditd
sudo systemctl restart wazuh-agent
```

#### Step 5c: Create a CDB List

On the Wazuh server:
```bash
sudo nano /var/ossec/etc/lists/suspicious-programs
```

Add:
```bash
ncat:red
nc:red
tcpdump:orange
ping:yellow
```

#### Step 5d: Create the Custom Rule

```bash
sudo nano /var/ossec/etc/rules/local_rules.xml
```

Add:
```xml
<group name="audit">
  <rule id="100210" level="12">
      <if_sid>80792</if_sid>
      <list field="audit.command" lookup="match_key_value" check_value="red">etc/lists/suspicious-programs</list>
      <description>Audit: Highly Suspicious Command executed: $(audit.exe)</description>
      <group>audit_command,</group>
  </rule>
</group>
```

#### Step 5e: Restart Wazuh

```bash
sudo systemctl restart wazuh-manager
```

#### Step 5f: Test It!

On Arch, run:
```bash
nc -v
```

Within moments, the dashboard displayed an alert with **Level 12** severity!

### Example 2: Tracking File Integrity

#### What I Learned

A real alert I received:

```
Time: Jun 30, 2026 @ 17:33:27.529
Path: /etc/resolv.conf
Action: modified
Rule Description: Integrity checksum changed.
Rule Level: 7
Rule ID: 550
```

**Investigation Process:**
1. ❓ Why did it change?
2. 🔍 Checked if it was legitimate (DHCP renewal)
3. ✅ Verified DNS servers were normal
4. 📝 Documented as "DHCP update - normal activity"

This was my first real "SOC analyst" moment — investigating an alert and determining if it was a threat!

---

## Step 6: Monitoring & Investigating Alerts

### The SOC Analyst Workflow

1. **Monitor Dashboard:** Check for new alerts
2. **Triage Alerts:** Determine severity and validity
3. **Investigate:** Gather context and evidence
4. **Decision:** Classify as threat or false positive
5. **Document:** Record findings and actions
6. **Respond:** Take appropriate action


---

## Troubleshooting Guide

### Common Issues and Solutions

| Issue | Solution |
|-------|----------|
| **Agent won't connect** | Check firewall: `sudo ufw allow 1514/tcp` |
| | Verify server IP in agent config |
| | Restart agent: `sudo systemctl restart wazuh-agent` |
| **Dashboard inaccessible** | Check IP: `ip a` |
| | Check services: `sudo systemctl status wazuh-*` |
| | Accept SSL certificate warning |
| **Password forgotten** | `sudo tar -O -xvf wazuh-install-files.tar wazuh-install-files/wazuh-passwords.txt` |
| **IP changed after reboot** | Set static IP with netplan |
| | Or update agent config with new IP |
| **Arch dependencies fail** | Use AUR method instead of DEB/RPM |
| | Install `yay` helper: `git clone https://aur.archlinux.org/yay.git && cd yay && makepkg -si` |

### Pro Tip: Setting a Static IP

Avoid IP changes by configuring netplan on Ubuntu:

```yaml
network:
  version: 2
  ethernets:
    eth0:
      dhcp4: no
      addresses:
        - 192.168.1.100/24
      gateway4: 192.168.1.1
      nameservers:
        addresses: [8.8.8.8, 8.8.4.4]
```

Apply:
```bash
sudo netplan apply
```

---

## What I Learned

### Technical Skills
- ✅ Deploying and configuring SIEM in production-like environment
- ✅ Understanding Linux system administration (Ubuntu & Arch)
- ✅ Managing network configurations (DHCP, static IPs)
- ✅ Creating custom detection rules
- ✅ Investigating security alerts
- ✅ Troubleshooting distributed systems

### Security Concepts
- ✅ SIEM architecture (Manager, Indexer, Dashboard)
- ✅ Agent-based monitoring
- ✅ File Integrity Monitoring (FIM)
- ✅ System call auditing (auditd)
- ✅ Alert triage and investigation
- ✅ Threat detection methodologies

---

## Resources for Further Learning
- 📚 [Wazuh Official Documentation](https://documentation.wazuh.com/)
- 📚 [SANS SEC503: Network Monitoring](https://www.sans.org/cyber-security-courses/network-monitoring-threat-detection/)

---

*Published: July 2026*
*Last Updated: July 2026*
