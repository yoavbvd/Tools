# OSCP Quick Cheat Sheet

Exam-safe commands by phase. Nothing here uses a prohibited tool. See [Restricted Tools](Restricted-Tools.md).

## Scanning
```bash
rustscan -a $IP --ulimit 5000 -- -sCV -oN scans/rs.txt
nmap -sC -sV -p- --min-rate 1000 -oA scans/tcp $IP
sudo nmap -sU --top-ports 100 -oA scans/udp $IP
```

## Web content discovery
```bash
ffuf -w /usr/share/seclists/Discovery/Web-Content/raft-medium-directories.txt -u http://$IP/FUZZ -mc 200,301,302,403
gobuster dir -u http://$IP -w /usr/share/wordlists/dirb/common.txt -x php,txt,html
nikto -h http://$IP
```

## SMB / AD enumeration
```bash
smbclient -L //$IP -N
enum4linux-ng -A $IP
nxc smb $IP -u user -p pass --shares
kerbrute userenum -d $DOMAIN --dc $IP users.txt
impacket-GetUserSPNs $DOMAIN/user:pass -request
```

## Privilege escalation
```bash
# Linux
sudo -l; find / -perm -u=s -type f 2>/dev/null; ./linpeas.sh
# Windows
whoami /priv; .\winPEASx64.exe
```

## Cracking
```bash
hashcat -m 1000 -a 0 hashes.txt /usr/share/wordlists/rockyou.txt
john --wordlist=/usr/share/wordlists/rockyou.txt hashes.txt
```

## Shell handling
```bash
nc -lvnp 443
python3 -c 'import pty;pty.spawn("/bin/bash")'   # stabilize
```
