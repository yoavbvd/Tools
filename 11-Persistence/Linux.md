# Linux Persistence

> Authorized testing only. Record every artifact so it can be removed.

## SSH authorized key
```bash
mkdir -p ~/.ssh && echo "ssh-ed25519 AAAA... you@host" >> ~/.ssh/authorized_keys
chmod 600 ~/.ssh/authorized_keys
```
- Adds your public key so you can log in without a password. **Detect/clean:** review `~/.ssh/authorized_keys` for every user; compare against a baseline.

## Cron job
```bash
(crontab -l 2>/dev/null; echo "*/10 * * * * /usr/local/bin/update.sh") | crontab -
```
- `*/10 * * * *` — run every 10 minutes. **Detect/clean:** `crontab -l` per user, and inspect `/etc/cron*`, `/var/spool/cron/`.

## systemd service / timer
```ini
# /etc/systemd/system/updater.service  (root) — then: systemctl enable --now updater
[Service]
ExecStart=/usr/local/bin/update.sh
[Install]
WantedBy=multi-user.target
```
- `enable` — start at boot. **Detect/clean:** `systemctl list-unit-files --state=enabled`; look for unsigned units in `/etc/systemd/system`.

## Shell profile / rc files
```bash
echo '/usr/local/bin/update.sh &' >> ~/.bashrc
```
- Runs on each interactive shell. **Detect/clean:** diff `~/.bashrc`, `~/.bash_profile`, `/etc/profile.d/*`.

## Other mechanisms to know
- **SUID backdoor** — a copied shell with the SUID bit (noisy; easy to find with `find / -perm -4000`).
- **Malicious PAM / LD_PRELOAD** — advanced; detect with file-integrity monitoring.
- **Service account / new user** — `useradd`; detect via `/etc/passwd` diff and auth logs.

## Defender checklist
- File-integrity monitoring (AIDE/Tripwire) on `~/.ssh`, cron paths, systemd dirs, rc files.
- Alert on new SUID files, new users, and new enabled services.
