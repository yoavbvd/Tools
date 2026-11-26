# 00 — Lab Setup

Bring up the attack host and target environment before active testing.

## Attack host
- **Kali Linux** or **Parrot OS** as the primary attack VM. Keep it patched.
- Snapshot the VM clean before each engagement so you can roll back.
- Install a wordlist pack: `sudo apt install seclists` (installs to `/usr/share/seclists`).

## Connectivity
```bash
# Connect a lab VPN (OffSec / Hack The Box pack)
sudo openvpn --config lab.ovpn
# Confirm the tunnel interface and route
ip a show tun0
```

## Target / AD lab
- To build a Windows Active Directory practice domain, see [Windows Server AD Lab Setup](../06-Active-Directory/Windows-Server-AD-Lab-Setup.md).
- Free vulnerable targets: VulnHub images, Hack The Box, OffSec Proving Grounds, TryHackMe.

## Engagement folder layout
```text
engagements/TARGET/
├── scans/      # nmap, rustscan, enumeration output
├── loot/       # credentials, hashes, dumped files
├── exploits/   # local copies of PoCs
├── www/        # files to serve to the target
└── report/     # screenshots and the draft report
```

See the top-level [START-A-PENTEST-LAB.md](../START-A-PENTEST-LAB.md) for the full walkthrough.
