# Windows Persistence

> Authorized testing only. Record every artifact so it can be removed. Map each to MITRE ATT&CK in the report.

## Scheduled task (T1053.005)
```cmd
schtasks /create /tn "Updater" /tr "C:\Windows\Temp\update.exe" /sc onlogon /ru SYSTEM
```
- `/tn` task name, `/tr` program to run, `/sc onlogon` trigger (also `onstart`, `minute`), `/ru SYSTEM` run context. **Detect/clean:** `schtasks /query /fo LIST /v`; Task Scheduler library; event 4698.

## Registry Run keys (T1547.001)
```cmd
reg add HKCU\Software\Microsoft\Windows\CurrentVersion\Run /v Updater /t REG_SZ /d "C:\Windows\Temp\update.exe"
```
- Runs at user logon (use `HKLM` for all users / needs admin). **Detect/clean:** inspect `Run`/`RunOnce` under HKCU and HKLM; Autoruns (Sysinternals).

## Service (T1543.003)
```cmd
sc create Updater binPath= "C:\Windows\Temp\update.exe" start= auto
```
- `start= auto` — start at boot (note the space after `=`). **Detect/clean:** `sc query`; event 7045 (new service installed).

## Startup folder
```cmd
copy update.exe "%APPDATA%\Microsoft\Windows\Start Menu\Programs\Startup\"
```
- Runs at logon. **Detect/clean:** inspect per-user and all-users Startup folders.

## WMI event subscription (T1546.003)
- A permanent WMI event filter + consumer runs code on a trigger (e.g. at a set time). Advanced and stealthy. **Detect/clean:** `Get-WmiObject -Namespace root\subscription -Class __EventFilter` (and `CommandLineEventConsumer`, `__FilterToConsumerBinding`).

## Domain-level persistence
- **Golden / silver tickets** and **DCSync rights** are the strongest AD persistence — see [06 — Kerberos Tickets](../06-Active-Directory/Kerberos-Tickets.md).

## Defender checklist
- Sysinternals **Autoruns** for a full picture of auto-start locations.
- Alert on events 4698 (task), 7045 (service), 4720 (new user), and new WMI subscriptions.
- Enable Credential Guard and LSASS protection to blunt the credential theft that persistence relies on.
