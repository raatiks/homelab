# Lab Explanation

This document explains the purpose behind each component of the Splunk detection lab and clarifies what was actually accomplished from a cybersecurity perspective.

## Lab Overview

Set up a virtual lab with:

- **Kali Linux** (Attacker VM)
- **Windows 10** (Victim endpoint)
- **Windows Server 2019** (SIEM host running Splunk Enterprise)

These machines are isolated in a host-only network. Logs from the victim endpoint are forwarded to Splunk, where they are parsed, searched, and visualized in detection dashboards.

## What Was Built

This project sets up a controlled Windows lab environment to simulate cyberattacks and test detection capabilities using Splunk. It involves three virtual machines:

- **Kali Linux** for running attacks (Hydra, Nmap, Metasploit)
- **Windows 10** as the target endpoint
- **Windows Server 2019** hosting Splunk Enterprise as the SIEM

Sysmon, Windows Event Logs, and Firewall logs are collected from the Windows 10 machine and forwarded to Splunk. Detection dashboards are built to monitor brute-force attempts, process activity, and network behavior.

---

## 1. 🛡️ Brute-Force Detection

### What You Did
- Simulated login attacks using **Hydra**.
- Captured failed login attempts via **Windows Security Logs** (EventCode 4625).
- Built a dashboard to track:
  - Volume of failed logins over time
  - Targeted usernames
  - Attacking IPs

### Why It Matters
- **EventCode 4625** flags failed authentication attempts. When seen in quick succession from the same IP or targeting the same account, it indicates brute-force.
- Brute-force is one of the easiest and most common attack vectors. Detecting it early prevents unauthorized access.

---

## 2. 🔬 Sysmon Process Monitoring

### What You Did
- Installed **Sysmon** with a hardened config.
- Forwarded logs to Splunk (specifically Event ID 1: process creation).
- Created a dashboard to show:
  - All process activity
  - Top parent-child pairings
  - Frequent command-line arguments

### Why It Matters
- Sysmon gives deep visibility into endpoint activity.
- **Event ID 1** shows every executed process. Combined with command-line args and parent processes, this data helps detect:
  - Malicious scripts
  - LOLBins (e.g., `powershell.exe`, `mshta.exe`)
  - Post-exploitation tools

---

## 3. 🌐 Firewall Log Monitoring

### What You Did
- Enabled Windows Defender Firewall logging.
- Forwarded `pfirewall.log` to Splunk.
- Built dashboards to analyze:
  - Blocked destination ports
  - Top source IPs
  - Allow vs drop ratios

### Why It Matters
- Firewall logs reveal network-level anomalies like:
  - Port scanning (e.g., from Nmap)
  - Lateral movement attempts
  - Suspicious inbound/outbound connections

---

## 4. ⚙️ SIEM Integration and Data Ingestion

### What You Did
- Deployed **Splunk Enterprise** on Server 2019.
- Deployed **Splunk Forwarder** on Windows 10.
- Verified forwarding and indexing of:
  - Windows Event Logs
  - Sysmon Logs
  - Firewall Logs

### Why It Matters
- This simulates a production SIEM pipeline.
- Helps you learn data onboarding, source typing, and setting up detection content.

---

## 5. 🧪 Attack Simulation

### What You Did
- **Hydra**: Simulated password guessing.
- **Nmap**: Scanned the Windows 10 VM to fingerprint open ports.
- **Metasploit**: Launched a simulated payload for reverse shell.

### Why It Matters
- Shows you understand adversary tactics.
- Lets you see how different telemetry sources catch different stages of the attack.

---

## Takeaway
Shows how
- to build an isolated detection environment
- attackers behave
- to turn logs into usable insights through Splunk dashboards

These are fundamental capabilities for threat detection, blue teaming, and incident response.

---

## ✅ Skills Demonstrated

- VM provisioning and network isolation
- Splunk configuration and log ingestion
- Detection engineering using SPL
- Dashboard creation
- Adversary simulation and blue team response

---

## 📚 Knowledge Areas Covered

- MITRE ATT&CK Techniques:
  - T1110: Brute-force
  - T1059: Command and Scripting Interpreter
  - T1046: Network Service Scanning
  - T1071: Application Layer Protocol (Reverse Shell)
- Event IDs: 4625 (failed login), Sysmon 1 (process), firewall logs
- Log forwarding and SIEM pipeline design