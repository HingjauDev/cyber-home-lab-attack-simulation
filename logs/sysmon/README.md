# Sysmon Logs

Sample logs from the attack simulation.

## Folder Contents

- **before-attack-sample/** → Normal activity before the attack
- **attack-events/** → Key Sysmon events triggered during the Linux → Windows attack
  - Event ID 1: Process Creation (payload execution + enumeration commands)
  - Event ID 3: Network Connection (C2 traffic to attacker)
  - Event ID 11: File Creation (payload drop)

See [attack-walkthrough.md](../../docs/attack-walkthrough.md) for context and Splunk queries.