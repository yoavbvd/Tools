# 01 — Reconnaissance

Map the target: discover hosts, open ports, services, subdomains, and public exposure.

## Contents
- [OSINT Toolkit](OSINT-Toolkit.md) — passive intelligence frameworks and search engines.
- [Internet Scanners and Threat Intel](Internet-Scanners-and-Threat-Intel.md) — Shodan, Censys, reputation and passive-DNS sources, plus hash-identification notes.

---

## nmap — network and service scanner
- **Purpose:** discover open ports, service versions, and run default enumeration scripts.
- **Install:** `sudo apt install nmap`

```bash
nmap -sC -sV -p- --min-rate 1000 -oA scans/tcp $IP
```
**Flags:**
- `-sC` — run the default safe NSE scripts (banner, titles, common checks).
- `-sV` — probe open ports to identify the service and version.
- `-p-` — scan all 65535 TCP ports (`-p 80,443` for specific ports; `--top-ports 1000` for the common set).
- `--min-rate 1000` — send at least 1000 packets/sec, so a full scan finishes faster.
- `-oA scans/tcp` — write output in all three formats (`.nmap`, `.gnmap`, `.xml`) using the base name `scans/tcp`.

```bash
sudo nmap -sU --top-ports 100 -oN scans/udp.txt $IP
```
- `-sU` — UDP scan (needs root; slower than TCP). `--top-ports 100` — the 100 most common UDP ports. `-oN` — normal human-readable output.

```bash
nmap --script "smb-vuln*" -p445 $IP
```
- `--script "smb-vuln*"` — run every NSE script whose name starts with `smb-vuln` (category/wildcard selection).

Timing: add `-T4` for a faster scan on reliable networks (`-T0`..`-T5`, higher = faster/noisier).

---

## rustscan — fast port sweeper
- **Purpose:** find open ports very fast, then hand them to nmap for deep scanning.
- **Install:** `cargo install rustscan`, or the official Docker image.

```bash
rustscan -a $IP --ulimit 5000 -- -sC -sV
```
**Flags:**
- `-a` — address/target (single IP, CIDR, or comma list).
- `--ulimit 5000` — raise the open-file-descriptor limit so more ports are probed in parallel (speed).
- `--` — everything after this is passed straight to nmap (here `-sC -sV` run against the ports rustscan found).

---

## masscan — internet-scale port scanner
- **Purpose:** scan very large ranges extremely fast (asynchronous).
- **Install:** `sudo apt install masscan`

```bash
sudo masscan 10.10.10.0/24 -p1-65535 --rate 1000 -oG scans/masscan.gnmap
```
- `-p1-65535` — port range. `--rate 1000` — packets/sec (raise with care; high rates can disrupt targets). `-oG` — greppable output. Confirm findings with nmap `-sV`.

---

## Subdomain & host discovery (ProjectDiscovery set)
- **Install:** `go install github.com/projectdiscovery/{subfinder/v2/cmd/subfinder,dnsx,httpx}@latest` (install each as needed).

```bash
subfinder -d target.com -all -o subs.txt
```
- `-d` — domain. `-all` — use all passive sources. `-o` — output file.

```bash
cat subs.txt | dnsx -silent -a -resp | tee resolved.txt
```
- `dnsx` resolves names. `-a` — query A records. `-resp` — show the resolved value. `-silent` — only results, no banner.

```bash
cat resolved.txt | httpx -silent -title -tech-detect -status-code
```
- `httpx` probes which hosts serve HTTP. `-title` — page title. `-tech-detect` — fingerprint technologies (Wappalyzer-style). `-status-code` — show HTTP status.

```bash
amass enum -d target.com -o amass.txt
```
- `amass` does deeper active+passive mapping. `enum` — enumeration mode. `-d` — domain. `-o` — output.

---

## whatweb — web technology fingerprint
- **Install:** `sudo apt install whatweb`
```bash
whatweb -a 3 http://$IP
```
- `-a 3` — aggression level 3 (thorough; sends more requests). Levels 1 (stealthy) to 4 (heavy).
