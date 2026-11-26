# Linux Privilege Escalation

## Automated enumeration

### linpeas — all-in-one enumeration
- **Purpose:** highlight likely escalation paths (SUID, sudo, cron, capabilities, secrets).
- **Install / run:**
```bash
# Transfer linpeas.sh to the target, then:
./linpeas.sh | tee loot/linpeas.txt
```

### pspy — watch processes without root
- **Purpose:** observe cron jobs and processes run by other users in real time.
- **Install:** download the `pspy64` binary from the pspy releases page.
- Run: `./pspy64`

## Manual checklist
```bash
sudo -l                                   # commands you can run as root
find / -perm -u=s -type f 2>/dev/null     # SUID binaries
getcap -r / 2>/dev/null                    # file capabilities
cat /etc/crontab; ls -la /etc/cron.*       # scheduled jobs
ss -tulpn                                  # internal listening ports
uname -a; cat /etc/os-release              # kernel / distro for known exploits
```

- Cross-check any SUID binary or sudo entry against **[GTFOBins](https://gtfobins.github.io)**.
- A writable cron script run by root, a weak `sudo` rule, or a kernel known-vuln are the most common wins.
