# 00 — predictions: what I think is in there, written before I opened it

*Measure: none. This file is written before a single byte of the object is
read, and it is scored at the end against what was actually measured.*

The object is `I:\SteamLibrary\steamapps\common\CBEUB`, a live Steam install of
*Capcom Beat 'Em Up Bundle* (2018), 268 files, 809,632,531 bytes, read-only.

The session was commissioned to answer one question: **is this a native
reimplementation of seven arcade games, or an emulator running the original
ROMs — and if it is an emulator, whose?**

Rules for this file, unchanged from the previous forty-five sessions:

* every clause is `method` (a claim about how something is built or how it can
  be derived) or `content` (a claim about what a specific byte or count says);
* every clause is `inherited` (it appears in the pre-briefing in `_pre\`, so
  re-deriving it earns a point on a **separate** scale that means nothing) or
  `open` (nobody has measured it and the pre-briefing says so);
* **the two totals are never added together**;
* a clause that contains a numeric threshold I cannot name the mechanism for
  is a guess wearing a measurement's clothes, and this session's standing
  prescription is not to write one. Where I break that rule below I say so.

---

## §A — the pre-briefing, restated, worth zero points

Everything in this section came from `_pre\`. It is written down so that when a
later chapter re-derives it, nobody can pretend the session discovered it.

**A.1 — the object.** 268 files, 809,632,531 bytes, no medium: no sectors, no
ECC, no cue sheet, no unclaimed space. Seven extensions; five of them
(`.arc`, `.tex`, `.mfx`, `.sngw`, `.fsd`) are Capcom's MT Framework. 16 sha1s
published for the non-`.arc` files; the 251 `.arc` files are unhashed.

**A.2 — the executable.** `CBEUB.exe`, 6,305,176 bytes, sha1
`98f19e10cd33d01be0a000c1d837cc61afe77727`, PE64. `.text` is 4,502,528 bytes at
entropy 8.0000 — 71.41 % of the file — and there is a `.bind` section at
7.9554, which is the section name Steam's DRM wrapper writes. `.rdata` at
5.4985 holds everything readable: 92 `r*` resource class names including
`rRom` and `rRomInterMediate`, the `MtObject` / `MtHeapAllocator` family, the
loader format string `bin\%s`, the string `cps2`, the CRT-filter parameter
`mCTREmulation`, and one absolute build path,
`C:\capdev_ibis\BSAC\buildout\MasterReleaseDX11x64\output\XBSACMasterReleaseDX11.pdb`.
zlib 1.2.8 is credited in full and no other licence text appears.

**A.3 — the archives.** 252 `.arc`, `ARC\0` version 7 on 252 of 252, 80-byte
entries of (64-byte backslash-separated name without extension, u32 type hash,
u32 compressed size, u32 uncompressed size, u32 offset), members are raw zlib.
The last member ends exactly at end-of-file on 251 of 251. 425 entries, 12
distinct type hashes, none resolved to a name. 228 of the archives are one
gallery texture each.

**A.4 — the fourteen ROMs.** Resource type `0x21D3D8A7`, 72,473,696 bytes
compressed, 214,565,760 uncompressed, member names `bin\ffight` … `bin\batcirj`.
Container magic `IBIS`, version 4 on 14 of 14, header 0x40, a zero-terminated
list of (offset, size) pairs, and header + Σ regions = file length on 14 of 14.
The four-region size table, the fourteen decompressed sha1s, and the
region-by-region pair diffs (captcomm vs captcomj: 1 byte of 8,749,120, at file
offset 0x0000EAA, 0x02 against 0x00) are all in `_pre\rom.txt`.

**A.5 — what is already settled about the question.** It is emulation, not
reimplementation, and four independent measurements say so: the `rRom` resource
type, the four-region map at the boards' sizes, `ffight` region 2
disassembling as Z80 (`DI ; IM 1 ; JP 0x0067`) with a ` version 2.00 ` banner,
and `batcir` region 0 carrying byte-interleaved English text. **The session's
job is the second half of the question and only the second half.**

**A.6 — what is not evidence**, from `_pre\question.txt`: the four-region
layout, the region sizes, the presence of Z80 and 68000 code, and the absence
of `MAME` strings inside ciphertext. What would be evidence, in ascending
order: an arbitrary name only one project uses; an arbitrary layout decision
hardware does not force; **a data table that is not in the ROMs and is not
derivable from them**; verbatim text.

**A.7 — the accounting**, as the pre-briefing computed it once: gallery
648,440,086 (80.09 %), ROMs on disk 72,473,696 (8.95 %), `game_win.arc`
32,471,060 (4.01 %), seven backgrounds 37,366,155 (4.62 %), executable
6,305,176 (0.78 %), everything else 12,576,358 (1.55 %).

**A.8 — the scans already run.** `paths.py`: 6 raw, 6 distinct, five of them
fragments inside compressed texture data and one real. `contacts.py`: 5 raw,
5 noise, 0 published. `protscan.py`: 0 hits on every marker, 2 on the positive
control — a non-measurement.

**A.9 — the neighbours.** `pc-residentevil-doc` (1996), `pc-residentevil2-doc`
(1998), `dc-dinocrisis-doc` (2000), all Capcom, all with an offset table whose
arithmetic closes exactly. The collection denominator for `crossall.py` is
given as 102 repositories, 73 with hash lists.

---

## §B — inherited clauses: re-derived from zero, scored separately

These are things `_pre\` already states. I re-derive each one with my own tool
and my own command. Getting them right is worth nothing except confidence in
the tooling; getting one **wrong** would mean the pre-briefing is wrong, which
is the interesting outcome.

| id | kind | clause |
|---|---|---|
| **I01** | method | `ARC\0` with u16 version 7 and u16 count, then 80-byte entries in the field order given in A.3, validates on 252 of 252 archives, and a `--validate` run on a non-ARC file (`CBEUB.exe`) fails loudly. |
| **I02** | method | On the 251 archives in `arc\`, the highest (offset + compressed size) equals the file length exactly. Residue 0 on 251 of 251. |
| **I03** | method | `IBIS` version 4, header 0x40, zero-terminated (offset, size) pairs; 0x40 + Σ sizes = length on 14 of 14. |
| **I04** | content | The fourteen decompressed sha1s in `_pre\rom.txt` reproduce exactly, all fourteen. |
| **I05** | content | The four-region size table reproduces exactly on 14 of 14, including 0x800000 for region 0 on the two CPS2 titles and 0x400000 on the five CPS1 ones. |
| **I06** | content | `captcomm` and `captcomj` differ in exactly one byte; `kod`/`kodj` in exactly two; the other five pairs differ in six figures. |
| **I07** | content | The install accounting closes to 809,632,531 with residue exactly 0, and the gallery share is 80.09 % of it. |
| **I08** | method | `CBEUB.exe` `.text` measures entropy 8.0000 to four decimals over 4,502,528 bytes, and a `.bind` section is present. |
| **I09** | content | `rRom` and `rRomInterMediate` are both present in `.rdata` as null-terminated strings, in the same table as `rTexture` and `rModel`. |
| **I10** | content | `paths.py` re-run gives 6 raw and exactly one that survives reading by hand. |
| **I11** | method | Every one of the 425 archive members is a raw zlib stream that inflates to exactly its declared uncompressed length. 425 of 425, residue 0. |
| **I12** | content | The seven menu fonts `menu_jpn menu_eng menu_fre menu_ita menu_ger menu_spa menu_chT` are all present in `game_win.arc`, seven of seven. |

**Predicted inherited score: 11.0 / 12.** I expect one of these twelve to come
back different, because the last ten pre-briefings carried 6 to 18 errors each
and this one was written in one pass. I do not know which one; if I did it
would not be a prediction. My weakest is **I07**, because a percentage computed
once is the exact shape of the error this branch keeps finding.

---

## §C — open clauses: the actual work

### C.1 — the four regions (the pre-briefing has looked at one of four)

| id | kind | clause |
|---|---|---|
| **C01** | method | Region 0 is the 68000 program ROM on 14 of 14, and I can show it: the first eight bytes are a 68000 reset vector — a longword initial stack pointer followed by a longword initial PC — and both land inside sane ranges for the board rather than looking like noise. |
| **C02** | method | Region 0 is stored byte-interleaved (the even/odd split of two 8-bit chips onto a 16-bit bus) and de-interleaving it with a four-line tool turns the scrambled ASCII in `_pre\rom.txt` into readable English on the sets that have English. |
| **C03** | content | De-interleaved region 0 yields readable in-game text — score labels, stage names, continue/insert-coin strings — on at least ten of the fourteen sets. |
| **C04** | method | Region 1 is graphics: no 68000 vector table, no Z80 entry sequence, no readable ASCII, and an entropy and zero-fraction profile distinct from both region 0 and region 3. I will state what it is by what it is not, plus a positive tile-structure test. |
| **C05** | method | Region 2 is a Z80 program on 14 of 14, not just on `ffight` — the same `F3` (DI) opening or an equivalent Z80 entry sequence, verified by disassembling the first instructions of all fourteen. |
| **C06** | content | Every one of the fourteen region 2s carries an ASCII build banner in the shape of ` version 2.00 ` or ` 1.16b /CPS2 1996 /APRIL `, and I can print all fourteen. |
| **C07** | method | Region 3 is sample data: high entropy, low zero-fraction, no code, and on the CPS1 sets it carries an OKI-style structure at its start (a table of sample start/end pointers) that I can show is a pointer table because the values ascend and stay inside the region. |
| **C08** | content | The four regions in order are program, graphics, sound-CPU program, samples — that is, the container's region order matches the board's functional order rather than being sorted by size. |

### C.2 — the CPS2 doubled program region (the best thread in the object)

| id | kind | clause |
|---|---|---|
| **C09** | content | On `armwar`, `pgear`, `batcir`, `batcirj` the 0x800000 region 0 is two 0x400000 halves that are **not** copies of each other. |
| **C10** | content | The two halves have visibly different entropy, and the difference is large enough to be structural rather than sampling noise. |
| **C11** | content | Exactly one of the two halves contains a 68000 reset vector and readable de-interleaved text; the other does not. |
| **C12** | content | The half that reads as code is the **second** half, and the first half is the encrypted image as it sits on the board — i.e. the container ships ciphertext and plaintext side by side. |
| **C13** | method | I will state explicitly what C09–C12, if confirmed, do and do not prove: that shipping a decrypted image is a *software* decision, that it means the product does not implement the CPS2 cipher at run time, and that it does **not** by itself name whose emulator this is. |
| **C14** | content | The same doubling is absent on all five CPS1 sets, 5 of 5 — region 0 there is 0x400000 with no plaintext/ciphertext pairing. |

### C.3 — the byte that changes, and the four games where it doesn't

| id | kind | clause |
|---|---|---|
| **C15** | content | The single differing byte between `captcomm` and `captcomj` sits inside region 0 (file offset 0x0000EAA is region 0 offset 0x0000E6A), not in the container header. |
| **C16** | content | It is a region/territory selector: the same byte position in the other sets takes a small set of low values, and the value differs consistently between the `-j` member and the non-`-j` member of a pair on the three pairs that differ by ≤2 bytes. |
| **C17** | method | I can locate the byte's position **in 68000 address space** after de-interleaving and say which half (even chip or odd chip) it lives on. |
| **C18** | content | The four pairs that differ by six figures differ because they are genuinely different program revisions, not patched copies — and I will show it by finding two *different* build banners or two different date strings in the two members of at least one such pair. |
| **C19** | content | Therefore the product ships fewer than fourteen distinct programs: at least three pairs are one program plus a byte patch, so the honest count of distinct arcade programs is 11, not 14. |
| **C20** | method | The regions that are identical between the members of a pair are identical *byte for byte*, and I will say so by diffing rather than by hashing — the standing lesson from the previous session. |

### C.4 — the images, which are 80 % of the product and have never been opened

| id | kind | clause |
|---|---|---|
| **C21** | method | The `.tex` format is derivable from the bytes alone: a four-byte magic, a packed header carrying width, height, mip count and a format code, and a payload that is block-compressed (DXT/BC family) rather than raw pixels. |
| **C22** | content | The magic is `TEX\0` or `TEX` plus a version byte, on 342 of 342 gallery textures plus the 8 loose `.tex` files. |
| **C23** | method | The payload size for at least one texture equals exactly the block-compressed size predicted by width, height and the format code — a quantity encoded twice, in two different ways, that agrees. That is the test that turns "an arithmetic that closes" into "a structure demonstrated". |
| **C24** | content | I decode and render at least ten gallery textures, and they are scanned promotional art — flyers, cabinet art, character illustrations, design documents — from the original arcade releases, i.e. a museum of 1989–1997 paper. |
| **C25** | content | The credit screen `ui\1_menu\18_credit\credit` is a GUI *layout*, and the credit text is not in it: the names, if any, live in a message table or are painted into a texture. |
| **C26** | content | Decoding the credit resource yields **no personal names** — this is a 2018 corporate re-release and the credits will be company names and legal notices. I record this as a prediction precisely because the previous session's scanner returned zero on a disc naming 110 people. |
| **C27** | method | At least one of the seven menu fonts decodes to a glyph atlas I can render, and the Italian one is the same atlas shape as the English one with a different glyph set — i.e. localisation here is a font plus a message table, not a repaint. |
| **C28** | content | Neither the gallery, nor the credit screen, nor any font, nor any message table names MAME, FinalBurn, or any emulator project. |

### C.5 — the type hashes, the PE, and the parts nobody read

| id | kind | clause |
|---|---|---|
| **C29** | method | The 12 archive type hashes are a CRC-family hash of the resource's **extension string**, and I can derive the exact function by finding one that maps a known extension to a known hash, then verifying it on a second and third. |
| **C30** | content | `0x241F5DEB` is the hash of `tex`, and `0x21D3D8A7` is the hash of `rom` — so the type that holds the fourteen arcade ROMs is literally the type named `rom`, and the engine class is `rRom`. |
| **C31** | content | At least 8 of the 12 type hashes resolve to a plausible three-or-four-letter extension by the derived function, and I publish the residue honestly for the ones that do not. |
| **C32** | method | The PE `.rsrc` walks cleanly with a reader I write, and carries a `VS_VERSIONINFO` block. |
| **C33** | content | That version block names Capcom as `CompanyName` — and it is the only place in the shipped files where the string "Capcom" appears in a legally-meant form, which is where `pc-gamelist-doc`'s Studio field must come from. |
| **C34** | content | The version block carries a product/file version number that is a **build stamp**, giving this object something closer to a version declaration than "undeclared". |
| **C35** | method | The import table is readable (it is not behind the packer, because the loader must resolve it) and I can list every imported DLL and every named import. |
| **C36** | content | The imports are ordinary: a C runtime, `kernel32`/`user32`, Direct3D 11 or DXGI, an input API, and `steam_api64.dll`. Nothing exotic, nothing that names an emulation library, nothing dynamically loaded by a name I can read. |
| **C37** | content | The `.pdata` exception table gives a function count for the packed program, and that count is a real measurement of program size that survives the wrapper. |
| **C38** | content | `root.arc` in `sa\DX11_64` opens with the same reader and is a shader or system archive, not a fifteenth ROM. |

### C.6 — the question itself

| id | kind | clause |
|---|---|---|
| **C39** | content | **No per-game configuration table — graphics-bank mappings, register layouts, the CPS1 "B-board" style tables — exists in any readable shipped file.** The tables, if the product has them, are inside the 71.41 % that is ciphertext. |
| **C40** | content | Nothing in any readable byte of any of the 268 files names MAME, FinalBurn, FBA, or any other emulator project — and I will report that as a measurement of coverage, not of absence, stating the fraction of the executable it could not see. |
| **C41** | method | The fourteen set names test is answerable from inside the object: I can say how many of the fourteen are forced by the games' titles and how many are arbitrary choices, and put a number on "would an independent namer have landed on all fourteen". |
| **C42** | content | At least one of the fourteen names is **not** a plausible independent abbreviation of its title — `pgear` for *Powered Gear* and `captcomj` for the Japanese *Captain Commando* are the candidates — and that is the strongest naming evidence the object contains. |
| **C43** | content | The naming convention is internally inconsistent in a way that is itself evidence: some pairs use a bare `-j` suffix on a shared stem, and at least one pair uses two entirely unrelated stems (`armwar` / `pgear`), which is what a *list inherited from elsewhere* looks like rather than a scheme designed once. |
| **C44** | method | **The session's answer to "whose emulator" is "not determinable from the shipped files."** I predict this outcome now, before measuring, so that it cannot be claimed as a discovery later, and so that if the measurements *do* determine it the prediction is visibly wrong. |
| **C45** | method | The chapter that says so will list, explicitly, what would close the question: the contents of `.text`, a run-time memory image, a per-game table, or a verbatim string — and will say that this branch's rules forbid three of those four. |
| **C46** | content | No accusation of licence infringement is published, because circumstantial evidence does not support one either way. |

### C.7 — accounting, leftovers, and the collection

| id | kind | clause |
|---|---|---|
| **C47** | content | The 32,768-byte header padding on the single-entry archives is zero fill, not garbage, on all of the single-entry archives. |
| **C48** | content | Region 0 is allocated larger than the program it holds on most of the fourteen sets, and the unused tail is constant fill (0x00 or 0xFF) — measurable as "allocated but not used" bytes, on 14 of 14. |
| **C49** | content | The total unpacked size of all 425 members exceeds 1.4 GB, against 809,632,531 on disk, and I publish the exact difference as the compressor's contribution. |
| **C50** | content | There is not one text file in the whole install: zero `.txt`, zero readme, zero licence file, zero third-party notice, on 268 of 268 files. |
| **C51** | method | `crossall.py` returns zero shared hashes against the rest of the collection, and I publish the denominators (repositories in the root, repositories with hash lists) **before** the result. |
| **C52** | content | The denominators in `_pre\crossdisc.txt` (102 and 73) are themselves wrong, because the count changes the moment this repository exists. |
| **C53** | content | `psblocks.py` and `timtmd.py` return zero on 268 of 268 files, and I run them rather than skipping them. |
| **C54** | method | The Capcom container through-line holds on a fourth product: `.arc` and `IBIS` both close with residue 0, joining 1996, 1998 and 2000, and I take the neighbours' figures from their `docs\` rather than from memory. |
| **C55** | content | I do not create a `pc-platformnotes-doc`. This object is an arcade-emulation object wearing a PC, and a platform checklist derived from it would describe one product. |

### C.8 — the thesis figure

| id | kind | clause |
|---|---|---|
| **C56** | method | I choose the thesis definition in `docs\01` before computing any figure, and I state that the definition must decide a case the table has never faced: **an object whose content is another finished product**. |
| **C57** | content | The definition I choose counts the gallery as "a rendering of something" and the arcade ROMs as **not** — because the ROMs are the product, not a recording of it — and the figure lands at the gallery's share of the install. |
| **C58** | content | That places this object in the upper half of the thesis table but below the 1995 record of 89.2297 %. |
| **C59** | method | Every figure in every chapter names its denominator **and its coverage**, and the coverage line for anything touching the executable is 28.59 %. |

---

## §D — the count, and the two predicted totals

Counted with a command before this line was written:

```
grep -cE '^\| \*\*C[0-9]+\*\*' docs/00-predictions.md   ->  59
grep -cE '^\| \*\*I[0-9]+\*\*' docs/00-predictions.md   ->  12
```

**Inherited: 12 clauses. Predicted 11.0.**

**Open: 59 clauses. Predicted 38.0.**

The calibration record for this branch is +10.5, +7.5, +5.0, +2.0 — four
over-estimates, same sign, magnitude halving each time. The straight-line
reading says predict about one point under instinct. My instinct says 39.5, so
I write **38.0**, and I expect to land within a point either way for the first
time.

Where I expect to lose points: **C.4**, because a texture format this
collection has never opened is exactly the kind of thing that eats an afternoon
and yields a header and no pixels; **C12**, because "second half is the
plaintext" is a fifty-fifty guess about ordering dressed as a finding; and
**C31**, which contains the only numeric threshold in this file that I cannot
name a mechanism for — I am flagging it here rather than pretending otherwise.

Where I expect to be right and it will not feel like much: **C.1**, because
region identification from vector tables and entropy is mechanical once the
reader exists.

**And C44 is the clause that matters.** I am predicting, in writing, before
opening anything, that the question this session was commissioned to answer
cannot be answered from the shipped files. If that is right it is not a
triumph; it is the honest shape of a 71 %-encrypted binary. If it is wrong, the
measurement that makes it wrong is the best thing this session will produce.
