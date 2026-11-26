# OSCP Exam Format

> Checked against OffSec sources on 2026-11-26. Verify the current **[OSCP Exam Guide](https://help.offsec.com/hc/en-us/articles/360040165632)** before your exam, since point values and scoring can change.

## Structure (based on current OffSec guidance)
- A **24-hour** hands-on exam, followed by a separate window (about 24 hours) to write and submit the report.
- The target set, based on current guidance, combines **standalone machines** and an **Active Directory set**. The AD set is scored as a chain.
- A passing score of **70 points** is the commonly cited threshold; confirm the exact breakdown in the current guide.
- OffSec has indicated a move toward **scaled scoring**. Confirm the effective date and mechanics in the official OSCP+ FAQ, as third-party summaries vary.

> **Analysis note:** Point values and the exact machine mix are the items most likely to change. Treat the official exam guide as authoritative over any third-party summary, including this page.

## Proctoring
- All OSCP+ exams are **proctored**. Proctoring can begin about 15 minutes before the start time.
- Expect identity verification and a webcam/screen-share check. The detailed rules are in OffSec's proctoring manual — read it before exam day.

## Points and flags
- Record `local.txt` and `proof.txt` contents with a screenshot that also shows the command context and the target IP.
- Points are awarded only for flags obtained within the rules. Flags obtained with prohibited tools are not scored — see [Restricted Tools](Restricted-Tools.md).

## Sources
- [OSCP Exam Guide — help.offsec.com](https://help.offsec.com/hc/en-us/articles/360040165632)
- [OSCP Exam Guide — offsec.com](https://www.offsec.com/offsec/oscp-exam-guide/)
