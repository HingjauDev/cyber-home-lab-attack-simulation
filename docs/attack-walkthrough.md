# Attack Simulation Walkthrough

## 1. Objective
The goal of this project was to simulate a real-world cyber attack within a controlled environment from initial compromise to defensive detection. By acting as both the red Team (Attacker) and the Blue Team (Defender), the simulation aims to validate how specific attack signatures (like reverse shells) manifest in Windows logs and how to reveal those threats using Splunk. 

## 2. Environment Architecture
The lab consisits of an isolated virtual network designed to mirror a small enterprise segment.

| Component | Operating System | Role | Primary Tools | 
| :---------: | :----------------: | :----: | :-------------: |
| Attacker Node | Kali Linux | Adversary | Metasploit, Nmap | 
| Target Node | Windows 10 | Victim Workstation | Sysmon, PowerShell |
| SIEM / Logging | Windows 10 (Host) | Monitoring & Analysis | Splunk Enterprise |

## 3. Technical Workflow
The simulation follows a three-stage lifecycle:<br/>
    1. Deployment: Provisioning VMs via VirtualBox and establishing a private `Internal Network` to ensure safety and isolation.<br/>
    2. Instrumentation: Configuring Sysmon     