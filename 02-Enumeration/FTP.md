# FTP (21)

- **Purpose:** check for anonymous access, readable/writable files, and known server-version exploits.

| Task | Command |
| --- | --- |
| Banner / version | `nmap -sV -p21 $IP` |
| FTP scripts | `nmap --script "ftp-*" -p21 $IP` |
| Anonymous login | `ftp $IP` → user `anonymous`, any password |
| Non-interactive grab | `wget -r ftp://anonymous:anonymous@$IP/` |

- If writable, you may be able to upload a payload that another service executes (for example a web root).
- Search for exploits by exact version: `searchsploit vsftpd 2.3.4`.
