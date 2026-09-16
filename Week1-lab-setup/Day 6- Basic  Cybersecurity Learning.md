Day 6: Cybersecurity Learning & Hands-On Practice

What I learnt

* SIEM and its role in security monitoring
* Difference between Wazuh, Security Onion, and Splunk
* SSH (Secure Shell) and its common use for remote access
* RDP (Remote Desktop Protocol) and its use for graphical remote access
* Password spraying and how it differs from brute-force attacks
* APIPA and the 169.254.0.0/16 address range
* HTTP on port 80 and HTTPS on port 443
* Basic network and infrastructure concepts

What I practiced

-Performed a basic TCP port scan against my Kali Linux lab machine to identify accessible services.
* Basic network reconnaissance
* TCP port scanning with Nmap
* Identifying open and closed ports
* Interpreting Nmap scan results
* Relating an open port to the service running on it

Scan Result

* Open: 22/tcp SSH
* Closed: 1,999 ports

Key Finding

TCP port 22 was the only open port identified during the scan, indicating that an SSH service was accessible on the target.

Key Takeaway

Hands-on monitoring helped connect the concepts I have been learning to actual security telemetry. I practiced identifying services, monitoring endpoint activity, and interpreting security events using Wazuh and Nmap.

Next Steps

* Review and analyze individual Wazuh alerts.
* Continue the NIST Cybersecurity Framework project.
* Set up Security Onion and explore network monitoring capabilities.
* Continue building practical detection and investigation skills.
