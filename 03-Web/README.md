# 03 — Web Application Testing

Enumerate and test HTTP/HTTPS services: hidden content, parameters, and common web vulnerabilities.

## Contents
- [Client-Side JavaScript Security](Client-Side-JavaScript-Security.md) — how client-side JS is abused and the defenses (CSP, HttpOnly, SRI).

---

## ffuf — fast web fuzzer
- **Purpose:** fuzz directories, files, parameters, and virtual hosts.
- **Install:** `go install github.com/ffuf/ffuf/v2@latest`

```bash
ffuf -w /usr/share/seclists/Discovery/Web-Content/raft-medium-directories.txt \
     -u http://10.10.10.50/FUZZ -mc 200,301,302,403 -recursion -recursion-depth 2
```
**Flags:**
- `-w` — wordlist. `FUZZ` in the URL is where each word is inserted.
- `-u` — target URL containing the `FUZZ` keyword.
- `-mc 200,301,302,403` — **match** these HTTP status codes (keep only these).
- `-fc 404` — **filter** out a status code; `-fs 1234` — filter out a response size; `-fw`/`-fl` filter by word/line count. Use these to hide a noisy default response.
- `-recursion -recursion-depth 2` — when a directory is found, fuzz inside it, up to 2 levels deep.

```bash
# Virtual host discovery
ffuf -w subs.txt -u http://10.10.10.50 -H "Host: FUZZ.target.com" -fs 0
```
- `-H` — add a header; here the `Host` header is fuzzed to find name-based vhosts. `-fs 0` filters empty responses (tune to the default size).

---

## gobuster — directory / DNS / vhost brute forcer
- **Install:** `sudo apt install gobuster`

```bash
gobuster dir -u http://10.10.10.50 -w wordlist.txt -x php,txt,html -t 50
```
- `dir` — directory/file mode (`dns`, `vhost` are other modes). `-u` — URL. `-w` — wordlist.
- `-x php,txt,html` — append these extensions to each word (finds `admin.php`, `notes.txt`).
- `-t 50` — 50 concurrent threads. `-b 404,403` — blacklist status codes. `-k` — skip TLS verification.

---

## feroxbuster — recursive content discovery
- **Install:** `sudo apt install feroxbuster`
```bash
feroxbuster -u http://10.10.10.50 -w wordlist.txt -x php,txt -d 2 -C 404,403
```
- `-u` URL, `-w` wordlist, `-x` extensions. `-d 2` — recursion depth. `-C 404,403` — filter out these status codes. Recurses automatically by default.

---

## nikto — web server scanner
- **Install:** `sudo apt install nikto`
```bash
nikto -h http://10.10.10.50 -p 80,443 -o nikto.txt
```
- `-h` host. `-p` ports. `-o` output file. Flags common misconfigs, dangerous files, and outdated software.

---

## nuclei — template-based scanner
- **Install:** `go install github.com/projectdiscovery/nuclei/v3/cmd/nuclei@latest`
```bash
nuclei -u http://10.10.10.50 -severity critical,high,medium -o nuclei.txt
```
- `-u` target. `-severity` — only run templates of these severities. `-t <dir>` — run a specific template set. `-o` — output.

---

## wpscan — WordPress scanner
- **Install:** `sudo apt install wpscan`
```bash
wpscan --url http://10.10.10.50 --enumerate u,vp,ap
```
- `--url` target. `--enumerate u` — users, `vp` — vulnerable plugins, `ap` — all plugins. Add `--api-token <t>` for vuln data.

---

## sqlmap — SQL injection
- **Purpose:** detect and exploit SQL injection.
- **Install:** `sudo apt install sqlmap`
```bash
sqlmap -r request.txt --batch --level 3 --risk 2 --dbs
```
- `-r request.txt` — read the HTTP request from a saved file (capture it in Burp/ZAP). `-u <url>` is the alternative.
- `--batch` — never prompt; take default answers.
- `--level 3` — test more injection points (1–5). `--risk 2` — allow heavier payloads (1–3).
- `--dbs` — enumerate databases. Then `-D db -T users --dump` to dump a table, `--os-shell` for a shell.

---

## Manual testing mindset
Automated scanners miss logic flaws. Test inputs by hand for SQLi, command injection, LFI/path traversal, SSRF, IDOR, and file-upload bypass. Capture and replay requests with **Burp Suite (Community)** or **OWASP ZAP**.
