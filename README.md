# 🛡️ Tools — Pentesting & OSCP Knowledge Base

A phase-by-phase cybersecurity and penetration-testing knowledge base: tools, cheat sheets, and an OSCP study section, organized so you can start a lab fast.

> **For educational purposes only, and only for lab-controlled, authorized penetration testing.** Run active techniques only against systems you own or are explicitly authorized to test.

## 🖥️ Browsable UI

This repo ships a dark-themed single-page UI (`index.html`) with a per-folder view of every page, rendered in the browser with copy-to-clipboard on command blocks.

- **Live (GitHub Pages):** enable Pages in **Settings → Pages → Source: `main` / root**, then open `https://yoavbvd.github.io/Tools/`.
- **Local:** serve the folder (`python3 -m http.server`) and open `index.html`. Opening the file directly with `file://` will not work, because the browser blocks local `fetch` of the Markdown pages.

---

## 🚀 Start here

- **[START-A-PENTEST-LAB.md](START-A-PENTEST-LAB.md)** — step-by-step checklist to bring up a lab and begin an engagement.
- **[Pentesting-Field-Guide.md](Pentesting-Field-Guide.md)** — the cross-phase operational methodology (network, web, and Active Directory), with a red-team playbook.

---

## 🗂️ Sections by pentest phase

| # | Section | What it covers |
| --- | --- | --- |
| 00 | **[Lab Setup](00-Lab-Setup/)** | Attack host, VPN, engagement folder layout |
| 01 | **[Recon](01-Recon/)** | nmap, rustscan, subdomain discovery, [OSINT Toolkit](01-Recon/OSINT-Toolkit.md), [Internet Scanners & Threat Intel](01-Recon/Internet-Scanners-and-Threat-Intel.md) |
| 02 | **[Enumeration](02-Enumeration/)** | Per-service: [FTP](02-Enumeration/FTP.md), [SSH](02-Enumeration/SSH.md), [SMB](02-Enumeration/SMB.md), [SNMP](02-Enumeration/SNMP.md), [LDAP](02-Enumeration/LDAP.md), netexec |
| 03 | **[Web](03-Web/)** | ffuf, gobuster, feroxbuster, nikto, nuclei, sqlmap, [Client-Side JS Security](03-Web/Client-Side-JavaScript-Security.md) |
| 04 | **[Exploitation](04-Exploitation/)** | searchsploit, hydra, msfvenom/metasploit, [Reverse Shells](04-Exploitation/Reverse-Shells.md) |
| 05 | **[Privilege Escalation](05-Privilege-Escalation/)** | [Linux](05-Privilege-Escalation/Linux.md) & [Windows](05-Privilege-Escalation/Windows.md): linpeas/winpeas, pspy |
| 06 | **[Active Directory](06-Active-Directory/)** | BloodHound, kerbrute, impacket, evil-winrm, responder, [AD Lab Setup](06-Active-Directory/Windows-Server-AD-Lab-Setup.md) |
| 07 | **[Post-Exploitation & Pivoting](07-Post-Exploitation-Pivoting/)** | ligolo-ng, chisel, SOCKS tunneling |
| 08 | **[Password Attacks](08-Password-Attacks/)** | hashcat, john, hash identification |
| 09 | **[Reporting](09-Reporting/)** | Evidence capture, report structure |

---

## 🎓 OSCP

- **[OSCP Section](OSCP/)** — exam study hub.
  - [Exam Format](OSCP/Exam-Format.md) · [Restricted Tools](OSCP/Restricted-Tools.md) · [Methodology](OSCP/Methodology.md) · [Cheat Sheet](OSCP/Cheat-Sheet.md) · [Report Tips](OSCP/Report-Tips.md) · [Practice Resources](OSCP/Practice-Resources.md)

> Exam rules were verified against OffSec sources on **2026-11-26**. Always confirm the current [OSCP Exam Guide](https://help.offsec.com/hc/en-us/articles/360040165632) before your exam.

---

## 🧩 Misc

- **[Misc](Misc/)** — [AI-Assisted Pentesting & Adversary Emulation](Misc/AI-Assisted-Pentesting-and-Adversary-Emulation.md) (ClaudeRed / ARTEX findings and vetted alternatives).

---

## 📌 Conventions

- Throughout the guide, `$IP` is the target, `$LHOST` is your attack host, and `$DOMAIN` is the AD domain.
- Each tool entry gives a one-line purpose, an install command, and a small table of common syntax.
