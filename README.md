<div align="center">

```
 ██████  ███    ██  ██████ ███████     ██████  ██    ██ ████████ ████████ ███████ ███    ██ 
██    ██ ████   ██ ██      ██          ██   ██  ██  ██     ██       ██    ██      ████   ██ 
██    ██ ██ ██  ██ ██      █████       ██████    ████      ██       ██    █████   ██ ██  ██ 
██    ██ ██  ██ ██ ██      ██          ██   ██    ██       ██       ██    ██      ██  ██ ██ 
 ██████  ██   ████  ██████ ███████     ██████     ██       ██       ██    ███████ ██   ████ 
```

**Byte-Level File Security Scanner**

[![Version](https://img.shields.io/badge/version-1.0.1-CC1122?style=flat-square)](#changelog)
[![MIT License](https://img.shields.io/badge/license-MIT-CC1122?style=flat-square)](LICENSE)
[![Xinu Compliant](https://img.shields.io/badge/xinu-compliant-880011?style=flat-square)](https://consciousnode.github.io)
[![Zero Dependencies](https://img.shields.io/badge/dependencies-zero-222222?style=flat-square)](#)
[![Browser Native](https://img.shields.io/badge/runtime-browser_native-0a0a0a?style=flat-square)](#)
[![Single File](https://img.shields.io/badge/build-single_file-1a1a1a?style=flat-square)](#)
[![ConsciousNode SoftWorks](https://img.shields.io/badge/ConsciousNode-SoftWorks-CC1122?style=flat-square)](https://github.com/ConsciousNode)

[**→ Launch Once Bytten**](https://consciousnode.github.io/once-bytten)

</div>

---

## What it does

Once Bytten is a forensic-grade file security scanner that runs entirely in your browser. Drop any file and get a byte-level security analysis in seconds. No upload. No server. No dependencies. The file never leaves your device.

Named after the 1985 Jim Carrey vampire film. Because it bites first.

---

## Features

### 🩸 Magic Bytes Detection
- 40+ file format signatures with offset-aware matching
- Extension vs. actual type mismatch detection — the classic disguise technique
- Polyglot file detection (valid as multiple formats simultaneously)
- Windows LNK shortcut flagging (can execute arbitrary commands)

### 📊 Entropy Analysis
- Shannon entropy computed per 4KB chunk across the entire file
- Visual heatmap: structured → normal → compressed → encrypted
- Byte frequency distribution canvas
- High-entropy region counting — common indicator of packed/encrypted payloads

### 🔍 String Extraction
- Printable ASCII run extraction (minimum 6 characters)
- Pattern-matched categories: URLs, IP addresses, registry paths, Windows/Unix paths, dangerous tool names, code execution patterns, base64 blobs, email addresses
- Filterable by category with risk scoring

### 🏗️ Structure Parsing
- **PE (Windows EXE/DLL):** COFF header, sections with per-section entropy, W+X flag detection, timestamp anomaly detection, suspicious section flagging
- **ELF (Linux/Unix):** Bitness, endianness, type, machine architecture, entry point
- **ZIP Archives:** Full entry listing, executable-in-archive detection, encrypted entry flagging
- **PDF:** JavaScript detection, OpenAction, Additional Actions, embedded file detection
- **MS Compound (OLE):** VBA macro capability warning

### 🔬 ROSA Structural Analysis
ROSA (Rapid Online Suffix Automaton) builds a minimal deterministic finite automaton over sampled file regions. Unlike Shannon entropy (which only captures byte frequency), ROSA captures **sequential structure** — detecting hidden patterns in seemingly random data, identifying structural discontinuities consistent with spliced payloads, and measuring true structural compressibility.

- Suffix automaton built over 3 sampled regions (start/middle/end)
- State count, transition density, compression ratio per region
- Bigram entropy vs Shannon entropy comparison
- 256×256 bigram distribution heatmap (byte[n] → byte[n+1] frequencies)

### 🔢 Hex Viewer
- Paginated hex display, 16 bytes per row
- Hex pattern search with byte-level highlighting
- Offset tracking throughout

### 📋 Reporting
- Threat level badge: CLEAN / LOW / MEDIUM / HIGH / CRITICAL
- Key findings summary on the Overview tab
- Full JSON export of all scan data

---

## Threat Levels

| Level | Meaning |
|-------|---------|
| ✓ **CLEAN** | No suspicious indicators detected |
| △ **LOW** | Minor anomalies or informational flags |
| ⚠ **MEDIUM** | Suspicious patterns, type mismatches, or embedded executables |
| ✗ **HIGH** | Multiple high-risk indicators confirmed |
| ☠ **CRITICAL** | Confirmed malicious patterns or extreme structural anomalies |

---

## Usage

**Online:** [consciousnode.github.io/once-bytten](https://consciousnode.github.io/once-bytten)

**Offline:** Download `index.html` and open it in any modern browser. That's it. No build step, no install, no server.

```
Maximum file size: 200 MB
Supported formats: Any — unknown formats still receive entropy, string, and hex analysis
```

---

## Architecture

Once Bytten follows the **Xinu philosophy**: the browser is bare metal. One file, zero external dependencies, zero network calls, fully offline-capable. Everything runs in the tab.

```
Scan Pipeline
─────────────
INGEST    → FileReader API → ArrayBuffer
MAGIC     → Signature database scan (512 bytes)
ENTROPY   → Shannon entropy per 4KB chunk → canvas heatmap
STRINGS   → Printable ASCII extraction → pattern matching
ROSA      → Suffix automaton over 3×8KB samples → bigram map
STRUCTURE → Format-specific parsers (PE/ELF/ZIP/PDF/OLE)
HEX       → Paginated viewer with search
```

All phases run asynchronously with yielded timeouts to keep the UI responsive on large files.

---

## ROSA Analysis — Technical Note

The suffix automaton (SAM) is the minimal deterministic finite automaton that accepts all suffixes of an input string. For byte sequences, key properties:

- **Compression ratio** (input_length / state_count): near 1.0 = highly unique/random; >2.0 = significant repetition
- **Transition density** (transitions / states): high = structured data; low = sparse/random
- **Bigram/Shannon ratio**: near 1.0 = no sequential structure; <0.8 = strong byte-pair patterns

High Shannon entropy + low ROSA compression ratio = consistent with encrypted or truly random data.  
High Shannon entropy + high ROSA compression ratio = structured pattern disguised as noise (rare — flag for investigation).

---

## Changelog

See [CHANGELOG.md](CHANGELOG.md) for full version history.

**v1.0.1** — XSS hardening (file-derived content escaped at parse + render), PE parser bounds fixes, entropy canvas downsampling for large files.  
**v1.0.0** — Initial release.

---

## License

MIT — See [LICENSE](LICENSE)

---

<div align="center">

**ConsciousNode SoftWorks**  
*browser-native · zero-dependency · MIT · xinu-compliant*

[consciousnode.github.io](https://consciousnode.github.io) · [GitHub](https://github.com/ConsciousNode)

</div>
