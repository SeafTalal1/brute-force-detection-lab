# Brute Force Detection Lab (Mini SOC)

## Overview

This project demonstrates the implementation of a mini Security Operations Center (SOC) lab to simulate and detect brute force attacks. The lab provides hands-on experience in monitoring, log analysis, and threat detection using a SIEM solution.

---

## Objective

The objective of this project is to simulate brute force attack scenarios and analyze how these attacks can be detected using Wazuh SIEM. The project focuses on understanding log collection, event correlation, and alert generation.

---

## Lab Architecture

The lab environment consists of the following components:

* **Attacker:** Kali Linux machine used to simulate brute force attacks
* **Victim:** Windows machine acting as the target system
* **SIEM:** Wazuh deployed on an Ubuntu server for monitoring and analysis

---

## Tools and Technologies

* **Wazuh** – SIEM platform for log collection and threat detection
* **Kali Linux** – Used for attack simulation
* **Windows** – Target system
* **Sysmon** – Provides detailed logging on Windows

---

## Lab Setup

### 1. Wazuh Setup

#### System Preparation

The installation was performed on an Ubuntu Server. The system was updated before installation:

```bash
sudo apt update && sudo apt upgrade -y
```

---

#### Download Installer

The official Wazuh installation script was downloaded:

```bash
curl -sO https://packages.wazuh.com/4.7/wazuh-install.sh
```

---

#### Install Wazuh (All-in-One)

All components were installed on a single server using the all-in-one installation mode:

```bash
sudo bash wazuh-install.sh -a --ignore-check
```

> The `--ignore-check` flag was used because Ubuntu 24 is not officially supported in the installer yet.

---

#### Installed Components

The installation script automatically deployed:

* **Wazuh Manager** – Analyzes incoming logs
* **Wazuh Indexer** – Stores and indexes logs (based on OpenSearch)
* **Wazuh Dashboard** – Web interface for monitoring
* **Filebeat** – For log forwarding

---

#### Service Verification

All services were verified after installation:

```bash
sudo systemctl status wazuh-manager
sudo systemctl status wazuh-indexer
sudo systemctl status wazuh-dashboard
```

Each service should show an active (running) status.

---

#### Accessing the Dashboard

The Wazuh dashboard was accessed through the browser using the server address:

```
https://<SERVER-IP>
```

A browser warning may appear due to a self-signed certificate. This can be safely ignored in a lab environment.

---

#### Authentication

Default credentials:

* Username: admin
* Password: generated during installation

![wazuh login page](screenshots/wazuh_login.png)
![wazuh dashboard](screenshots/wazuh_dashboard.png)

---

#### Password Reset (if needed)

```bash
sudo /usr/share/wazuh-indexer/plugins/opensearch-security/tools/wazuh-passwords-tool.sh -u admin -p 'NewStrongPassword!'
```

---

#### Network Configuration

The network mode was set to **Bridged Adapter** to allow access from the host machine.

The server IP was verified using:

```bash
ip a
```

---

## Project Status

* [x] Wazuh SIEM Installed
* [x] Dashboard Accessible
* [ ] Add Windows Agent
* [ ] Simulate Brute Force Attack
* [ ] Analyze Logs and Alerts

---

### 2. Windows Agent Preparation

#### Installing Sysmon

Sysmon was installed on the Windows machine to enhance system logging and provide detailed visibility into system activity.

Steps performed:

1. Downloaded Sysmon from the official Microsoft Sysinternals website  [ https://learn.microsoft.com/en-us/sysinternals/downloads/sysmon ]
2. Extracted the archive  
3. Executed Sysmon with administrator privileges  

```bash
sysmon.exe -i
```

Verification

Sysmon logs were verified using Event Viewer:
* Applications and Services Logs > Microsoft > Windows > Sysmon
![wazuh dashboard](screenshots/eventvwr1.png)
![wazuh dashboard](screenshots/eventvwr2.png)

The logs confirmed that Sysmon was successfully installed and actively collecting system events.


--- 

## Learning Outcomes

* Understanding SIEM architecture (Manager, Indexer, Dashboard)
* Hands-on experience with log collection and monitoring
* Basic incident detection and troubleshooting
* Network configuration in virtual environments

---

## Next Steps

* Install Wazuh agent on Windows
* Generate brute force attack scenarios using Kali Linux
* Monitor and analyze alerts in Wazuh dashboard
