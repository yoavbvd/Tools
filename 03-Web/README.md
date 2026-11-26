# 03 — Web Application Testing

Enumerate and test HTTP/HTTPS services: hidden content, parameters, and common web vulnerabilities.

## Contents
- [Client-Side JavaScript Security](Client-Side-JavaScript-Security.md) — how client-side JS is abused and the defenses (CSP, HttpOnly, SRI).

## Content discovery

### ffuf — fast web fuzzer
- **Purpose:** fuzz directories, files, parameters, and virtual hosts.
- **Install:** `go install github.com/ffuf/ffuf/v2@latest`

| Task | Command |
| --- | --- |
| Directory fuzz | `ffuf -w wordlist.txt -u http://$IP/FUZZ` |
| Hide 404s | `ffuf -w wordlist.txt -u http://$IP/FUZZ -mc 200,301,302` |
| VHost fuzz | `ffuf -w subs.txt -u http://$IP -H "Host: FUZZ.target.com" -fs 0` |
| Parameter fuzz | `ffuf -w params.txt -u "http://$IP/page.php?FUZZ=test"` |

### gobuster — directory / DNS / vhost brute forcer
- **Install:** `sudo apt install gobuster`

| Task | Command |
| --- | --- |
| Directories | `gobuster dir -u http://$IP -w wordlist.txt` |
| With extensions | `gobuster dir -u http://$IP -w wordlist.txt -x php,txt,html` |
| DNS subdomains | `gobuster dns -d target.com -w subs.txt` |

### feroxbuster — recursive content discovery
- **Install:** `sudo apt install feroxbuster`

| Task | Command |
| --- | --- |
| Recursive scan | `feroxbuster -u http://$IP -w wordlist.txt` |
| Filter by status | `feroxbuster -u http://$IP -w wordlist.txt -C 404,403` |

## Vulnerability checks

### nikto — web server scanner
- **Install:** `sudo apt install nikto`
- Basic scan: `nikto -h http://$IP`

### nuclei — template-based scanner
- **Install:** `go install github.com/projectdiscovery/nuclei/v3/cmd/nuclei@latest`
- Run: `nuclei -u http://$IP -severity critical,high,medium`

### sqlmap — SQL injection
- **Purpose:** detect and exploit SQL injection.
- **Install:** `sudo apt install sqlmap`

| Task | Command |
| --- | --- |
| Test a saved request | `sqlmap -r request.txt --batch` |
| Enumerate databases | `sqlmap -r request.txt --batch --dbs` |
| Dump a table | `sqlmap -r request.txt --batch -D db -T users --dump` |
