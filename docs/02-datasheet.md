# 02 — datasheet: every figure with the command that reproduces it

*Measure: this chapter is the index of measurements. Every row carries the
command that regenerates it from the read-only install.*

Throughout, `$CB` is `I:\SteamLibrary\steamapps\common\CBEUB` and `$ARC` is
`$CB\nativeDX11x64\arc`. Nothing in this repository writes to either.

## the install

| fact | value | command |
|---|---|---|
| files | 268 | `python tools/hashall.py $CB` |
| bytes | 809,632,531 | same |
| distinct sha1 | 268 | same |
| unreadable files | 0 | same |
| extensions | 7 (`arc` 252, `tex` 8, `sngw` 4, `mfx` 1, `fsd` 1, `exe` 1, `dll` 1) | `find $CB -type f \| sed 's/.*\.//' \| sort \| uniq -c` |
| text files of any kind | **0** | same |
| drive-lettered paths | 6 raw, **1 real** | `python tools/paths.py $CB` |
| personal-data scanner hits | 5 raw, 5 noise, 0 published | `python tools/contacts.py $CB` |
| copy-protection markers | 0, positive control fires twice | `python tools/protscan.py $CB` |
| PlayStation TIM/TMD blocks | 0 | `python tools/psblocks.py $CB` |
| cross-collection hash hits | 0 of 687, over 106 repositories | `python tools/crossall.py` |

## Engine

MT Framework, named by its own class strings in the unpacked part of the
executable. `python tools/pe.py --strings --section .rdata $CB/CBEUB.exe`

| fact | value |
|---|---|
| allocator family | `MtObject`, `MtDefaultAllocator`, `MtHeapAllocator`, `MtBlockAllocator`, `MtVirtualAllocator` |
| resource class names in `.rdata` | 91 matching `r[A-Za-z][A-Za-z0-9_]*` |
| resource type hash function | `(~crc32(class_name)) & 0x7FFFFFFF` — **derived**, see [04-arc.md](04-arc.md) |
| type hashes present in the archives | 13, **13 of 13 resolved** |
| the ROM resource class | **`rRom`**, hash `0x21D3D8A7`, extension `bin` |
| the loader's path format | `bin\%s` |
| build path (the only absolute path) | `C:\capdev_ibis\BSAC\buildout\MasterReleaseDX11x64\output\XBSACMasterReleaseDX11.pdb` |
| container formats derived here | `ARC\0` v7, `IBIS` v4, `TEX\0`, `GMD\0`, `GFD\0` (magic only) |

`BSAC` is not expanded by measurement. The executable's version resource says
`CAPCOM BEAT 'EM UP BUNDLE / CAPCOM BELT ACTION COLLECTION`, which makes an
expansion tempting; **an expansion of an acronym is not a measurement** and
the letters do not line up (`BSAC` has an S that `BELT ACTION COLLECTION` does
not). Recorded as unexplained.

## the executable

`python tools/pe.py --sections $CB/CBEUB.exe`

| fact | value |
|---|---|
| size / sha1 | 6,305,176 / `98f19e10cd33d01be0a000c1d837cc61afe77727` |
| format | PE32+, machine 0x8664, image base 0x140000000, 9 sections |
| link timestamp | `0x625D05FB` = **2022-04-18 06:32:27 UTC** |
| `.text` | 4,502,528 raw bytes, **entropy 8.0000**, 0.39 % zeros |
| `.bind` | 206,616 bytes, entropy 7.9554 — the Steam DRM wrapper's section name |
| `.rdata` | 827,392 bytes, entropy 5.4985 — everything readable is here |
| **readable fraction of the file** | **28.59 %** |
| version resource | CompanyName **CAPCOM CO., LTD.**, FileVersion **1.0.0.2**, language 1041 (Japanese) |
| resource entries | 12: 8 icons, 2 icon groups named `CLASS_885150` / `CLASS_885151`, 1 version, 1 manifest |
| imports | **16 DLLs, 249 named imports**, nothing exotic |

The icon group names `CLASS_885150` and `CLASS_885151` are the Steam app id and
depot id, which is how the version in [13-provenance.md](13-provenance.md)
cross-checks.

## the archives

`python tools/mtarc.py --validate $ARC/*.arc` then `--census`

| fact | value |
|---|---|
| archives | **252** (251 in `arc\`, plus `root.arc`) |
| header | `ARC\0`, u16 version = **7 on 252 of 252**, u16 entry count |
| entry | 80 bytes: 64-byte name, u32 type hash, u32 compressed, u32 uncompressed-and-flags, u32 offset |
| uncompressed size field | low **29** bits; top 3 bits are flags |
| members | **426** |
| last member ends at EOF | **252 of 252**, residue 0 |
| members inflating to declared length | **426 of 426** |
| distinct type hashes | 13 |
| compressed total | 783,241,060 |
| uncompressed total | **1,707,290,593** |
| header padding on single-entry archives | 0x00 fill, **242 of 242** |
| duplicate members | 7 (one `dummy` pair, one icon in all 7 background archives) |

## the fourteen ROMs

`python tools/ibis.py --validate --map _work/rom/bin/*`

| fact | value |
|---|---|
| container | `IBIS`, u32 version = **4 on 14 of 14** |
| layout | pairs of (u32 offset, u32 size) from +8, zero-terminated |
| first region offset | 0x40 on 14 of 14 |
| accounting | regions tile the file contiguously, last ends at EOF, **residue 0 on 14 of 14** |
| regions per file | 4, always |
| compressed / uncompressed | 72,473,696 / **214,565,760** |
| region 0 identified as | 68000 program — legal reset vector on 10 of 10 CPS1 sets directly, and in the second half on 4 of 4 CPS2 sets |
| region 1 identified as | graphics, flat 16×16 4bpp tiles |
| region 2 identified as | Z80 program — `DI ; IM 1` on 14 of 14 |
| region 3 identified as | samples; OKI pointer table validates on 4 of 4 CPS1-OKI sets |
| region 0 allocated but unused | 32,449,970 of 75,497,472 = **42.98 %** |

## coverage, which has to be stated on every count

**71.41 % of the executable is at entropy 8.0000 and cannot be read.** Every
string count, symbol search and identifier list in this repository that touches
`CBEUB.exe` is a measurement over **28.59 %** of it. The data files are not
packed and carry no such caveat: `.arc`, `.tex`, `.sngw`, `.mfx` and the
fourteen ROMs are readable in full.

This is the first object in the collection whose coverage is below 100 % for a
structural reason rather than for lack of effort, and the figure is repeated
beside every affected count rather than stated once here.
