# SSH (22)

- **Purpose:** identify version, supported auth methods, and test weak or reused credentials.

| Task | Command |
| --- | --- |
| Banner / version | `nmap -sV -p22 $IP` |
| Auth methods | `ssh -v user@$IP` (read the offered methods) |
| Credential brute force | `hydra -L users.txt -P pass.txt ssh://$IP` |
| Key login | `ssh -i id_rsa user@$IP` |

- Treat SSH brute force as noisy and often rate-limited. Prefer credentials found elsewhere.
- A recovered private key may need a passphrase crack — see [08-Password-Attacks](../08-Password-Attacks/).
