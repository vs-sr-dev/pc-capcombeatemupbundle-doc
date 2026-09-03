# 16 — against the collection: four Capcom containers in twenty-two years

*Measure: `python tools/crossall.py` with the denominators published first;
neighbour figures taken from their own `docs\`, opened for this chapter, not
from memory.*

## the crossing, with denominators first

```
repositories in the collection root ....... 106
of those, with a notes/ directory ......... 75
hash tokens read across all of them ....... 54,148
my hashes offered ......................... 687 distinct
    (268 files + 426 archive members + 56 ROM regions, deduplicated)

CROSSINGS: 0 of my 687 distinct hashes appear in another repository
```

Zero was the expectation and zero is the result. It was still worth running,
for the reason the CD-i branch learned: **when the unit of sharing is smaller
than a file, file hashes do not see it.** So this object was crossed at three
granularities — whole files, archive members, and individual ROM regions —
rather than one, and all three return zero.

The empty-file trap was checked and reported separately: sha1
`da39a3ee5e6b4b0d3255bfef95601890afd80709` appears 30 times in 10 repositories
and is excluded. This object contains no empty file.

## the through-line: Capcom builds containers whose arithmetic closes

This collection has now measured four Capcom products across twenty-two years,
and the same habit shows up in every one. The figures below come from each
repository's own chapters.

| year | object | container | how it closes |
|---|---|---|---|
| 1996 | `pc-residentevil-doc` | `.RDT`, 348 files, 107,977,364 bytes | offset table; **2,972 PlayStation TIM/TMD blocks validated inside them**, 3,429 structures over 44,047 raw identifier hits — a 93 % false-positive rate that only length arithmetic could clear |
| 1998 | `pc-residentevil2-doc` | `.BIN` — **six formats behind one extension**, classified by signature rather than by name | five indexed archives whose offset tables close with residue 0 |
| 2000 | `dc-dinocrisis-doc` | an image container | `size == 12 + w × h × 2` on **3,141 of 3,141** members; sector accounting closes on 265 of 265 files |
| **2018** | **this object** | **`ARC\0` v7 and `IBIS` v4** | **252 of 252 archives end at EOF with residue 0; 426 of 426 members inflate to the declared length; 14 of 14 ROM containers tile contiguously with residue 0** |

**Four products, twenty-two years, two console generations and a PC era apart,
and every one of them ships an offset table whose arithmetic is exact.** That
is a house style, and it is the first cross-title finding this object produced
that has nothing to do with emulation.

It is worth writing because it is true rather than because it fills a chapter,
and the honest caveat goes with it: *exact offset tables are what competent
engineers write*, and this is a claim about consistency, not about uniqueness.

## why this container fell in half an hour and Resident Evil's did not

The brief asked the question directly. The answer is that they are different
kinds of problem and the difference is measurable.

`pc-residentevil-doc`'s `.RDT` was attacked three ways and no layout was
derived, and the reason is visible in that repository's own coverage figure:
**58.94 %** of `.RDT` bytes were accounted for by validated PlayStation
structures, and the other 41 % is room script, collision and camera data in a
format with **no magic number, no version field and no declared member count**.
There is nothing to validate against. The only handle it offered was a
*foreign* structure embedded inside it — TIM and TMD blocks with their own
length arithmetic — which is exactly the move that worked, and it stopped where
the foreign structures stopped.

`ARC\0` and `IBIS` fell quickly because they announce themselves:

* a **four-byte magic**, so the negative control has something to fail on;
* an explicit **version field**, constant across the corpus (7 on 252 of 252,
  4 on 14 of 14);
* an explicit **member count** and an explicit **size per member**, which
  together give an arithmetic that either closes or does not;
* and, decisively, **a second, independent encoding of the same quantity** —
  the declared uncompressed length versus what zlib actually produces; the
  region offsets versus the region sizes; the texture's width and height versus
  the payload length the archive header declared.

*An arithmetic that closes is not a structure demonstrated* — this object's own
`IBIS` header proved that, closing perfectly under a wrong reading
([05-ibis.md](05-ibis.md)). But **a quantity encoded twice, in two places, by
two mechanisms, agreeing on N of N, is a structure demonstrated**, and that is
what the 2018 containers offer and the 1996 one does not.

And `pc-residentevil2-doc` supplied the move this object needed for the thirteen
type hashes: **classify by signature, not by name.** The 426 members here carry
no extensions at all — the names in the archive are `ui\3_image\g00\g0001_BM_HQ_NOMIP`
with the type in a hash field — so every member had to be identified by its
first four bytes (`TEX\0`, `GMD\0`, `GFD\0`, `IBIS`) or by the hash function
derived in [04-arc.md](04-arc.md). Same lesson, twenty years later, applied to
a hash instead of an extension.

## and the warning from the previous session, which landed

`dc-dinocrisis-doc` ended with two lessons, and both of them decided something
here:

**"A hash proves equality and does not measure difference."** Applied to
twenty-eight region comparisons in [08-pairs.md](08-pairs.md), it is the reason
this repository can say that three pairs differ by a `MOVE` immediate and four
by a rebuild, instead of saying that seven pairs have different sha1s.

**"A text search cannot find a name that was drawn."** The scanner returned
zero over 809,632,531 bytes; the product names more than a hundred people and
credits six companies, all in pixels. That lesson was in the pre-briefing, in
bold, and I predicted zero names anyway ([15-corrections.md](15-corrections.md)).

## no platform checklist

There is no `pc-platformnotes-doc` and this session does not propose one.

The PC family in this collection has forty-six repositories and has never
carried a platform checklist, and this object is the wrong one to start with:
its interesting structure is `ARC\0`, `IBIS`, `TEX\0` and `GMD\0` — MT
Framework, which is one company's engine — and the CPS1/CPS2 hardware, which is
arcade. **Almost nothing measured here generalises to "PC".** The one genuinely
platform-level finding, that a Steam install carries a declarable build id in a
manifest one directory up ([13-provenance.md](13-provenance.md)), is two
sentences and belongs in `pc-gamelist-doc`'s conventions rather than in a
checklist of its own.

If a checklist is ever wanted here, the honest unit is **MT Framework**, not
PC, and it wants a second MT Framework title before it is worth writing.
