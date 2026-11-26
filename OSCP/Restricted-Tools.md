# OSCP Restricted and Prohibited Tools

> **Verification:** Checked against OffSec's OSCP+ Exam Guide and AI policy on **2026-11-26**. This is the item students most often get wrong — confirm the current wording in the **[OSCP Exam Guide](https://help.offsec.com/hc/en-us/articles/360040165632)** before your exam. OffSec states it will not comment on tool status beyond the exam guide.

## Prohibited (do not use at all)
| Category | Examples named by OffSec |
| --- | --- |
| AI chatbots / LLMs | OffSec KAI, ChatGPT, Gemini, Copilot, DeepSeek, and similar assistants with direct prompt access |
| Spoofing | IP, ARP, DNS, NBNS spoofing/poisoning (this rules out Responder-style poisoning) |
| Commercial tools | Burp Suite Pro, Metasploit Pro, and other paid editions |
| Automatic exploitation | db_autopwn, browser_autopwn, **SQLmap**, SQLninja |
| Mass vulnerability scanners | Nessus, NeXpose, OpenVAS, Canvas, Core Impact, SAINT |

## Restricted (limited use)
- **Metasploit Framework and Meterpreter:** restricted. Current guidance limits their use (commonly cited as **one target machine only**). Verify the exact limit in the exam guide. Build the habit of manual exploitation.

## Allowed
- Any tool that performs **no restricted action**.
- Tools that perform **automatic enumeration** are explicitly allowed (for example nmap scripts, enum4linux-ng, nikto, gobuster, ffuf).
- **BloodHound** Legacy and Community Edition are allowed.
- Non-interactive AI features (for example Notion AI, Google AI Overview) need not be disabled, but you must not query a chatbot for help.

## Why the AI ban matters
- OffSec classifies LLM use as third-party help and as sharing exam information — both Academic Policy violations that can void the result.

## Sources
- [OSCP Exam Guide — help.offsec.com](https://help.offsec.com/hc/en-us/articles/360040165632)
- [OffSec AI usage policy — help.offsec.com](https://help.offsec.com/hc/en-us/articles/35549468971156)
