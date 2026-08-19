# fivem-fleet-texture-audit

Measure what a FiveM vehicle fleet actually **costs** in texture memory, and how
much of that cost is waste.

Every other fleet check answers *"will this crash?"* — duplicate spawn names,
modkit collisions, undefined vehicle layouts. None of them answer *"what is this
pack going to do to my server?"*, which is the question behind most texture-pool
trouble.

## What it found on a real server

203 vehicles from a live fleet:

```
24,835 textures
17,638 MB of texture memory
  86.9 MB per vehicle, on average
 6,556 MB recoverable — 37% of the total
   200 of 203 vehicles ship a 2K or larger texture
```

The worst offenders were not the ones with the most textures:

| Vehicle | Textures | Memory | Recoverable |
|---|---:|---:|---:|
| A | 91 | 178 MB | **128 MB** |
| B | 61 | 167 MB | 120 MB |
| C | 100 | 174 MB | 111 MB |

That first car carries 91 textures and 178 MB because they are stored
**uncompressed**. Compressed properly it is roughly 50 MB and looks identical in
game.

## Use it

Needs Windows PowerShell 5.1 and `CodeWalker.Core.dll` (plus `SharpDX.dll` and
`SharpDX.Mathematics.dll`) from a [CodeWalker](https://github.com/dexyfex/CodeWalker)
release. Those are not redistributed here — point `-CodeWalkerPath` at your own copy.

Loose `.ytd` files are not encrypted, so **no GTA5 keys are required**.

```powershell
.\Audit-FleetTextures.ps1 -Path "C:\server\resources\[cars]" -CodeWalkerPath "C:\CodeWalker"

# machine-readable, and a quick sample while you are trying it out
.\Audit-FleetTextures.ps1 -Path <dir> -CodeWalkerPath <dir> -Json audit.json -Top 25
.\Audit-FleetTextures.ps1 -Path <dir> -CodeWalkerPath <dir> -Limit 50
```

It reads `.ytd` files recursively, so point it at a whole `[cars]` category, a
single resource, or a pack you are about to install.

## How the numbers are worked out

A texture's memory cost is `width × height × bytes-per-pixel`, and the format
decides the last part:

| Format | Bytes per pixel |
|---|---|
| DXT1 / BC1 | 0.5 |
| DXT3, DXT5, BC*, ATI | 1 |
| A8R8G8B8 and anything else | **4** |

"Recoverable" assumes uncompressed → **DXT5** only, never the better DXT1 case,
so the figure is a floor. If you compress properly you beat it.

## What it deliberately does not tell you

**It does not report texture-pool headroom.** The engine's pool ceiling is a
count, and whether it counts texture dictionaries or individual textures is not
something this tool can determine from files alone. A percentage derived from an
unverified mechanic does not belong in a report someone acts on.

It also does not change anything. It reads and reports; recompressing is a
separate decision with separate tooling.

## Why bother

Texture bloat is invisible until it is not. It costs load time on every join, VRAM
on every client, and eventually it is the reason a server stops being joinable at
all — at which point the obvious suspect is "a bad car", and the actual cause is
the whole fleet being a third larger than it needs to be.

Uncompressed art is also the most common bloat in bought vehicle packs, because
nobody checks, and it is the cheapest thing in the world to fix.

## Changelog

See [CHANGELOG.md](CHANGELOG.md). Current release: **v1.0.0** — `.Audit-FleetTextures.ps1 -Version`.

## Licence

MIT. Built by [Sector 5 Development](https://github.com/bryansanchez379-ui) — we
build the software game communities run on.

Not affiliated with Rockstar Games, Cfx.re or the CodeWalker project.
