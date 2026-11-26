# Windows Privilege Escalation

Run the automated enumerator first, then confirm each lead by hand.

## Automated enumeration

### winpeas — all-in-one enumeration
```powershell
.\winPEASx64.exe > loot\winpeas.txt
```
- Surfaces service, registry, token, and credential misconfigurations. Yellow/red highlights are the leads. Use `winPEASany.exe` when unsure of architecture.

### PrivescCheck — focused PowerShell audit
```powershell
powershell -ep bypass -c ". .\PrivescCheck.ps1; Invoke-PrivescCheck -Extended"
```
- `-ep bypass` — allow the unsigned script to run for this process only. `-Extended` — run the deeper checks.

## Manual checklist
```powershell
whoami /priv          # privileges — look for SeImpersonatePrivilege, SeBackupPrivilege
cmdkey /list          # saved credentials usable with runas
systeminfo            # OS build and installed hotfixes (feed to WES-NG)
# Unquoted service paths
wmic service get name,pathname,startmode | findstr /i "Auto" | findstr /i /v "C:\Windows"
```
**Flag notes:**
- `whoami /priv` — lists the token privileges; `SeImpersonatePrivilege` often leads to SYSTEM via a "Potato" technique.
- `findstr /i` — case-insensitive match; `/v` — invert (exclude). Together they keep auto-start services **not** under `C:\Windows` — candidates for an unquoted-path hijack.

## Map missing patches
- Run **WES-NG** against `systeminfo` output: `wes.py systeminfo.txt` maps missing KBs to public exploits.

## Living off the land
- Abuse trusted signed binaries instead of dropping tools — see the [LOLBAS page](LOLBAS.md).

## Common wins
- `SeImpersonatePrivilege` → PrintSpoofer / Potato to SYSTEM.
- Writable service binary or unquoted service path.
- Stored credentials (`cmdkey`, registry, unattended-install files like `Unattend.xml`).
- AlwaysInstallElevated registry keys set.
