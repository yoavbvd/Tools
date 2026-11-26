# Linux Privilege Escalation

Run the automated enumerator first, then confirm each lead by hand.

## Automated enumeration

### linpeas — all-in-one enumeration
- **Purpose:** highlight likely escalation paths (SUID, sudo, cron, capabilities, secrets, kernel).
```bash
# Transfer linpeas.sh to the target, then:
./linpeas.sh -a | tee loot/linpeas.txt
```
- `-a` — run **all** checks (thorough). Output is color-coded: red/yellow highlights are the strongest leads. `tee` keeps a copy for the report.

### pspy — watch processes without root
- **Purpose:** observe cron jobs and other users' commands in real time, without privileges.
```bash
./pspy64 -pf -i 1000
```
- `-p` — watch process events. `-f` — watch file-system events (shows which files jobs touch). `-i 1000` — poll every 1000 ms.

### linux-exploit-suggester — kernel exploit matcher
```bash
./linux-exploit-suggester.sh
```
- Compares `uname -r` and the distro against known local-root exploits. Treat results as leads; verify the kernel/version before running anything.

## Manual checklist
```bash
sudo -l                                  # what you may run as root (look for NOPASSWD)
find / -perm -u=s -type f 2>/dev/null    # SUID binaries
getcap -r / 2>/dev/null                  # file capabilities (e.g. cap_setuid)
cat /etc/crontab; ls -la /etc/cron.*     # scheduled jobs you might hijack
ss -tulpn                                # internal listening services to pivot to
uname -a; cat /etc/os-release            # kernel/distro for known exploits
```
**Flag notes:**
- `find ... -perm -u=s` — match files with the SUID bit set; `-type f` restricts to regular files; `2>/dev/null` hides permission-denied noise.
- `getcap -r /` — recurse the whole filesystem for capabilities.
- `ss -tulpn` — `t` TCP, `u` UDP, `l` listening, `p` owning process, `n` numeric ports.

## Where to escalate
- Cross-check any SUID binary, sudo rule, or capability against **[GTFOBins](https://gtfobins.github.io)** for a known escalation.
- Most common wins: a writable root cron script, a `sudo` NOPASSWD entry, a SUID GTFOBin, or a known kernel exploit.
