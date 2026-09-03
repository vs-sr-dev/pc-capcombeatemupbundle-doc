# 03 — the executable: a question about code, behind a wrapper

*Measure: `python tools/pe.py --sections --imports --version
"$CB/CBEUB.exe"`. 6,305,176 bytes, of which 4,502,528 cannot be read.*

## the constraint, first

`.text` is 4,502,528 bytes at **entropy 8.0000 bits per byte** — the maximum a
byte stream can carry — with 0.39 % zeros. Code does not look like that.
Beside it sits a section named `.bind`, 206,616 bytes at 7.9554, which is the
section name Steam's DRM wrapper writes.

**71.41 % of this file is ciphertext, and this branch does not run programs,
attach debuggers or unwrap packers.** So every symbol search below is a search
of the remaining **28.59 %**, and that figure is repeated on every count rather
than mentioned once and forgotten. A report of "no MAME strings found" over
this file would be a measurement of a packer, not of a product.

```
name      vsize        raw size     file off     entropy   zeros
.text     0x0044B3FF   0x0044B400   0x00000400   8.0000    0.39 %
.rdata    0x000C9E4C   0x000CA000   0x0044B800   5.4985   39.52 %
.data     0x048B7630   0x0002B400   0x00515800   5.1879   42.39 %
.pdata    0x0002C6E8   0x0002C800   0x00540C00   6.2923   25.94 %
.tls      0x00000015   0x00000200   0x0056D400   0.0204   99.80 %
.gfids    0x000000C8   0x00000200   0x0056D600   1.9352   77.34 %
_RDATA    0x00001FE0   0x00002000   0x0056D800   6.1191   29.39 %
.rsrc     0x0005ED70   0x0005EE00   0x0056F800   7.9609    1.03 %
.bind     0x00032718   0x00032718   0x005CE600   7.9554    2.14 %
```

`.data` is worth a second look: its virtual size is 0x048B7630 — 76 MB — against
0x2B400 of raw bytes. The program reserves seventy-six megabytes of zero-filled
working memory at load, which is the right order of magnitude for fourteen ROM
images plus decode buffers, and is the only thing in the section table that
says anything about what the packed code does.

## the version resource, which nobody had read

`.rsrc` walks cleanly: 12 data entries, 8 icons, 2 icon groups, one manifest,
one `VS_VERSIONINFO`.

```
CompanyName       CAPCOM CO., LTD.
FileDescription   CAPCOM BEAT 'EM UP BUNDLE / CAPCOM BELT ACTION COLLECTION
FileVersion       1.0.0.2
InternalName      CBEUB.exe
LegalCopyright    CAPCOM CO., LTD.
OriginalFilename  CBEUB.exe
ProductName       CAPCOM BEAT 'EM UP BUNDLE / CAPCOM BELT ACTION COLLECTION
ProductVersion    1.0.0.2
resource language 1041 (Japanese)
```

**This is where the string "Capcom" comes from**, and it matters for
[16-collection.md](16-collection.md): the pre-briefing had noted that the
readable executable does not contain the word, and it was right about
`.rdata` and wrong about the file. It is in `.rsrc`, in the field whose whole
purpose is to name the publisher, and `pc-gamelist-doc`'s Studio column can
cite it.

The link timestamp is `0x625D05FB` = **2022-04-18 06:32:27 UTC**. The file's
mtime on this machine is 2023-07-31, which is when Steam wrote it, and the two
are different facts. Neither is the release date; the product shipped in 2018
and this is build 1.0.0.2.

The two icon groups are named **`CLASS_885150`** and **`CLASS_885151`**. Those
are the Steam app id and the depot id, embedded in the binary, and they let the
version in [13-provenance.md](13-provenance.md) be cross-checked against a
manifest that lives outside the folder.

## the imports, which are not behind the wrapper

The loader has to resolve imports before the wrapper runs, so the import table
is plaintext even though the code is not. 16 DLLs, 249 named imports:

```
KERNEL32.dll 133   USER32.dll 52   WS2_32.dll 21   steam_api64.dll 12
ole32.dll 6   WINMM.dll 4   IMM32.dll 4   ADVAPI32.dll 3   SHELL32.dll 3
IPHLPAPI.DLL 3   D3DCOMPILER_43.dll 2   OLEAUT32.dll 2   PSAPI.DLL 1
d3d11.dll 1   DINPUT8.dll 1   GDI32.dll 1
```

**Nothing here is exotic.** A C runtime, Win32, Direct3D 11, DirectInput 8,
Winsock, Steam. No unusual library, no dynamic-loading of a name that could be
read, nothing that names an emulation project or a JIT. That is a real
measurement and it is also a weak one: a 68000 interpreter needs nothing but
`malloc`, so a clean import table is exactly what both hypotheses predict.

## what `.rdata` does say

91 strings match the MT Framework resource-class shape `r[A-Za-z]...`,
including **`rRom`** and **`rRomInterMediate`**, sitting in the same table as
`rTexture`, `rGUI`, `rSoundBank`. A ROM is a first-class resource type in this
engine and Capcom named the class. Sixty of the other class names are
scrambled tokens (`rBTc`, `rH0z`, `rK261`); `rRom` is not.

Two findings the pre-briefing did not have:

**The fourteen set names are in the executable**, not only in the archive
member names. At `.rdata+0x3368`, immediately after the string `arc\game_win`
and immediately before `sAppGame`, fourteen NUL-terminated strings sit
back to back:

```
+0x3368  ffightj   +0x3370  ffight    +0x3378  kodj      +0x3380  kod
+0x3388  captcomj  +0x3390  captcomm  +0x33A8  knightsj  +0x33B8  knights
+0x33C0  wofj      +0x33C8  wof       +0x33CC  pgear     +0x33D4  armwar
+0x33E0  batcirj   +0x33E8  batcir
```

They are not evenly spaced, so this is not a data table — it is a run of
compiler-emitted string literals in source order. **The order is
Japanese-first within every pair**, and the pair `pgear` / `armwar` obeys it,
which confirms `pgear` is the Japanese member. See
[12-question.md](12-question.md), where this is the only naming evidence the
object contains and where it is weighed rather than asserted.

**`BoardId` is a false friend.** It appears at `.rdata+0x79780` and reads like
an arcade board identifier. Its neighbours are `SearchResultList`, `NumUsers`,
`NONCE`, `PORT`, `Rtt`, `nNetwork::TagChecker`. It is a leaderboard, in the
online-play code. Recorded here so the next reader does not spend an hour on
it.

Other readable strings worth having: `cps2` at `.rdata+0x105C0` followed by
`vid`; `bin` beside `rRom`, which is the resource extension the type hash is
computed from; `mCTREmulation`, `uChromaticAberrationFilter`,
`mNoisePowerCroma` — a CRT filter. **The only thing in this binary with
"Emulation" in its name emulates a television.**

## third-party attribution: the pre-briefing was wrong, and so was I

The pre-briefing recorded that the only licence credit anywhere is zlib 1.2.8,
and that there is "no in-binary credits list at all". Both statements are true
of the executable's strings and both are **false of the product**, because the
attributions are painted into a texture:

```
Networking provided by GGPO
DIGITAL HEARTS Co., Ltd.
Font Design by Fontworks Inc.
Special Thanks: Founder Electronics Corp., Ltd. & Founder International Inc.
Keywords STUDIOS
(c) HIROSHI MOTOMIYA (c) Thirdline (c) SHUEISHA (c) CAPCOM CO., LTD. 1992, 2018
```

See [10-credits.md](10-credits.md). *A text search cannot find a credit that was
drawn* — the same lesson the previous session learned about names, arriving
here as a lesson about licences.

## a false positive I generated myself

Searching the readable sections for `FBA` returned 8 hits. All eight are in the
**offset column of my own output** — `.rdata+0x03DFBA`, `.rsrc+0x011FBA` — and
none is in the data. The scan fired on its own formatting.

*A scan that fires is a scan to be read by hand*, and this one had to be read
by hand to discover that the tool was reading itself. The corrected count of
`FBA`, `MAME`, `FinalBurn`, `Musashi`, `Cyclone`, `A68K`, `kabuki`, `QSound`
and `romset` in the readable 28.59 % of this executable is **zero, all of
them** — and that is a measurement of coverage, not of absence.
