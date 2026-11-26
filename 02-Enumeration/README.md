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

---

## netexec (nxc) — successor to CrackMapExec
- **Purpose:** enumerate and test credentials across SMB, WinRM, LDAP, MSSQL, SSH, FTP, and more.
- **Install:** `pipx install git+https://github.com/Pennyw0rth/NetExec`

```bash
nxc smb 10.10.10.0/24 -u users.txt -p 'Winter2026!' --continue-on-success
```
**Flags:**
- `smb` — the protocol module (`winrm`, `ldap`, `mssql`, `ssh`, `ftp` are others).
- `-u` — username or a file of usernames. `-p` — password or a file of passwords.
- `--continue-on-success` — keep going after the first valid login (needed for a full spray).
- `--shares` — list shares and your access. `--rid-brute` — enumerate domain users via RID cycling. `--users` — pull the user list.

> A `Pwn3d!` in nxc output means the credential has admin rights on that host.

---

## DNS enumeration
- **Install:** `sudo apt install dnsutils dnsrecon`

```bash
dig axfr @$IP domain.local
```
- `axfr` — request a full **zone transfer** (dumps every record if the server is misconfigured). `@$IP` — query this DNS server directly.

```bash
dnsrecon -d domain.local -n $IP -a
```
- `-d` — domain. `-n` — name server to use. `-a` — also attempt a zone transfer.

---

## General port/service sweep reference
After the initial nmap (see [01-Recon](../01-Recon/)), work each open port top-down. Do not skip "boring" ports — a forgotten FTP or SNMP service is a common foothold.
