# 08 — Password Attacks

Crack recovered hashes offline and test credentials. Online brute force (hydra) lives in [04-Exploitation](../04-Exploitation/).

## Identify the hash first
```bash
hashid '$1$abc$...'
# or
hash-identifier
```
- `hashid` prints likely hash types and their hashcat/john mode numbers. See also the hash notes in [01-Recon/Internet-Scanners-and-Threat-Intel](../01-Recon/Internet-Scanners-and-Threat-Intel.md).

---

## hashcat — GPU cracker
- **Install:** `sudo apt install hashcat`
```bash
hashcat -m 1000 -a 0 hashes.txt /usr/share/wordlists/rockyou.txt -r /usr/share/hashcat/rules/best64.rule
```
**Flags:**
- `-m 1000` — hash **mode**. Common: `0` MD5, `100` SHA1, `1000` NTLM, `1800` sha512crypt, `3200` bcrypt, `13100` Kerberoast (TGS), `18200` AS-REP.
- `-a 0` — **attack mode**: `0` wordlist, `1` combinator, `3` brute-force/mask, `6`/`7` hybrid.
- `-r <rule>` — apply a mutation rule file (adds digits, capitalizes, leetspeak) to each word.
- `--force` — ignore warnings (needed in some VMs). `-O` — optimized kernels (faster, limited length). `--show` — print already-cracked hashes.

```bash
# Mask attack: 8 chars, upper+lower+digit
hashcat -m 1000 -a 3 hashes.txt '?u?l?l?l?l?l?d?d'
```
- `?u` upper, `?l` lower, `?d` digit, `?s` symbol, `?a` all. Each `?x` is one position.

---

## john — John the Ripper
- **Install:** `sudo apt install john`
```bash
john --wordlist=/usr/share/wordlists/rockyou.txt --format=NT hashes.txt
john --show hashes.txt
```
**Flags:**
- `--wordlist=<file>` — dictionary mode with this list. Without it, John runs its default modes.
- `--format=NT` — force the hash format (use when auto-detect guesses wrong).
- `--rules` — apply John's mangling rules to the wordlist.
- `--show` — display cracked passwords from the pot file.

**`*2john` converters** turn a protected file into a crackable hash:
```bash
ssh2john id_rsa > hash.txt       # SSH private key passphrase
zip2john secret.zip > hash.txt   # ZIP archive password
```
Then crack `hash.txt` with john or hashcat.

---

## Wordlists & rules
- `rockyou.txt` — ships at `/usr/share/wordlists/rockyou.txt.gz` on Kali (`gunzip` it first).
- **SecLists** (`sudo apt install seclists`) — `/usr/share/seclists/Passwords/` has many focused lists.
- Generate a target-specific list: `cewl http://10.10.10.50 -w custom.txt` (`-w` output file) scrapes words from the site.
