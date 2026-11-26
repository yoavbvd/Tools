# SNMP (161/udp)

- **Purpose:** read device and system data exposed through community strings (often default `public`).

| Task | Command |
| --- | --- |
| Find the service | `sudo nmap -sU -p161 -sV $IP` |
| Brute community strings | `onesixtyone -c /usr/share/seclists/Discovery/SNMP/snmp.txt $IP` |
| Walk the MIB tree | `snmpwalk -v2c -c public $IP` |
| Processes / software / ports | `snmpwalk -v2c -c public $IP 1.3.6.1.2.1.25` |

- **Install:** `sudo apt install snmp onesixtyone snmp-mibs-downloader`
- SNMP can reveal running processes, installed software, user accounts, and listening ports.
