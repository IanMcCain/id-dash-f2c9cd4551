# HANDOFF, build 235. STAGED AND VERIFIED, NOT SHIPPED

*08.06.2026. `~/Desktop/WORK/Personal/Output/index.html` is build 235, md5 `2c1f2cab6c9c8aa1b2f0230c7a2de03f`,
917,645 bytes. Live is still the corrupted build 233.*

## Severity, decided on evidence. THIS IS COSMETIC. Do not drive home for it.

live 233 and a clean build were run side by side on the real 08-04 export at 390x844:
zero JS errors in both, ONE state difference (a note edit made deliberately), identical card, button,
checkbox, input and link counts on all six tabs. No data is wrong, nothing is unclickable, nothing is lost.

One real consequence beyond ugly characters, worth knowing but still cosmetic. The rr-b69 icon system maps
emoji to line icons through `var EM={'HOUSE':'home', ...}` and `baseEmoji()` strips VS16 and ZWJ with
`t.replace(/[VS16 ZWJ]/g,'')`. Mangling turned that character class into six ordinary letters, one of which
also appears inside the mangled emoji, so the strip destroys the key and the lookup misses. The icon swap
therefore fails silently and the raw mojibake stays on screen. Measured on Real Estate: 0 SVG icons live
against 4 clean. That is why it looks worse than a few odd glyphs.

## What is in build 235

1. **Pure ASCII, and that is the durable fix.** All 101 non ASCII characters are now `\uXXXX` in script
   (94) and `&#xNNNN;` in markup (6), plus one em dash in an HTML comment swapped for a comma.
   PROVEN IMMUNE: byte identical after a MacRoman round trip, and after Latin-1 and cp1252. Build 234
   changes by 411 bytes under the same test. This failure class is now impossible, not merely unlikely.
2. **The encoding repair.** Rebuilt from the clean local copy, not by patching characters.
3. **Section headers no longer pile up.** Every `.sectlabel` was `position:sticky` at the same top sharing
   one containing block, so 2 to 3 stacked in a 32px band. Home overlapped at 29 of 36 scroll positions,
   Pickleball at 14. Now `position:static`, 0 overlaps on all six tabs.
4. **Spicer note contradiction fixed.** It read "take home is $2,906.39 and it is REALIZED" on a record
   carrying `actualNet: 2770`.

## Verification done

- build 235 vs build 234: identical state, identical rendered text on all six tabs except the build number
  itself, all four tab bar icons resolve to SVG in both, gear renders correctly in both, 0 JS errors.
- Six tab sweep: 0 overlap, 0 offscreen, 0 clipping, 0 mojibake.
- Three same day loads: zero state drift. Date rollover 08-06 to 08-07: only the date changed.
- All eight stuck changes present and correct.
- `grep -c 'content="v21 build' index.html` returns 1.

## TO SHIP IT

Drag `index.html` onto https://github.com/IanMcCain/id-dash-f2c9cd4551 , Add file, Upload files, main.

Then verify, and this is the step that was missing last time:
```bash
curl -s https://raw.githubusercontent.com/IanMcCain/id-dash-f2c9cd4551/main/index.html | md5
# must equal 2c1f2cab6c9c8aa1b2f0230c7a2de03f
```
A line count cannot see character corruption. Compare the hash.

## Why this session could not ship it, diagnosed properly

**`file_upload` is deterministically broken, not transient.** Five attempts across two sessions, and this
session retried on a NEW tab, a fresh page load and a fresh element ref. Every attempt returns the same
server side validation error: `paths` arrives as `undefined`. Other array taking tools in the same session
reach their servers fine, so this is that tool's binding, not arrays in general and not the path or the
folder grant.

**The clipboard is not the problem.** `pbcopy` was tested both under the shell's real locale
(`LC_CTYPE="C"`) and under UTF-8, and the clipboard md5 matched the file exactly both times. So the earlier
theory that pbcopy mangled it is WRONG and is recorded here so nobody chases it.

**The web editor paste does not work under automation, and it fails DANGEROUSLY.** `cmd+a` then `cmd+v`
left the document still showing build 233 at line 20 while enabling the Commit button, meaning the editor
reported itself dirty without having taken the new content. Committing there would have shipped a wrong
file. It was cancelled and `main` was confirmed unchanged (md5 `2d0f5794...`, 293 non ASCII, still 233).
Do not use this route.

## THE TOKEN DECISION HAS AGED BADLY, and Ian should decide, not us

The deploy token was declined on 07.28.2026 on the reasoning that the Chrome route was sufficient because
Chrome stays awake on the Mac. The Chrome route is now the single point of failure: it broke, and working
around it is what corrupted the app in the first place. Every fallback the protocol names is unavailable
(no clone, no `gh`, no token). So a verified fix now sits on disk with no way to reach production while
Ian is away from the Mac. That is the exact scenario the token would cover.

This is his call and no token was created or handled here. `_DEPLOY_PROTOCOL.md` section 2 should be
revisited with this evidence rather than left as a closed decision.
