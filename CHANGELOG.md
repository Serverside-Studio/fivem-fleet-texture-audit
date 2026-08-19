# Changelog

All notable changes to this project are recorded here.
Format follows [Keep a Changelog](https://keepachangelog.com/en/1.1.0/);
versioning follows [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [1.1.0] - 2026-08-19

### Added
- **Log mode** — `-LogPath <fxserver.log>` sizes a fleet from a console log
  alone. No CodeWalker, no file access, no install. The engine already prints
  `Asset x.ytd uses N MiB of physical memory` on every boot, so a log is enough
  to measure a server you only have the console for.
- Log mode reports total streamed memory, heaviest assets, a per-resource
  breakdown, and a concentration figure — whether the weight sits in a short
  list you can fix or is spread across the whole fleet.
- `-Json` now works in both modes.

### Fixed
- Matched only `MiB of physical memory`, missing `MiB of virtual memory`. Real
  servers emit both, often in the same log — one live sample carried 11,510
  physical and 8,950 virtual lines, so roughly 44% of the subject was invisible.
- **Removed 46 NUL bytes** from `Audit-FleetTextures.ps1`. A box-drawing section
  divider was stripped byte-wise when the file was made ASCII-only for the
  PowerShell 5.1 ANSI trap, leaving NULs behind. PowerShell parsed it fine
  because the damage sat inside a comment, which is why it shipped — but every
  tool that sniffs content treated the file as binary, so `grep`, GitHub diffs
  and code search all refused to display it.

### Note
Log mode deliberately does **not** report recoverable waste. That requires
reading texture headers; quoting a recoverable figure from a log would be a
guess presented as a measurement.

## [1.0.0] - 2026-08-19

First public release.

### Added
- `Audit-FleetTextures.ps1` — reads every `.ytd` under a path and reports texture
  count, memory cost, and how much of that cost is recoverable waste.
- Per-vehicle breakdown sorted by recoverable waste, so the worst offenders are
  named rather than buried in a total.
- `-Json` for machine-readable output, `-Limit` for a quick sample, `-Top` to
  control how many offenders are listed.
- `-CodeWalkerPath` so the tool is standalone — it loads CodeWalker.Core from
  wherever you keep it rather than assuming an install location.

### Notes
- Waste is computed as uncompressed to DXT5 only, never the better DXT1 case, so
  the reported figure is a floor rather than a best case.
- It deliberately does not report texture-pool headroom. The engine's ceiling is
  a count and whether it counts dictionaries or individual textures cannot be
  determined from the files alone.
- ASCII-only source. PowerShell 5.1 reads a BOM-less file as ANSI, so a single
  non-ASCII character inside a string is enough to stop the script parsing on
  another machine even though it runs fine on the author's.

[1.0.0]: https://github.com/bryansanchez379-ui/fivem-fleet-texture-audit/releases/tag/v1.0.0
