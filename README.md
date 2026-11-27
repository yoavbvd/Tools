<div align="center">

# 🛡️ Pentesting Toolkit & Cheat-Sheets

**A phase-by-phase offensive-security knowledge base** — tools, commands, and cheat sheets organized so you can start a lab fast.

[![Browse the UI](https://img.shields.io/badge/🖥️_Browse-Live_UI-39d98a?style=for-the-badge)](https://yoavbvd.github.io/Tools/)
&nbsp;
[![Authorized use only](https://img.shields.io/badge/Use-Authorized_testing_only-ff6b6b?style=flat-square)](#-legal--ethical-use)
[![Format](https://img.shields.io/badge/Format-Markdown-2bc4ff?style=flat-square)](#)

</div>

---

## 🖥️ Browse it as a web app

This repo ships a **dark-themed single-page UI** ([`index.html`](index.html)) with a per-folder view of every page, rendered in the browser with copy-to-clipboard on each command block.

It also includes a **🔍 full-text search** and a **📓 Lab Notebook** — an interactive per-phase checklist with notes that saves in your browser and exports your run as a Markdown or HTML report (nothing is uploaded).

> **▶ Live site: [yoavbvd.github.io/Tools](https://yoavbvd.github.io/Tools/)**

Run it locally with `python3 -m http.server`, then open `index.html`. (Opening the file with `file://` won't work — the browser blocks local `fetch` of the Markdown pages.)

---

## 🚀 Start here

- **[START-A-PENTEST-LAB.md](START-A-PENTEST-LAB.md)** — step-by-step checklist to bring up a lab and begin.
- **[Pentesting-Field-Guide.md](Pentesting-Field-Guide.md)** — the cross-phase operational methodology (network, web, Active Directory).
- **[Cyber-Kill-Chain.md](Cyber-Kill-Chain.md)** — the 7 kill-chain stages mapped to every folder and to MITRE ATT&CK.

---

## 🗂️ Sections by phase

| # | Section | Covers |
| --- | --- | --- |
| 00 | **[Lab Setup](00-Lab-Setup/)** | Attack host, VPN, engagement layout |
| 01 | **[Recon](01-Recon/)** | nmap, rustscan, masscan, subfinder/httpx, whatweb, [OSINT](01-Recon/OSINT-Toolkit.md), [scanners & threat intel](01-Recon/Internet-Scanners-and-Threat-Intel.md) |
| 02 | **[Enumeration](02-Enumeration/)** | Per-service: [FTP](02-Enumeration/FTP.md) · [SSH](02-Enumeration/SSH.md) · [SMB](02-Enumeration/SMB.md) · [SNMP](02-Enumeration/SNMP.md) · [LDAP](02-Enumeration/LDAP.md) · netexec |
| 03 | **[Web](03-Web/)** | ffuf, gobuster, feroxbuster, nikto, nuclei, wpscan, sqlmap, [client-side JS](03-Web/Client-Side-JavaScript-Security.md) |
| 04 | **[Exploitation](04-Exploitation/)** | searchsploit, hydra, msfvenom, file transfer, [reverse shells](04-Exploitation/Reverse-Shells.md) |
| 05 | **[Privilege Escalation](05-Privilege-Escalation/)** | [Linux](05-Privilege-Escalation/Linux.md) & [Windows](05-Privilege-Escalation/Windows.md): linpeas/winpeas, pspy, [GTFOBins](05-Privilege-Escalation/GTFOBins.md), [LOLBAS](05-Privilege-Escalation/LOLBAS.md) |
| 06 | **[Active Directory](06-Active-Directory/)** | BloodHound, kerbrute, impacket, mimikatz, certipy, responder, [Kerberos golden/silver tickets](06-Active-Directory/Kerberos-Tickets.md) |
| 07 | **[Post-Exploitation & Pivoting](07-Post-Exploitation-Pivoting/)** | [Situational Awareness](07-Post-Exploitation-Pivoting/Situational-Awareness.md) (post-shell commands), ligolo-ng, chisel, SOCKS tunneling |
| 08 | **[Password Attacks](08-Password-Attacks/)** | hashcat, john, `*2john`, wordlists |
| 09 | **[Reporting](09-Reporting/)** | Evidence capture, report structure |
| 10 | **[Analysis & Forensics](10-Analysis-Forensics/)** | [OLEdump](10-Analysis-Forensics/OLEdump.md), [Crypto/RSA](10-Analysis-Forensics/Crypto-and-Encoding.md), olevba, pdfid, exiftool, binwalk, yara |
| 11 | **[Persistence](11-Persistence/)** | [Linux](11-Persistence/Linux.md) & [Windows](11-Persistence/Windows.md): cron, systemd, scheduled tasks, Run keys, WMI |

**Other:** [Misc](Misc/) (AI-assisted & adversary-emulation notes) · [OSCP](OSCP/) (exam study notes — a side section).

---

## 📌 Conventions

- `$IP` = target · `$LHOST` = your attack host · `$LPORT` = listener port · `$DOMAIN` = AD domain.
- Each tool entry gives a **one-line purpose, an install command, and the common syntax — with each flag explained**.

---

## ⚖️ Legal & ethical use

> For educational purposes only, and only for **lab-controlled, authorized penetration testing**. Run active techniques only against systems you own or are explicitly authorized to test. The authors accept no liability for misuse.
