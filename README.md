# 🛡️ Windows Attack Detection Lab

This project simulates a brute-force and a network-based attack in a Windows environment, capturing logs with Sysmon, WinEventLog, and Firewall logs, and visualizing them using Splunk.

## 🔧 Lab Components
- **Kali Linux VM**: Used to simulate attacks (Hydra, Nmap, Metasploit)
- **Windows 10 VM**: Target endpoint with Sysmon and Splunk Forwarder installed
- **Windows Server 2019 VM**: Hosts Splunk Enterprise

## 🧪 Attack Scenario
- Brute-force login attempt via Hydra
- TCP port scanning via Nmap
- Reverse shell execution via Metasploit

## 📊 Dashboards

### 1. Brute Force Detection
- **Sourcetype**: `WinEventLog:Security`
- **Panel 1**: Timechart of failed logins
- **Panel 2**: Top 5 targeted usernames
- **Panel 3**: Top IP's for failed logins

### 2. Sysmon Process Monitoring
- **Sourcetype**: `WinEventLog:Microsoft-Windows-Sysmon/Operational`
- **Panel 1**: Timechart for all processes
- **Panel 2**: Top 10 processes by name
- **Panel 3**: Parent-child process pairings
- **Panel 4**: Most common command-line arguments

### 3. Firewall Log Monitoring
- **Sourcetype**: `WinFirewallLog`
- **Panel 1**: Timechart of firewall events
- **Panel 2**: Top blocked destination ports
- **Panel 3**: Top source IPs
- **Panel 4**: Allow vs drop comparison

## 🛠️ Setup Instructions
See [`setup_instructions.md`](setup_instructions.md) for full Splunk, Sysmon, and log forwarding configuration steps.

## 📸 Screenshots
All dashboards are available in the [`dashboards/`](dashboards/) folder.

## 🔍 Why This Matters
This lab proves your ability to:
- Simulate realistic attacks
- Capture multi-source telemetry
- Build effective detection content in Splunk

## 📚 References
- MITRE ATT&CK
- Windows Event ID 4625
- Sysmon Event ID 1, 3, 11
- Microsoft Defender + Firewall logging
