# Attack Walkthrough

This is document details the simulated attack from the **Kali Linux Attacker VM** to the **Windows 10 Victim VM**, including payload delivery, successful compromise, command execution, and detection in **Splunk**.

**Lab Context**
- Attacker: Kali Linux VM - IP: `192.168.20.11`
- Victim: Windows 10 VM with **Sysmon** + **Splunk Enterprise** - IP: `192.168.20.12`
- Network: Isolated VirtualBox [Internal] Network
- Goal: Simulate a realistic attack chain and validate detection capabilities

**⚠️ Important Notes**
- All activities were performed in an **isolated lab environment** with no internet access to the victim during testing. 
- No real malware was used - only safe, educational payloads for demonstration. 
- Take a VM snapshot before starting the attack

## 1. Preparation on Attacker (Kali Linux)

1. Scan the OS information
```bash
nmap -A -Pn -n 192.168.20.12
```

2. Create the payload
    ```bash
    


    ```