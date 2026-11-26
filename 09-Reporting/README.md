# 09 — Reporting and Evidence

The report is the deliverable. Capture evidence as you work, not at the end.

## Capture evidence continuously
- Screenshot the moment you obtain a flag, shell, or credential. Include the target IP and a timestamp in frame.
- Log terminal sessions:
```bash
script -a engagement-log.txt     # record a shell session
# or use tmux logging
```

## Evidence layout
```text
report/
├── screenshots/
├── loot/            # hashes, dumped files, credentials
└── report_draft.md
```

## Report structure (Konfidas format)
For each finding, use: **Finding → Severity (Critical / High / Medium / Low) → Impact → Recommendation → References.**

- **Executive summary:** risk and business impact, in plain language.
- **Methodology:** scope, dates, approach.
- **Findings:** one per issue, with reproduction steps and evidence.
- **Remediation:** prioritized, actionable fixes.

## Tools
- **Markdown + pandoc** for a clean PDF, or **Dradis** / **SysReptor** for structured reporting.
