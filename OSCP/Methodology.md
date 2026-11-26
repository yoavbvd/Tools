# OSCP Methodology

A repeatable loop for each exam target. The goal is thorough enumeration — most failures are missed enumeration, not missed exploits.

## 1. Enumerate fully
- Full TCP port scan, then deep scan of open ports. UDP top ports in parallel.
- For every service, run its enumeration page in [02-Enumeration](../02-Enumeration/). Do not skip a port because it looks dull.
- Keep a per-target notes file. Write down every version, path, and credential.

## 2. Find the foothold
- Match service versions to public exploits (`searchsploit`).
- Test default and weak credentials.
- For web, do content discovery, then test inputs manually (SQLi, file upload, LFI, command injection). Remember SQLmap is prohibited — inject by hand.

## 3. Get a stable shell
- Catch a reverse shell, then stabilize it. See [Reverse Shells](../04-Exploitation/Reverse-Shells.md).

## 4. Escalate privileges
- Run linpeas/winpeas, then confirm each lead manually. See [05-Privilege-Escalation](../05-Privilege-Escalation/).
- Grab `local.txt` and `proof.txt` with a screenshot as soon as you can.

## 5. Active Directory chain
- From a first set of domain credentials, map with BloodHound, then follow the shortest path (Kerberoast, AS-REP, misconfig) toward Domain Admin. See [06-Active-Directory](../06-Active-Directory/).

## 6. Pivot if needed
- Tunnel into internal subnets with ligolo-ng or chisel. See [07-Post-Exploitation-Pivoting](../07-Post-Exploitation-Pivoting/).

## Mindset
- When stuck, re-enumerate. Go back to the scan output and the pages you skimmed.
- Take a short break every few hours. Fatigue causes missed detail.
