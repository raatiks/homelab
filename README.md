# 🛡️ Windows Attack Detection Lab

This project simulated both a brute-force and a network-based attack in a Windows environment, capturing logs using Sysmon, WinEventLog, and Firewall logs, and visualizing them with Splunk.

## 🔧 Lab Components
- **Kali Linux VM**: Used to simulate attacks (Hydra, Nmap, and Metasploit)
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
- **Panel 3**: Top IPs for failed logins

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
Detailed configuration for Splunk, Sysmon, and log forwarding is in [`setup_instructions.md`](setup_instructions.md).

## 📸 Screenshots
Dashboards, VM configs, Splunk searches, and attack terminals are available in their respective folders: [`dashboards/`](dashboards/), [`VM_settings/`](VM_settings/), [`search/`](search/), [`kali_terminal/`](kali_terminal/).

## 🔍 What This Lab Demonstrated
This lab helped me understand how to:
- Simulate realistic attacks
- Capture multi-source telemetry
- Build effective detection content in Splunk

## 📚 References
- [`MITRE ATT&CK`](https://attack.mitre.org)
- [`Windows Event ID 4625`](https://learn.microsoft.com/en-us/windows/security/threat-protection/auditing/event-4625)
- Sysmon Event ID [`1`](https://learn.microsoft.com/en-us/sysinternals/downloads/sysmon#event-id-1-process-creation), [`3`](https://learn.microsoft.com/en-us/sysinternals/downloads/sysmon#event-id-3-network-connection), [`11`](https://learn.microsoft.com/en-us/sysinternals/downloads/sysmon#event-id-11-filecreate)
- Microsoft Defender + Firewall logging: [`Defender Overview (Microsoft Defender for Endpoint)`](https://learn.microsoft.com/en-us/microsoft-365/security/defender-endpoint/microsoft-defender-endpoint?view=o365-worldwide), [`Windows Firewall Logging Setup`](https://learn.microsoft.com/en-us/windows/security/operating-system-security/network-security/windows-firewall/configure-logging?tabs=intune), [`Firewall Log File Format (pfirewall.log)`](https://www.exabeam.com/explainers/event-logging/firewall-logs/)
