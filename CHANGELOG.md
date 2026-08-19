# Changelog

All notable changes to this project are recorded here.
Format follows [Keep a Changelog](https://keepachangelog.com/en/1.1.0/);
versioning follows [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

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
