# 11 — Persistence

Maintain access to a compromised host across reboots and credential changes. In an engagement, persistence is used **only when the scope allows it**, and every mechanism is documented so the client can remove it afterwards.

> Authorized testing only. Each technique below includes a **detection/cleanup** note for defenders. Persistence that is not cleaned up is a finding against you — track everything you set.

## Pages
- [Linux](Linux.md)
- [Windows](Windows.md)

## Rules of thumb
- **Log every persistence artifact** you create (path, time, host) so it can be reverted.
- Prefer the **least invasive** mechanism that proves the point.
- Persistence maps to **MITRE ATT&CK Tactic TA0003**; name the specific technique (e.g. T1053 Scheduled Task) in your report.
- See also [07 — Post-Exploitation & Pivoting](../07-Post-Exploitation-Pivoting/) and [06 — Active Directory](../06-Active-Directory/Kerberos-Tickets.md) (golden/silver tickets are domain persistence).
