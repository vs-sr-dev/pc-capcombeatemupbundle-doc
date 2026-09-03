# 04 — the .arc container: an arithmetic that closes, and a hash that resolves

*Measure: `python tools/mtarc.py --validate` over 252 archives, then
`--census`, then `--inflate-check` over all 426 members.*

## the format

Derived from the bytes. `tools/mtarc.py` implements it and runs `--validate`
before it will do anything else.

```
+0   4    'ARC\0'
+4   u16  version
+6   u16  entry count
then <count> entries of 80 bytes:
  +0   64   name, NUL-padded, backslash-separated, no extension
  +64  u32  resource type hash
  +68  u32  compressed size
  +72  u32  uncompressed size in the low 29 bits, flags in the top 3
  +76  u32  offset from the start of the file
```

**Negative control, which fires:**

```
$ python tools/mtarc.py --validate "$CB/CBEUB.exe" "$CB/.../Gothic_AM_NOMIP.tex"
NOT-AN-ARC  CBEUB.exe: magic is b'MZ\x90\x00', not b'ARC\x00'
NOT-AN-ARC  Gothic_AM_NOMIP.tex: magic is b'TEX\x00', not b'ARC\x00'
rc=1
```

**Census, which closes:**

```
archives            252
entries             426
versions            {7: 252}
residue==0          252 of 252
distinct types      13
compressed total    783241060
uncompressed total  1707290593
```

Every member is a raw zlib stream; `zlib.decompress` succeeds on **426 of 426**
and the inflated length equals the declared length on **426 of 426**. The
highest (offset + compressed size) equals the file length exactly on **252 of
252**: no slack, no trailer, no unclaimed bytes.

**Two corrections to the pre-briefing here.** It counted 251 archives, 425
members and 12 type hashes because it never opened `root.arc`; opening it
makes every denominator 252 / 426 / 13, and `root.arc` closes with residue 0
like the rest. And it reported the texture type's uncompressed total as
1,275,599,399, where the 29-bit mask gives **1,488,896,704** — confirmed by
actually inflating the members rather than trusting the field.

The 32,768-byte gap before the first member on single-entry archives is
**0x00 fill on 242 of 242** such archives. It is padding, not garbage.

## the type hashes, all thirteen

The pre-briefing said "nobody computed the hash function". It is four lines,
and it discriminates cleanly between candidates. `tools/mttype.py` pulls every
`r`-prefixed class name out of `.rdata` (91 of them) and tries a family of
CRC-32 variants against the 13 observed hashes:

```
crc32        maps  0 of 13
crc32 & 31   maps  0 of 13
jamcrc       maps  5 of 13
jamcrc & 31  maps 13 of 13     <- (~crc32(name)) & 0x7FFFFFFF
```

Thirteen of thirteen is not a coincidence a wrong function produces, and the
five-of-thirteen partial match from plain JAMCRC is what a *nearly* right
function looks like — the five whose top bit happens to be clear.

Negative control: three invented class names that are not in `.rdata`
(`rNotAThing`, `rZzzzzz`, `rQwertyuiop`) hash to values that collide with none
of the thirteen.

| type hash | class | entries | uncompressed |
|---|---|---:|---:|
| `0x241F5DEB` | **rTexture** | 342 | 1,488,896,704 |
| `0x21D3D8A7` | **rRom** | 14 | 214,565,760 |
| `0x22948394` | rGUI | 37 | 2,422,720 |
| `0x242BB29A` | rGUIMessage | 8 | 645,090 |
| `0x724DF879` | rSoundSourceMSADPCM | 14 | 488,096 |
| `0x2D462600` | rGUIFont | 4 | 108,846 |
| `0x02358E1A` | rShaderPackage | 1 | 152,392 |
| `0x1BCC4966` | rSoundRequest | 1 | 4,432 |
| `0x15D782FB` | rSoundBank | 1 | 3,143 |
| `0x4C0DB839` | rScheduler | 1 | 1,510 |
| `0x167DBBFF` | rSoundStreamRequest | 1 | 952 |
| `0x07F768AF` | rGUIIconInfo | 1 | 932 |
| `0x7808EA10` | rRenderTargetTexture | 1 | 16 |

**The type that holds the fourteen arcade programs is the type named `rRom`.**
That was the difference the pre-briefing named — between "the type that holds
the roms" and "the type named rRom holds the roms" — and it is now measured
rather than assumed. The engine's own extension string for the class is `bin`,
which sits beside `rRom` in `.rdata` and is what the loader's `bin\%s` format
string builds a path from.

## what the archives are

| group | archives | members | bytes on disk |
|---|---:|---:|---:|
| `g0NNN.arc` — gallery, one texture each | 228 | 228 | 648,440,086 |
| `game_NM.arc` — one ROM each | 14 | 14 | 72,932,448 |
| `game_bgNN.arc` — menu backgrounds | 7 | 35 | 37,366,155 |
| `game_win.arc` — the whole front end | 1 | 145 | 32,471,060 |
| `caution.arc` — the health-and-safety screen | 1 | 3 | 238,068 |
| `root.arc` — the shader package | 1 | 1 | 50,779 |

`root.arc` holds one member, `sc\DX11_64\root`, type `rShaderPackage`,
152,392 bytes uncompressed. **It is not a fifteenth ROM.**

Seven members of the 426 are byte-identical duplicates: two `dummy` textures in
`game_win.arc`, and one `onlineIcon` texture repeated in all seven background
archives. 419 distinct sha1s over 426 members; the list is
`notes/sha1-arc-members.txt`.

## the compression, quantified

```
on disk, all 252 archives     791,498,596
sum of compressed members     783,241,060
sum of uncompressed members  1,707,290,593
```

The difference the compressor makes is **924,049,533 bytes**, and the archive
headers and padding account for 791,498,596 − 783,241,060 = **8,257,536**
bytes, which is **exactly 252 × 32,768**: every archive, single-member or not,
reserves a 32,768-byte header block. The six group totals above sum to
791,498,596 with residue 0. Nobody had quantified any of it.
