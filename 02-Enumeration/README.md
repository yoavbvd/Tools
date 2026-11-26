# 02 — Service Enumeration

Enumerate each open service in depth. Pick the page for the port you found.

## Per-service pages
- [FTP (21)](FTP.md)
- [SSH (22)](SSH.md)
- [SMB (139/445)](SMB.md)
- [SNMP (161/udp)](SNMP.md)
- [LDAP (389/636)](LDAP.md)
- HTTP/HTTPS (80/443) → see [03-Web](../03-Web/)
- Kerberos / AD services (88/389/445) → see [06-Active-Directory](../06-Active-Directory/)

## General multi-protocol tools

### netexec (nxc) — successor to CrackMapExec
- **Purpose:** enumerate and test credentials across SMB, WinRM, LDAP, MSSQL, SSH, FTP, and more.
- **Install:** `pipx install git+https://github.com/Pennyw0rth/NetExec`

| Task | Command |
| --- | --- |
| SMB host info | `nxc smb $IP` |
| Credential check | `nxc smb $IP -u user -p pass` |
| Password spray | `nxc smb $IP -u users.txt -p 'Winter2026!' --continue-on-success` |
| List shares | `nxc smb $IP -u user -p pass --shares` |

### DNS enumeration
- **Purpose:** resolve names, attempt zone transfers, find records.
- **Install:** `sudo apt install dnsutils dnsrecon`

| Task | Command |
| --- | --- |
| Zone transfer attempt | `dig axfr @$IP domain.local` |
| Record sweep | `dnsrecon -d domain.local -n $IP` |
