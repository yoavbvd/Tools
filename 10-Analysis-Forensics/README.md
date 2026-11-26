# 10 — Analysis & Forensics

Static analysis of suspicious documents and files: extract macros, carve embedded objects, read metadata, and triage with signatures. Useful for malware triage, phishing analysis, and CTF forensics.

> Analyze untrusted samples in an isolated VM with no production network access. Treat every sample as live malware.

## Contents
- [OLEdump](OLEdump.md) — analyze OLE / Microsoft Office files and extract macros.

## Tool index

### olevba — macro extraction and triage
- **Purpose:** extract and deobfuscate VBA macros from Office files and flag suspicious keywords.
- **Install:** `pipx install oletools`

```bash
olevba --decode suspicious.doc
```
- `--decode` — show decoded strings (Hex, Base64, StrReverse, Dridex) next to the raw macro.
- `-a` / `--analysis` — only print the analysis table (IOCs, auto-exec triggers, suspicious calls).
- `-c` / `--code` — only print the extracted VBA source.

### pdfid / pdf-parser — PDF triage
- **Purpose:** flag risky PDF structures (JavaScript, auto-actions, embedded files), then inspect them.
- **Install:** `pipx install pdfid` (Didier Stevens Suite), or clone the suite.

```bash
pdfid.py suspicious.pdf
```
- Output counts of `/JavaScript`, `/OpenAction`, `/Launch`, `/EmbeddedFile`. Non-zero counts warrant a closer look.
```bash
pdf-parser.py -s JavaScript --raw suspicious.pdf
```
- `-s JavaScript` — search objects whose type contains "JavaScript".
- `--raw` — print raw (undecoded) content of the matched objects.

### exiftool — metadata
- **Purpose:** read/write metadata (author, software, timestamps, GPS) across images, documents, and media.
- **Install:** `sudo apt install libimage-exiftool-perl`

```bash
exiftool sample.docx
```
- Add `-a` (show duplicate tags), `-G` (group tags by category), `-u` (show unknown tags) for a fuller view.

### binwalk — firmware / embedded file carving
- **Purpose:** find and extract files embedded inside binaries and firmware images.
- **Install:** `sudo apt install binwalk`

```bash
binwalk -e firmware.bin
```
- `-e` — extract known file types automatically (uses signatures).
- `-Me` — recursively scan and extract found files (matryoshka). Use with care on untrusted input.
- `--dd='.*'` — carve every signature match to disk.

### strings — printable text
```bash
strings -n 8 sample.bin | less
```
- `-n 8` — only show sequences of at least 8 printable characters (cuts noise).
- `-e l` — interpret 16-bit little-endian encoding (useful for Windows/UTF-16 strings).

### yara — signature matching
- **Purpose:** classify and match samples against detection rules.
- **Install:** `sudo apt install yara`

```bash
yara -r rules.yar ./samples/
```
- `-r` — recurse into the target directory.
- `-s` — print the matching strings and their offsets.
- `-w` — suppress warnings for noisy rule sets.
