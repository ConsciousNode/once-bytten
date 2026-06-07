# Changelog — Once Bytten

All notable changes to Once Bytten are documented here.

---

## [1.0.1] — 2026-06-07

### Security

- **XSS hardening via centralized `esc()` utility.** The scanner was displaying file-derived content (PE section names, ZIP filenames, extracted strings, PDF version strings) directly into `innerHTML` without HTML entity escaping. A crafted file with `<script>` or other HTML in its metadata could inject code into the results UI. Added a single `esc()` function (escapes `&`, `<`, `>`) and applied it consistently at both parse time and render time.

  Affected surfaces:
  - PE section names (`parsePE`) → `esc(nameRaw)` at parse, `esc(sec.name)` at render
  - ZIP entry filenames (`parseZIP`) → `esc(fnameRaw)` at parse, `esc(e.name)` at render
  - Extracted strings (`renderStringTable`) → `esc(s.val)` at render
  - PDF version string (`renderStructure`) → `esc(p.version)` at render

### Fixed

- **PE parser bounds hardening.** Added early bounds check (`peOff + 4 > bytes.length`) before reading the PE signature, and a post-signature check (`peOff + 24 > bytes.length`) before reading COFF header fields. Prevents reads from truncated or malformed PE headers.

- **PE section table overflow protection.** Added `sectOff + (i+1)*40 > bytes.length` guard inside the section iteration loop, and a `rawOff + rawSize > bytes.length` check before computing per-section Shannon entropy. A crafted PE declaring a section `rawSize` beyond file bounds would previously pass garbage data to `shannonEntropy()`.

- **Entropy heatmap downsampling for large files.** Capped the entropy canvas at `MAX_BARS = 2000` columns. For a 200 MB file (~51,000 × 4KB chunks), the previous code would attempt to create a ~51,000-pixel wide canvas, which would degrade browser performance significantly on large files and fail silently on mobile. Chunks are now averaged into at most 2,000 display columns, preserving visual fidelity without the cost.

---

## [1.0.0] — 2026-06-07

### Initial release

- Magic bytes database: 40+ file format signatures, offset-aware matching, extension mismatch detection, polyglot detection, Windows LNK flagging
- Shannon entropy analysis: per-4KB chunk, canvas heatmap, byte frequency distribution, high-entropy region counting
- String extraction: printable ASCII runs ≥6 chars, 10 pattern categories (URL, IP, registry, Windows/Unix paths, dangerous tool names, code execution patterns, base64, email), category filtering, risk scoring
- ROSA structural analysis: suffix automaton over 3×8KB sampled regions, bigram entropy, 256×256 bigram distribution heatmap
- Structure parsers: PE (COFF header, sections with per-section entropy, W+X detection, timestamp anomaly), ELF, ZIP (entry listing, executable-in-archive detection, encrypted entry flagging), PDF (JS, OpenAction, AA, embedded file), MS OLE compound
- Hex viewer: paginated 16-bytes-per-row display, hex pattern search with highlighting
- Threat levels: CLEAN / LOW / MEDIUM / HIGH / CRITICAL with animated CRITICAL badge
- JSON report export
- Zero dependencies, single HTML file, browser-native, fully offline-capable
- Xinu-compliant
