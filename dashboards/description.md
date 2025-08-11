This file was made to create the dashboards folder

Below are the SPL commands used to make the dashboards:

1. Brute Force Detection
- Panel 1: Timechart of failed logins ->

index=* sourcetype="WinEventLog:Security" EventCode=4625 | timechart span=1m count

- Panel 2: Top 5 targeted usernames ->
index=* sourcetype="WinEventLog:Security" EventCode=4625 | top Workstation_Name limit=5

- Panel 3: Top IPs for failed logins ->
index=* sourcetype="WinEventLog:Security" EventCode=4625 | top Source_Network_Address limit=5

2. Sysmon Process Monitoring
- Panel 1: Timechart for all processes ->
index=* sourcetype="WinEventLog:Microsoft-Windows-Sysmon/Operational" EventCode=1 | timechart span=1m count

- Panel 2: Top 10 processes by name ->
index=* sourcetype="WinEventLog:Microsoft-Windows-Sysmon/Operational" EventCode=1 | top Image limit=10

- Panel 3: Parent-child process pairings ->
index=* sourcetype="WinEventLog:Microsoft-Windows-Sysmon/Operational" EventCode=1 | stats count by ParentImage, Image | sort - count

- Panel 4: Most common command-line arguments ->
index=* sourcetype="WinEventLog:Microsoft-Windows-Sysmon/Operational" EventCode=1 | top CommandLine limit=10

3. Firewall Log Monitoring
- Panel 1: Timechart of firewall events ->
index=* sourcetype="WinFirewallLog" | rex field=_raw "^(?<action>\S+)" | timechart span=1m count by action

- Panel 2: Top blocked destination ports (\S+ tokens are based on log format)->
index="*" sourcetype="WinFirewallLog"
| rex field=_raw "^\S+\s+\S+\s+\S+\s+\S+\s+\S+\s+\S+\s+\S+\s+(?<dest_port>\d+)" | top dest_port

- Panel 3: Top source IPs ->
index="*" sourcetype="WinFirewallLog"
| rex field=_raw "^\S+\s+\S+\s+(?<action>\S+)\s+\S+\s+(?<src_ip>\d{1,3}(?:\.\d{1,3}){3})" | top src_ip

- Panel 4: Allow vs drop comparison ->
index="*" sourcetype="WinFirewallLog" | rex field=_raw "^\S+\s+\S+\s+(?<action>\S+)" | stats count by action
