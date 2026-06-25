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

#### 1. Download Sysmon
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

### 2. Wazuh Agent Installation

The Wazuh agent was installed on the Windows machine to enable log collection and communication with the Wazuh manager.

Steps performed:

1. Downloaded a compatible Wazuh agent version (4.7.5) to match the manager version
2. Installed the agent using the MSI installer
3. Configured the agent to connect to the Wazuh manager using the server IP

```bash
msiexec.exe /i wazuh-agent-4.7.5-1.msi WAZUH_MANAGER="<SERVER-IP>"
```

---

3. Agent Registration and Authentication

The agent was registered with the Wazuh manager using the automatic authentication method.

Steps performed:

3.1. Executed the following command on the Windows machine:

```bash
"C:\Program Files (x86)\ossec-agent\agent-auth.exe" -m <SERVER-IP>
```

3.2. The agent requested and received a valid authentication key from the manager

Verification:

* Successful output:

  ```
  INFO: Valid key received
  ```

This confirmed that the agent was successfully authenticated and registered.

---

4. Starting the Wazuh Agent Service

After successful registration, the agent service was restarted.

```bash
net stop WazuhSvc
```

```bash
net start WazuhSvc
```

Verification:

* The service started successfully without errors
* The agent began communicating with the Wazuh manager

---

5. Agent Connectivity Verification

The agent status was verified from the Wazuh dashboard.

Steps performed:

5.1. Navigated to:

   ```
   Wazuh Dashboard → Agents
   ```

5.2. Confirmed:

   * Agent status: **Active**
   * No disconnected or pending agents
   * Successful communication between agent and manager

![wazuh dashboard](screenshots/active_wazuh_dashboard.png)

---

### 3. Sysmon Integration Verification

After configuring the Wazuh agent to collect Sysmon logs, the integration was verified from the Wazuh dashboard.

Steps performed:

1. Opened **Security Events → Events**
2. Filtered events using:

```text
rule.groups:sysmon
```

3. Confirmed that Sysmon events were successfully ingested by Wazuh.

The dashboard displayed multiple Sysmon-based detections, including:

- Process creation events
- Discovery activity
- `net.exe` account discovery commands
- Security-related behavioral detections

This verification confirmed that:

- Sysmon was successfully generating Windows events.
- The Wazuh agent was collecting Sysmon logs.
- The Wazuh manager was processing the events.
- Detection rules were successfully generating security alerts.

![Sysmon Events](screenshots/sysmon_events.png)

6. Issues Encountered and Resolutions

During the setup process, several issues were encountered and resolved:

* **Version Mismatch**

  * Issue: Agent version higher than manager
  * Resolution: Installed compatible agent version (4.7.5)

* **Time Synchronization Issue**

  * Issue: Agent not becoming active
  * Resolution: Set both systems to UTC and synchronized system time

* **Duplicate Agent Name**

  * Issue: Agent registration failed due to duplicate name
  * Resolution: Removed existing agent entries from manager

* **Manual Key Handling Errors**

  * Issue: Errors when manually copying authentication key
  * Resolution: Used `agent-auth` for automatic key exchange

---

### Outcome

The Windows endpoint was successfully prepared with enhanced logging (Sysmon) and fully integrated with the Wazuh SIEM.

The system is now ready for attack simulation and detection testing.

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

---

## Troubleshooting

During the lab setup, several issues were encountered and resolved.

### 1. Wazuh Agent Version Mismatch

**Issue**

The Windows agent failed to register with the manager.

```
ERROR: Agent version must be lower or equal to manager version
```

**Cause**

The installed agent version (4.14.x) was newer than the Wazuh manager version (4.7.5).

**Resolution**

Installed the Wazuh Agent version **4.7.5**, matching the manager version.

---

### 2. Agent Registration Failure

**Issue**

The agent registration failed due to duplicate entries.

```
ERROR: Duplicate agent name
```

**Cause**

The agent had already been registered previously.

**Resolution**

Removed the existing agent from the Wazuh manager and registered it again using:

```bash
agent-auth.exe -m <WAZUH_SERVER_IP>
```

---

### 3. Agent Appeared as Disconnected

**Issue**

The agent appeared as **Disconnected** in the Wazuh dashboard even though the Windows service was running.

**Cause**

The Wazuh server received a new IP address after reboot because it was using DHCP. The Windows agent was still attempting to connect to the previous server IP.

**Resolution**

Checked the agent log file:

```
C:\Program Files (x86)\ossec-agent\logs\ossec.log
```

The logs showed that the agent was attempting to connect to the old server IP.

Configured a **DHCP Reservation** on the router to permanently assign the Wazuh server the same IP address (`192.168.1.9`).

After restarting the Wazuh agent service, the agent successfully reconnected and its status changed to **Active**.

---

### 4. Time Synchronization

**Issue**

The Windows machine and the Wazuh server had different system times.

**Cause**

Different time synchronization settings on the virtual machines.

**Resolution**

Configured both systems to synchronize their clocks and verified that both machines were using the correct UTC time.

---

### Lessons Learned

- Always check the agent logs before reinstalling or reconfiguring Wazuh.
- Keep the Wazuh Manager IP address static to prevent connectivity issues.
- Ensure the agent version is compatible with the Wazuh Manager version.
- DHCP Reservation is an effective way to maintain a consistent server IP in a lab environment.
