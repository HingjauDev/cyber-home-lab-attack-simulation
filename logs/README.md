# Logs Folder

This folder contains **sample logs** from the attack simulation.

**Purpose**: Allow others to analyze the same events, test Splunk queries, and what was detected. 

**Important**: 
- Logs are from an isolated VirtualBox lab only 
- Logs from splunk are marked on 04/24/2026 while others are on 04/25/2026, because the time system in splunk follows the American timezone. 

## Contents

- **sysmon/** -> Raw Sysmon events (csv) focused on the attack chain 
    - Process creation (Event ID 1)
    - Network connections to attacker (Event ID 3)
- **splunk/** -> Exported query results and detection output
- **linux/** -> Commands executed from the attacker machine

## How to Use these Logs
1. Import the CSV into Splunk(or any SIEM)
2. Run the queries shown in [attack-walkthrough.md](../docs/attack-walkthrough.md)
3. Compare "before" vs "during attack" events.

See the network diagram and attack walkthrough for context. 