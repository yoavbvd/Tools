# Windows Privilege Escalation

## Automated enumeration

### winpeas — all-in-one enumeration
- **Purpose:** surface misconfigurations (services, registry, tokens, stored credentials).
- **Install / run:**
```powershell
.\winPEASx64.exe > loot\winpeas.txt
```

## Manual checklist
```powershell
whoami /priv          # look for SeImpersonatePrivilege, SeBackupPrivilege
cmdkey /list          # saved credentials
# Unquoted service paths
wmic service get name,pathname,startmode | findstr /i "Auto" | findstr /i /v "C:\Windows"
systeminfo            # OS build / hotfixes for known exploits
```

- **SeImpersonatePrivilege** often leads to SYSTEM via a "Potato"-class technique.
- Check writable service binaries, scheduled tasks, and AlwaysInstallElevated.
- Use **WES-NG** (Windows Exploit Suggester – Next Generation) against `systeminfo` output to map missing patches to public exploits.
