# SMB (139/445)

- **Purpose:** list shares, pull files, map users, and spot known SMB vulnerabilities.

### smbclient — list and access shares
- **Install:** `sudo apt install smbclient`

| Task | Command |
| --- | --- |
| List shares (null session) | `smbclient -L //$IP -N` |
| Connect to a share | `smbclient //$IP/share -N` |
| Recursive download | `smbclient //$IP/share -N -c 'recurse ON; prompt OFF; mget *'` |

### enum4linux-ng — full SMB/RPC enumeration
- **Install:** `pipx install enum4linux-ng` or clone the repo.

| Task | Command |
| --- | --- |
| Full enumeration | `enum4linux-ng -A $IP` |
| Users and shares only | `enum4linux-ng -U -S $IP` |

### nmap and netexec
| Task | Command |
| --- | --- |
| SMB vuln scripts | `nmap --script "smb-vuln*" -p445 $IP` |
| Shares via netexec | `nxc smb $IP -u '' -p '' --shares` |
| Users via RID brute | `nxc smb $IP -u guest -p '' --rid-brute` |
