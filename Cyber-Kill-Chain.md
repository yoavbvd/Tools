# Cyber Kill Chain — mapped to this project

The **Lockheed Martin Cyber Kill Chain®** describes the seven stages of an intrusion. This page maps each stage to the folders in this repo and to **MITRE ATT&CK®** tactics, so you can see where each tool and technique fits in the bigger picture.

> The Kill Chain is a linear model, good for teaching and for framing a report. For technique-level detail, map findings to **MITRE ATT&CK** (noted per row). Strategic assessments use **NIST CSF**.

## The seven stages

| # | Kill Chain stage | What happens | In this project | ATT&CK tactic |
| --- | --- | --- | --- | --- |
| 1 | **Reconnaissance** | Gather information on the target | [01 Recon](01-Recon/), [OSINT](01-Recon/OSINT-Toolkit.md) | Reconnaissance (TA0043) |
| 2 | **Weaponization** | Build the payload/exploit | [04 Exploitation](04-Exploitation/) (msfvenom, searchsploit) | Resource Development (TA0042) |
| 3 | **Delivery** | Get the payload to the target | [03 Web](03-Web/), phishing, service exploits | Initial Access (TA0001) |
| 4 | **Exploitation** | Execute code / get a foothold | [04 Exploitation](04-Exploitation/), [Reverse Shells](04-Exploitation/Reverse-Shells.md) | Execution (TA0002) |
| 5 | **Installation** | Establish persistence | [11 Persistence](11-Persistence/) | Persistence (TA0003) |
| 6 | **Command & Control** | Maintain a channel to the host | [07 Pivoting](07-Post-Exploitation-Pivoting/) (tunneling, C2) | Command and Control (TA0011) |
| 7 | **Actions on Objectives** | Achieve the goal (data, domain) | [05 PrivEsc](05-Privilege-Escalation/), [06 Active Directory](06-Active-Directory/), [08 Password Attacks](08-Password-Attacks/) | Priv-Esc / Lateral / Exfil / Impact |

## How the pieces line up
- **Enumeration** ([02](02-Enumeration/)) sits between Recon and Delivery — it turns broad recon into specific, attackable services.
- **Privilege escalation** and **lateral movement** ([05](05-Privilege-Escalation/), [06](06-Active-Directory/)) repeat in a loop during *Actions on Objectives* until the goal is met.
- **Reporting** ([09](09-Reporting/)) documents the whole chain; the [Lab Notebook](https://yoavbvd.github.io/Tools/#/notebook) tracks it per phase.

## Why defenders care
Each stage is a chance to detect and break the chain. The earlier you break it, the cheaper the incident. For each finding in a report, note which stage it enables and which control would break the chain there (a Finding → Severity → Impact → Recommendation line).

## See also
- [Pentesting Field Guide](Pentesting-Field-Guide.md) — the hands-on methodology.
- [MITRE ATT&CK](https://attack.mitre.org/) · [Lockheed Martin Cyber Kill Chain](https://www.lockheedmartin.com/en-us/capabilities/cyber/cyber-kill-chain.html)
