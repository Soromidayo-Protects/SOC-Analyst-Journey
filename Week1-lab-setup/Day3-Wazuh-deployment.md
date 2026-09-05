# Day 3 — Wazuh SIEM Deployment & First Live Agent

## What happened
- Kali VM ran into a boot issue (stuck in initramfs) after a previous session — 
  resolved by restarting the VM cleanly; no data was lost
- Discovered a Wazuh OVA already available in VirtualBox (alternative SIEM to 
  Security Onion) and used it instead of waiting on further downloads
- Successfully downloaded the Security Onion ISO in parallel (verification 
  and install still pending)

## What I did
- Logged into the Wazuh server console (Linux) and found its IP via `ip a`: 192.168.1.100
- Accessed the Wazuh web dashboard at https://192.168.1.100 (self-signed cert warning, proceeded)
- Logged into the dashboard (admin/admin)
- Explored the default Overview page: Agents Summary, Last 24 Hours Alerts, 
  Endpoint Security features (Configuration Assessment, Malware Detection, 
  File Integrity Monitoring), Threat Intelligence (Threat Hunting, MITRE ATT&CK)

## Deploying my first agent
- Used "Deploy new agent" wizard: selected Linux > DEB amd64
- Set server address to 192.168.1.100, agent name "Kali-lab"
- Ran the generated install command on Kali (hit a paste-formatting error first — 
  fixed by re-copying the command cleanly)
- Installed successfully, then ran:
  - `sudo systemctl daemon-reload`
  - `sudo systemctl enable wazuh-agent`
  - `sudo systemctl start wazuh-agent`
- Verified with `sudo systemctl status wazuh-agent` confirmed "active (running)"

## Result
- Wazuh dashboard now shows **Active (1)** agent
- Alert counts jumped from near-zero to 118 medium + 103 low severity alerts 
  within minutes of connecting, Wazuh immediately began monitoring Kali's 
  configuration, files, and system activity.

## Key takeaway
This is my first live Detect-function capability: a real endpoint reporting 
into a real SIEM. The jump in alert volume the moment the agent connected is 
a clear before/after demonstration of what monitoring coverage actually does.

## Next steps
- Review individual alerts in the Wazuh dashboard
- Install/verify Security Onion (ISO downloaded, pending hash check + VM setup)
- Begin simulating an attack from Kali against a monitored target.
