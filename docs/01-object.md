# 01 — the object: eighty per cent of it is a picture gallery

*Measure: 268 files, 809,632,531 bytes, counted with `python tools/hashall.py`
over the install root. No medium, no sectors, no image — the file bytes are the
only bytes there are.*

## what this is

`I:\SteamLibrary\steamapps\common\CBEUB` is a live Steam installation of
*Capcom Beat 'Em Up Bundle* (2018), read-only for the whole of this session.
Seven arcade games from 1989–1997 — CPS1, CPS1.5 and CPS2 — sold as one PC
product. Internal project name `ibis`, module `BSAC`, engine MT Framework.

It is the first object this collection has been pointed at that is not a dump.
There is no cue sheet to disagree with a track table, no ECC to account for, no
unclaimed sectors. That removes the three-denominator problem that decided half
of the previous session and replaces it with a single number:

```
python tools/hashall.py "I:/SteamLibrary/steamapps/common/CBEUB"
    files 268  bytes 809632531  distinct sha1 268  unreadable 0
```

That figure is confirmed from outside the folder. Steam's own manifest for app
885150 declares `SizeOnDisk 809632531`, byte for byte the same number
(see [13-provenance.md](13-provenance.md)).

## the definition this repository uses, chosen before any figure

This collection keeps a running table of what fraction of an object is *a
recording or a rendering of something that existed outside the program*. Every
previous entry was a game whose content was speech, music, video or rendered
imagery. **This is the first object whose content is another finished
product**, and the table has no rule for that. So the rule is fixed here,
before the arithmetic, and it does not change again:

> **A recording or rendering is a fixed reproduction of something that existed
> before the program and that the program only plays back. A program is not a
> recording of itself.**

Under that rule:

* the **gallery art** counts. 228 archives of scanned promotional artwork,
  concept sheets and character designs from the original arcade releases: paper
  that existed in 1989 and was photographed;
* the **fourteen arcade ROMs do not count.** They are executable programs. The
  product runs them; it does not play them back. An arcade ROM is the *thing*,
  not a recording of the thing — and calling it "content" would make the
  headline figure mean the opposite of what it means everywhere else in the
  table;
* the **menu backgrounds and front-end art** count as rendering, but they are
  reported separately because they are the product's own furniture rather than
  reproduced material.

The consequence is stated plainly: this is the choice that keeps the table
comparable, and it is also the choice that produces the *lower* of the two
available figures. The alternative — counting the ROMs as content — would give
89.10 %, which would put this object second in the whole table. It is not
adopted, and the number is printed here so nobody has to take that on trust.

## the accounting, which closes with residue zero

```
python tools/mtarc.py --census .../arc/*.arc .../sa/DX11_64/root.arc
```

| part | bytes on disk | share |
|---|---:|---:|
| gallery, 228 `g0NNN.arc` | 648,440,086 | **80.09 %** |
| the fourteen ROM archives | 72,932,448 | 9.01 % |
| menu backgrounds, 7 `game_bgNN.arc` | 37,366,155 | 4.62 % |
| `game_win.arc`, the whole front end | 32,471,060 | 4.01 % |
| `CBEUB.exe` | 6,305,176 | 0.78 % |
| everything else (8 `.tex`, 4 `.sngw`, `.mfx`, `.fsd`, `.dll`, `caution.arc`, `root.arc`) | 12,117,606 | 1.50 % |
| **total** | **809,632,531** | **100.00 %** |
| residue | **0** | |

**The thesis figure for this object is 80.09 %**, the gallery's share of the
install, and it needs no coverage caveat because it is measured over files, not
over the packed executable.

That places it fourth in the table, below 1995's 89.2297 % of recorded speech
and above 1999's 79.0009 %:

| year | object | figure |
|---|---|---|
| 1995 | (recorded speech) | 89.2297 % |
| 1994 | (recorded music) | 86.2465 % |
| 2000 | Dino Crisis (Dreamcast) | 84.7172 % |
| **2018** | **Capcom Beat 'Em Up Bundle** | **80.09 %** |
| 1999 | (rendered sound and film) | 79.0009 % |
| 1992 | Cruise for a Corpse | 0.4858–0.6083 % |

A new legend line is required and is added here: **for this row the
denominator is files on disk, and the excluded 9.01 % is executable code that
the product runs rather than plays.**

## what the install keeps, and what it loses

Against the original arcade boards, this installation **keeps**:

* the 68000 program of all seven games, byte-identical to a board dump in
  everything except one to two bytes on three of them
  (see [08-pairs.md](08-pairs.md));
* the Z80 sound program of all seven, with its build banner intact — `ffight`
  still announces ` version 2.00 `, `batcir` still announces
  ` 1.16b /CPS2      1996  /APRIL     `;
* the OKI and Q-Sound sample data;
* the graphics, though **not in the board's form** — see below.

It **loses**:

* the board's interleave. The graphics ROMs have been de-interleaved into flat
  16×16 4bpp tiles before shipping ([06-regions.md](06-regions.md));
* the CPS2 cipher. The two CPS2 titles ship the encrypted program *and* a
  decrypted copy side by side, so the cipher is never executed
  ([07-halves.md](07-halves.md));
* the Kabuki cipher on the Q-Sound sound CPU, the same way;
* the region jumper. On three of the seven games the Japanese and World
  versions differ by one or two bytes of program, patched rather than rebuilt.

**Everything the original hardware needed a secret to read has been unwrapped
before shipping.** That single sentence is the shape of this object, and
[12-question.md](12-question.md) is about what it does and does not imply.

## the eighty per cent

228 archives, one texture each, named `ui\3_image\gNN\g0NNN_BM_HQ_NOMIP`, in
eight groups for seven games. Decoded and rendered for the first time in this
collection ([09-textures.md](09-textures.md)), they are scanned flyers,
character design sheets, illustrated posters, sprite reference sheets and
painted key art. Four fifths of the weight of a product that sells seven games
is a museum of the paper that advertised them.

The ROMs — the reason the product exists — are 9.01 % of it.
