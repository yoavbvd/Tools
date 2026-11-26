# OLEdump (oledump.py)

- **Purpose:** analyze OLE2 structured-storage files — the container format behind legacy Microsoft Office files (`.doc`, `.xls`, `.ppt`) and many `.msg` emails — and extract embedded streams, especially **VBA macros**.
- **Author:** Didier Stevens (part of the Didier Stevens Suite).
- **Why it matters:** malicious Office documents hide auto-executing macros in OLE streams. OLEdump lists those streams and dumps the code for review without opening the document in Office.

## Install
```bash
# Option A: pip
pipx install oledump
# Option B: grab the script directly (Didier Stevens Suite)
wget https://raw.githubusercontent.com/DidierStevens/DidierStevensSuite/master/oledump.py
python3 oledump.py -h
```
> Run untrusted samples only inside an isolated analysis VM.

## Core workflow

### 1. List the streams
```bash
oledump.py suspicious.doc
```
Each line is one OLE stream. A letter **`M`** or **`m`** next to a stream marks a VBA macro:
- `M` — a macro **with** code (worth dumping).
- `m` — a macro stream with no real code (often empty module shells).
- `O` — stream contains an embedded OLE object.

### 2. Dump a specific macro stream
```bash
oledump.py -s 3 -v suspicious.doc
```
- `-s 3` — select stream number **3** (use `-s a` to select **all** streams).
- `-v` — decompress VBA; turn the stored, compressed macro back into readable source.

### 3. Follow obfuscation
```bash
oledump.py -s 3 -v --vbadecompresscorrupt suspicious.doc
```
- `--vbadecompresscorrupt` — attempt decompression even when the VBA container looks corrupt (a common anti-analysis trick).

## Useful flags

| Flag | What it does |
| --- | --- |
| `-s <n>` | Select stream number `<n>`; `-s a` selects all streams. |
| `-v` | Decompress VBA macro source into readable code. |
| `-i` | Print extra info per stream (size, name, indicators). |
| `-d` | Dump the **raw** stream bytes (for non-macro / binary streams). |
| `-x` | Dump and save selected stream(s) to disk. |
| `-S` | Output strings from the stream instead of raw bytes. |
| `-p <plugin>` | Run an analysis plugin, e.g. `plugin_http_heuristics` to surface URLs. |
| `--vbadecompresscorrupt` | Decompress even when the macro container is marked corrupt. |

## Typical triage sequence
```bash
# 1. See which streams hold macros
oledump.py sample.doc
# 2. Decompress every macro at once
oledump.py -s a -v sample.doc | tee macros.txt
# 3. Pull indicators (URLs, shell calls) with a plugin
oledump.py -p plugin_http_heuristics sample.doc
```
Then read `macros.txt` for auto-exec triggers (`AutoOpen`, `Document_Open`, `Workbook_Open`), shell/`WScript` calls, and decoded download URLs.

## Related tools
- **olevba** (oletools) — higher-level macro triage with a built-in IOC table. See [Analysis & Forensics overview](README.md).
- For modern (`.docx`/`.xlsm`) files, which are ZIP archives, also try `unzip -l file.docx` and inspect `word/vbaProject.bin` with oledump.
