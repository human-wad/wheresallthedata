# doom/ — the door behind the question

Clicking "Where's all the data?" lands here. This directory is a complete,
self-contained static build of Doom. No server logic, no build step on
deploy: copy the directory as-is.

## Contents

| file | what it is |
|---|---|
| `index.html` | the page: styling, boot logic, GPL source links |
| `websockets-doom.js` / `websockets-doom.wasm` | Chocolate Doom, ported to WASM by Cloudflare's doom-wasm, built from source |
| `doom1.wad` | the IWAD — DOOM shareware v1.9 (freely redistributable) |
| `default.cfg` | engine defaults (key bindings etc.) |

Only this page's visitors download the WAD. Nothing here is loaded by the
landing page.

## Hosting requirements

Smaller than the old stub assumed:

- **`.wasm` served as `application/wasm`** — every mainstream static host
  (GitHub Pages, Cloudflare Pages, Netlify, nginx, `python -m http.server`)
  already does this. It matters only for streaming compilation; a wrong MIME
  still runs, with a console warning.
- **No COOP/COEP headers needed.** The port is single-threaded
  (`USE_PTHREADS=0`), so no cross-origin isolation. The stub comment's worry
  about GitHub Pages not being able to set those headers is moot.
- ~6.5 MB total across the five files (WAD 4.0 MB, wasm 2.3 MB); both cache
  well (send long cache headers if you can).

## Provenance

- **Engine**: [Chocolate Doom](https://github.com/chocolate-doom/chocolate-doom)
  (GPL v2) via Cloudflare's
  [doom-wasm](https://github.com/cloudflare/doom-wasm) fork,
  commit `65e0d3ae2ffa604155eebd96ed40da6567bd08f4`, built with
  Emscripten 6.0.12 on 2026-10-22.
- **Patches applied to the fork for current toolchains** (all recorded in
  the comment atop `index.html`):
  1. `EXTRA_EXPORTED_RUNTIME_METHODS` → `EXPORTED_RUNTIME_METHODS`
     (flag was removed from Emscripten),
  2. dropped `LEGACY_GL_EMULATION` / `PROXY_POSIX_SOCKETS` (same),
  3. `src/doomtype.h`: guard the `boolean` enum for C23 (`true`/`false`
     became keywords; autoconf now defaults to `-std=gnu23`),
  4. `src/doom/r_defs.h`: `spriteframe_t.rotate` changed `boolean` →
     `short`. The field doubles as a -1 sentinel after
     `memset(sprtemp, -1, ...)`; a 1-byte bool makes the 0xFF sentinel
     indeterminate under -O3 with current clang, and the engine died on the
     first sprite. Same change as upstream Chocolate Doom.
  5. debug info dropped (`-g` / `-gsource-map`) so the .wasm ships lean
     (7.7 MB -> 2.3 MB) and binaryen optimizes fully.
- **IWAD**: DOOM shareware v1.9 `doom1.wad`, retrieved from
  [Software Heritage](https://archive.softwareheritage.org/) content-addressed
  by SHA-1 and verified against the checksums published at
  [wad-archive.com](https://www.wad-archive.com/wad/5b2e249b9c5133ec987b3ea77596381dc0d6bc1d):
  - md5 `f0cefca49926d00903cf57551d901abe`
  - sha1 `5b2e249b9c5133ec987b3ea77596381dc0d6bc1d`
  - sha256 `1d7d43be501e67d927e415e0b8f3e29c3bf33075e859721816f652a526cac771`
  id Software has always permitted redistribution of the shareware WAD.

GPL v2 means anyone receiving the binaries must be offered the engine's
source — the page footer links both upstream repos, which satisfies that.

## Rebuilding from source

```sh
git clone https://github.com/cloudflare/doom-wasm && cd doom-wasm
git checkout 65e0d3ae2ffa604155eebd96ed40da6567bd08f4
# apply the five patches above (see index.html comment for exact edits)
./scripts/build.sh            # needs Emscripten + autotools on PATH
cp src/websockets-doom.{js,wasm} src/default.cfg doom/
# plus a doom1.wad whose sha1 is 5b2e249b9c5133ec987b3ea77596381dc0d6bc1d
```
