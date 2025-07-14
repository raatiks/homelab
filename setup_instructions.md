# Setup Instructions

This is a walk-through on how to set up and connect everything so that you can do this project.

## The High-Level Overview
| Tool                           | Purpose                                                                 |
|:-------------------------------|:------------------------------------------------------------------------|
| Hypervisor (ex: VMware Fusion) | To run multiple VMs on a single machine                                 |
| ISO files                      | Disk image used to install or boot an operating system                   |
| SIEM (ex: Splunk Enterprise)   | Collects, searches, alerts on logs, and builds dashboards                |
| Log Forwarder (ex: Splunk Forwarder) | Sends logs from a source machine to the SIEM system                  |

Make sure you write down the usernames and passwords for all the accounts (Kali Linux, Windows 10, Windows Server 2019, Splunk Enterprise, and Splunk Forwarder). Also, record the IP addresses of the VMs.

## 1. Setting Up the Virtual Environment
- **Hypervisor**: Choose one and install it. Any of these will work: VirtualBox, VMware Workstation, VMware Fusion. Avoid running multiple hypervisors on the same host to prevent conflicts with virtual hardware.
- **ISO Files**: Download and keep these handy:
  - [`Kali Linux`](https://cdimage.kali.org/kali-2025.2/kali-linux-2025.2-installer-amd64.iso)
  - [`Windows 10`](https://www.microsoft.com/en-us/software-download/windows10ISO)
  - [`Windows Server 2019`](https://www.microsoft.com/en-us/evalcenter/evaluate-windows-server-2019) *(Requires free Microsoft account)*

## 2. Requirements for VMs
- **Kali Linux VM**  
  RAM: 2–4 GB  
  CPU: 2 cores  
  Disk: 20–30 GB  
  Network: Host-only Adapter (isolated lab)

- **Windows 10 VM**  
  RAM: 2–4 GB  
  CPU: 2 cores  
  Disk: 40–60 GB  
  Network: Start on NAT to get updates and tools, then switch to Host-only after forwarding is confirmed

- **Windows Server 2019 VM**  
  RAM: 4–6 GB  
  CPU: 2 cores  
  Disk: 50–70 GB  
  Network: The same process applies; start with NAT for setup, then switch to Host-only. If the system clock drifts after reboot, follow the steps in the "Clock issue" section.

## 3. Install and Configure Splunk (Enterprise and Forwarder)
1. Create a Splunk account at [splunk.com](https://www.splunk.com/).
2. **Splunk Forwarder**  
   - Install on Windows 10 using the `.msi` file from Splunk’s site.  
   - Use default install options.  
   - Choose to monitor all local event logs during setup.  
   - Configure it to forward to the IP of the server running Splunk Enterprise on port `9997`.
3. **Splunk Enterprise**  
   - Install on Windows Server 2019. Use the `.msi` installer.  
   - Stick to recommended install options.  
   - Write down the admin username and password you set.  
   - After install, log in via the web interface at `http://<server_ip>:8000`.  
   - Go to **Settings → Forwarding and receiving → Configure receiving → Add port 9997**.  
   - Add data inputs for Windows Event Logs and Sysmon logs (once Sysmon is installed).
  
## 4. Set Up the Domain Controller and Join Windows 10 to the Domain

### On the Server (Promote to Domain Controller):
1. Open Server Manager → Add Roles and Features  
2. Select **Active Directory Domain Services**  
3. After installation, click **Promote this server to a domain controller**  
4. Select **Add a new forest**, enter domain: `lab.local`  
5. Set a Directory Services Restore Mode (DSRM) password  
6. Let the server reboot.  
7. After reboot, confirm the domain is `lab.local`. This server also acts as DNS.

### On Windows 10 (Join the Domain):
1. Set DNS to point to the Server:
   - Press `Win + R`, type `ncpa.cpl`, press Enter  
   - Right-click active adapter → Properties  
   - Select **Internet Protocol Version 4 (TCP/IPv4)** → Properties  
   - Select **Use the following DNS server addresses**  
   - Set **Preferred DNS server** to the server’s IP (e.g. `192.168.56.101`)  
   - Leave Alternate DNS blank

2. Join the domain:
   - Right-click **This PC** → **Properties**  
   - Click **Change settings** (under Computer name)  
   - Click **Change** → Select **Domain** → Enter `lab.local`  
   - Enter domain admin credentials when prompted  
   - Reboot

3. Log in as: `lab\\administrator`

## 5. Install Sysmon and Configure Log Forwarding
1. Download Sysmon from Microsoft’s Sysinternals page.  
2. Install it with a config file (e.g. `sysmon.exe -accepteula -i <config.xml>`). You can use [SwiftOnSecurity’s Sysmon config](https://github.com/SwiftOnSecurity/sysmon-config).  
3. Confirm that logs show up in Windows Event Viewer under:  
   `Applications and Services Logs → Microsoft → Windows → Sysmon → Operational`
4. Forward those logs via Splunk Forwarder.  
   In Splunk Enterprise:  
   - Go to Settings → Data Inputs → Add new input  
   - Select **Event Log**  
   - Add: `Microsoft-Windows-Sysmon/Operational`
Also, add a monitor for firewall logs: "C:\Windows\System32\LogFiles\Firewall\pfirewall.log" with sourcetype WinFirewallLog.

## 6. Verify Logs Are Ingested in Splunk Enterprise
1. In Splunk Enterprise, go to **Search & Reporting**
2. Confirm logs are coming in from all sources:
   - Brute-force logins:  
     `index=* sourcetype="wineventlog:Security" EventCode=4625`
   - Sysmon logs:  
     `index=* sourcetype="WinEventLog:Microsoft-Windows-Sysmon/Operational"`
   - Firewall logs:  
     `index=* sourcetype="WinFirewallLog"`

3. If nothing is showing up, check:
   - Forwarder status
   - Port 9997 open on Enterprise
   - Windows firewall rules
   - DNS resolution between VMs

Once logs are confirmed, switch all VM adapters to **Host-only**. Confirm all IPs share the same subnet (first three octets match, e.g. `172.28.182.x`).  
Use `ipconfig` on Windows, `ip a` on Kali.

---

## Clock Issue (If Server Time Is Off)

If your Windows Server VM clock is incorrect (especially after a reboot), follow these steps:

1. Temporarily switch the server’s network adapter to **NAT** (you need internet access to reach `time.windows.com`).
2. Open **Command Prompt as Administrator** and run these commands one by one:

```cmd
net stop w32time
w32tm /unregister
w32tm /register
net start w32time
w32tm /config /manualpeerlist:"time.windows.com" /syncfromflags:manual /reliable:YES /update
w32tm /resync /force
```

3. Wait until the time syncs correctly. You can verify with:

```cmd
w32tm /query /status
```

4. Once it's in sync, switch the network adapter back to **Host-only**.

---

At this point, the lab is fully operational and ready for attack simulations and telemetry collection.
