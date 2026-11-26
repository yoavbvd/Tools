# 01 — Reconnaissance

Map the target: discover hosts, open ports, services, subdomains, and public exposure.

## Contents
- [OSINT Toolkit](OSINT-Toolkit.md) — passive intelligence frameworks and search engines.
- [Internet Scanners and Threat Intel](Internet-Scanners-and-Threat-Intel.md) — Shodan, Censys, reputation and passive-DNS sources, plus hash-identification notes.

## Core active tools

### nmap — network and service scanner
- **Purpose:** discover open ports, service versions, and run default enumeration scripts.
- **Install:** `sudo apt install nmap`

| Task | Command |
| --- | --- |
| Default scripts + versions | `nmap -sC -sV -oA out $IP` |
| All TCP ports | `nmap -p- --min-rate 1000 -oA full $IP` |
| UDP top 100 | `sudo nmap -sU --top-ports 100 -oA udp $IP` |
| Specific scripts | `nmap --script "smb-vuln*" -p445 $IP` |
| Scan from a list | `nmap -iL hosts.txt -oA sweep` |

### rustscan — fast port sweeper
- **Purpose:** find open ports very fast, then hand them to nmap.
- **Install:** `cargo install rustscan` or use the official Docker image.

| Task | Command |
| --- | --- |
| Fast sweep → nmap | `rustscan -a $IP --ulimit 5000 -- -sCV` |
| Subnet | `rustscan -a 10.10.10.0/24` |

### subfinder / amass — subdomain discovery
- **Purpose:** enumerate subdomains of an in-scope domain (passive and active).
- **Install:** `go install github.com/projectdiscovery/subfinder/v2/cmd/subfinder@latest`

| Task | Command |
| --- | --- |
| Passive subdomains | `subfinder -d target.com -o subs.txt` |
| In-depth mapping | `amass enum -d target.com` |
