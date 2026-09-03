# pc-capcombeatemupbundle-doc

Measurements of *Capcom Beat 'Em Up Bundle* (Capcom, 2018) as Steam installs
it: **268 files, 809,632,531 bytes**, read-only, no medium.

Seven CPS1/CPS1.5/CPS2 arcade games from 1989–1997, sold as one PC product.
Internal project `ibis`, module `BSAC`, engine MT Framework.

This repository publishes **measurements and the code that reproduces them**.
It contains no ROM bytes, no textures and no extracted game data — hashes yes,
bytes no.

## the short sheet

| | |
|---|---|
| object | `CBEUB` Steam install, app **885150**, depot **885151**, build **11421272** |
| files / bytes | 268 / **809,632,531** — matches Steam's own `SizeOnDisk` exactly |
| executable | `CBEUB.exe`, 6,305,176 bytes, PE32+, linked 2022-04-18, version 1.0.0.2 |
| **readable fraction of the executable** | **28.59 %** — `.text` is 4,502,528 bytes at entropy 8.0000 behind a Steam wrapper |
| containers derived here | `ARC\0` v7, `IBIS` v4, `TEX\0`, `GMD\0` |
| archives | 252, **residue 0 on 252 of 252**; 426 members, **426 of 426** inflate to declared length |
| resource type hashes | 13, **13 of 13 resolved** as `(~crc32(class name)) & 0x7FFFFFFF` |
| arcade ROMs | 14 containers, 214,565,760 bytes, four regions each, **residue 0 on 14 of 14** |
| distinct arcade programs | **11**, not 14 — three pairs are one program with a patched constant |
| **thesis figure** | **80.09 %** — the gallery's share of the install |
| absolute paths | **1**, over 28.59 % coverage |
| personal names found by scanner | 0 · **named in the product: more than a hundred** |
| cross-collection hash hits | 0 of 687, over 106 repositories |
| the question | **not determinable from the shipped files** — [12](docs/12-question.md) |

## the three findings

**1. Eighty per cent of this product is a picture gallery.** 648,440,086 bytes
of scanned arcade flyers, character sheets and painted key art, against
72,932,448 bytes — 9.01 % — for the seven games it exists to sell. Decoding the
`.tex` format was the price of knowing that, and this collection had never
opened one.

**2. Every secret the original hardware kept has been unwrapped before
shipping.** The CPS2 program cipher, the Kabuki sound-CPU cipher and the CPS
graphics interleave are the three things about these boards that cannot be
derived from the ROMs. All three have been applied offline: the CPS2 sets carry
ciphertext and plaintext side by side, the Q-Sound sets carry the encrypted Z80
at 0x00000 and its plaintext at 0x28000, and the graphics ship as flat 16×16
4bpp tiles.

**3. Which means the most discriminating test of authorship is not blocked —
it is designed out of the product.** The running program never needs a key or a
per-game table, so there is none to find, and there would be none even if the
packed `.text` were readable. That is the substance of
[12-question.md](docs/12-question.md), and the answer there is *not
determinable from the shipped files*, published with everything that was tried
and a list of what would close it.

## chapters

| | |
|---|---|
| [00 — predictions](docs/00-predictions.md) | written before anything was opened; 12 inherited clauses, 59 open |
| [01 — the object](docs/01-object.md) | what it is, the content definition, and the 80.09 % |
| [02 — datasheet](docs/02-datasheet.md) | every figure with the command that reproduces it |
| [03 — the executable](docs/03-executable.md) | PE, the wrapper, the version resource, the imports, the set-name table |
| [04 — the .arc container](docs/04-arc.md) | format, census, and thirteen type hashes resolved |
| [05 — the IBIS container](docs/05-ibis.md) | fourteen ROMs, and a header the pre-briefing got wrong |
| [06 — the four regions](docs/06-regions.md) | 68000, graphics, Z80, samples — identified from the bytes |
| [07 — ciphertext and plaintext](docs/07-halves.md) | the doubled CPS2 region, and the Kabuki copy |
| [08 — the byte that changes](docs/08-pairs.md) | eleven programs in fourteen containers |
| [09 — the .tex format](docs/09-textures.md) | derived, and the eighty per cent finally looked at |
| [10 — the credit roll](docs/10-credits.md) | a hundred names and six companies, all in pixels |
| [11 — eight languages](docs/11-languages.md) | the GMD table, and the missing language index 6 |
| [12 — whose emulator](docs/12-question.md) | **the chapter this session exists for** |
| [13 — provenance](docs/13-provenance.md) | the build id, found one directory up |
| [14 — leftovers](docs/14-leftovers.md) | 989 MB of it, on an object with no medium |
| [15 — corrections](docs/15-corrections.md) | twelve in the pre-briefing, four of mine |
| [16 — against the collection](docs/16-collection.md) | four Capcom containers in twenty-two years |
| [17 — scoring](docs/17-scoring.md) | 10.5/12 and 52.0/59, and a calibration that flipped sign |
| [18 — personal data](docs/18-personal-data.md) | the scanner's zero, and what it was a zero about |

## tools

Twelve new readers were written for this object, each with `--validate` before
anything else and a negative control that fires:

`mtarc.py` · `ibis.py` · `mttex.py` · `ddswrap.py` · `gmd.py` · `mttype.py` ·
`pe.py` · `m68k.py` · `z80head.py` · `romcensus.py` · `pairdiff.py` ·
`tilepeek.py`

`notes/` carries `sha1-all.txt` (268 files), `sha1-arc-members.txt` (426
members) and `sha1-rom-regions.txt` (56 regions) — the first hash lists this
object has ever had.

## the object was not modified

Nothing in this repository writes to `I:\SteamLibrary`. The game was never
launched by this session, no process was attached, no memory was dumped and the
Steam wrapper was not unpacked. Where a question required any of those, the
answer recorded is that the question is not answerable from the shipped files.
