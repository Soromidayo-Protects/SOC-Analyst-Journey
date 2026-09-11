# MITRE ATT&CK T1110 : Brute Force

**Tactic:** Credential Access
**Source:** [attack.mitre.org/techniques/T1110](https://attack.mitre.org/techniques/T1110/)
**Documented by:** Obisesan | SOC Analyst Training Journal- Week 1

---

## 1. Technique Overview

| Field | Detail |
|---|---|
| ID | T1110 |
| Tactic | Credential Access |
| Sub-techniques | T1110.001, T1110.002, T1110.003, T1110.004 |
| Platforms | Containers, ESXi, IaaS, Identity Provider, Linux, Network Devices, Office Suite, SaaS, Windows, macOS |

**Definition:**
Adversaries use brute force techniques to gain access to accounts when passwords are unknown or when password hashes have already been obtained. Without knowledge of the correct password, an attacker systematically guesses it using a repetitive or iterative mechanism.

Brute forcing can happen in two contexts:
- **Online**: interacting directly with a live authentication service (e.g., SSH, RDP, a web login form) that validates credentials in real time. Noisier, rate-limited, easier to detect.
- **Offline**: cracking previously stolen password hashes without touching the live service. Quieter, no live alerts generated, but requires the attacker to have already exfiltrated hash data.

**Where it sits in the attack chain:**
Brute forcing is rarely the very first action. It commonly follows other post-compromise steps, such as:
- OS Credential Dumping
- Account Discovery
- Password Policy Discovery (learning lockout thresholds/complexity rules before attempting the attack)

It's also frequently combined with **External Remote Services** as an Initial Access vector, e.g., brute forcing an internet-facing RDP or VPN endpoint.

---

## 2. Sub-techniques

| ID | Name | Notes |
|---|---|---|
| T1110.001 | Password Guessing | Manually or automatically trying common/likely passwords against one account |
| T1110.002 | Password Cracking | Offline cracking of stolen password hashes |
| T1110.003 | Password Spraying | One (or few) passwords tried against *many* accounts avoids per-account lockout |
| T1110.004 | Credential Stuffing | Using credentials leaked from other breaches, assuming password reuse |

**Analyst note:** When triaging an alert, the first question should be "which sub-technique am I looking at?" the log pattern differs (one account/many passwords = guessing/cracking; many accounts/one password = spraying; known-good credential pairs = stuffing).

---

## 3. Detection Strategy- DET0463

**Name:** Brute Force Authentication Failures with Multi-Platform Log Correlation

| Analytic ID | Description |
|---|---|
| AN1275 | High volume of failed logon attempts followed by a successful one, from a suspicious user, host, or timeframe |
| AN1276 | Multiple authentication failures (valid or invalid users) followed by success from the same IP/user |
| AN1277 | Password spraying/brute force attempts across a user pool within short time intervals |
| AN1278 | Multiple failed authentications in unified logs (e.g., macOS `loginwindow`, Linux `sshd`) |
| AN1279 | Excessive login attempts followed by success from SaaS apps (O365, Dropbox, etc.) |

**Core detection pattern (my takeaway):**
Every analytic above follows the same logical shape:

```
failure spike  →  correlate by identity / source IP / timeframe  →  followed by a success
```

A single failed login is noise. A *cluster* of failures - especially crossing a defined threshold followed by a success event is the actual signal. If I were writing a detection rule (Sigma/Splunk/KQL), the core logic would be:

- Count failed auth events grouped by `user` or `source_ip`
- Set a threshold (e.g., >5 failures in 5 minutes)
- Alert if that threshold is crossed **and** a success event follows within the same window
- For spraying specifically: group by `source_ip` across *many distinct users* rather than one user

---

## 4. Procedure Examples (Real-World Usage)

| ID | Actor / Tool | Behavior |
|---|---|---|
| C0025 | Sandworm Team (2016 Ukraine Electric Power Attack) | Used a script to attempt RPC authentication against a number of hosts |
| G1030 | Agrius | Engaged in brute forcing activities via SMB |
| G0007 | APT28 | Performs brute force attacks to obtain credentials |
| G0082 | APT38 | Uses brute force when passwords/hashes are unknown or unavailable |
| G0087 | APT39 | Used Ncrack to reveal credentials |
| G0096 | APT41 | Performed password brute-force attacks on the local admin account |
| S0572 | Caterpillar WebShell | Has a built-in module to perform brute force attacks |
| S0220 | Chaos | Conducts brute force attacks against SSH services for initial access |
| S0488 | CrackMapExec | Can brute force supplied user credentials across a network range |

**Why the Sandworm/Ukraine example matters:** It's a nation-state actor using brute force as part of a larger campaign against critical infrastructure (2016 Ukraine power grid attack) a good example to cite when explaining that brute forcing isn't just a "script kiddie" technique, it's used at every tier of threat actor sophistication.

---

## 5. Study Takeaways / Analyst Checklist

When I see a brute-force related alert, I should ask:
- [ ] Is this against one account (guessing/cracking) or many accounts (spraying)?
- [ ] Is there a successful login following the failure cluster? (This is the highest-priority indicator.)
- [ ] What's the source single IP, multiple IPs (distributed), or a known malicious range?
- [ ] Which platform/log source is this appearing in (Windows Security log, sshd, SaaS audit log, etc.)?
- [ ] Does the account that succeeded have privileged access? (escalates severity)
- [ ] Is there a related technique nearby Password Policy Discovery beforehand, or Valid Accounts / External Remote Services usage afterward?

---

## 6. References

- MITRE ATT&CK — T1110 Brute Force: https://attack.mitre.org/techniques/T1110/
