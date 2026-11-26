# 08 — Password Attacks

Crack recovered hashes offline and test credentials. Online brute force (hydra) lives in [04-Exploitation](../04-Exploitation/).

## Identify the hash
- Note the hash format before cracking. See the hash-identification notes in [01-Recon/Internet-Scanners-and-Threat-Intel](../01-Recon/Internet-Scanners-and-Threat-Intel.md).
- Tools: `hashid 'HASH'` or `hash-identifier`.

## hashcat — GPU cracker
- **Install:** `sudo apt install hashcat`

| Task | Command |
| --- | --- |
| NTLM + wordlist | `hashcat -m 1000 -a 0 hashes.txt rockyou.txt` |
| Kerberoast (TGS) | `hashcat -m 13100 -a 0 hashes.txt rockyou.txt` |
| AS-REP | `hashcat -m 18200 -a 0 hashes.txt rockyou.txt` |
| With rules | `hashcat -m 1000 hashes.txt rockyou.txt -r /usr/share/hashcat/rules/best64.rule` |

Common modes: `0` MD5, `100` SHA1, `1000` NTLM, `1800` sha512crypt, `3200` bcrypt.

## john — John the Ripper
- **Install:** `sudo apt install john`

| Task | Command |
| --- | --- |
| Auto wordlist | `john --wordlist=rockyou.txt hashes.txt` |
| Force a format | `john --format=NT --wordlist=rockyou.txt hashes.txt` |
| Show cracked | `john --show hashes.txt` |
| Convert (e.g. SSH key) | `ssh2john id_rsa > hash.txt` |

- The default wordlist `rockyou.txt` ships at `/usr/share/wordlists/rockyou.txt.gz` on Kali (gunzip first).
