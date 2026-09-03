# 08 — the byte that changes: the bundle ships eleven programs, not fourteen

*Measure: `python tools/pairdiff.py` on all four regions of all seven pairs —
28 comparisons, listed rather than hashed.*

## the diffs

```
pair                 r0            r1          r2         r3
ffight  / ffightj    1 byte        9,174       identical  identical
kod     / kodj       2 bytes       identical   identical  identical
captcomm/ captcomj   1 byte        identical   identical  identical
knights / knightsj   392,445       identical   identical  identical
wof     / wofj       839,146       54,846      identical  identical
armwar  / pgear      2,232,969     identical   identical  identical
batcir  / batcirj    2,088,974     identical   identical  identical
```

Three pairs differ by one or two bytes. Four differ by hundreds of thousands or
millions. The pre-briefing recorded this and said nobody had explained why.

## the three that differ by a byte, disassembled

The differing bytes, with their surroundings, after the word swap that turns
region 0 into a 68000 image:

**`captcomm` vs `captcomj`, one byte of 8,749,120, at region 0 + 0x0E6A
(file offset 0xEAA):**

```
0x000E60   60 00 FE B2  4B F8 80 00  1B 7C 00 02  78 8A  33 FC
0x000E60   60 00 FE B2  4B F8 80 00  1B 7C 00 00  78 8A  33 FC
                        ^^^^^^^^^^^  ^^^^^^^^^^^^^^^^^^
                        LEA $8000.W,A5
                                     MOVE.B #$02,$788A(A5)
                                     MOVE.B #$00,$788A(A5)
```

**`kod` vs `kodj`, two bytes, at region 0 + 0x1D72:**

```
0x001D70   3B 7C 02 02  8F 00      MOVE.W #$0202,$8F00(A5)
0x001D70   3B 7C 00 00  8F 00      MOVE.W #$0000,$8F00(A5)
```

**`ffight` vs `ffightj`, one byte, at region 0 + 0x72600:**

```
0x0725F0   00 00 ... 00 00
0x072600   00 04  FF FF FF FF ...
0x072600   00 00  FF FF FF FF ...
```

— not an instruction. A single word in a data table, in a run of zeros
followed by 0xFF fill.

So the mechanism is not one mechanism. In two of the three it is **an
immediate operand inside a `MOVE` that writes a constant into work RAM at
start-up**; in the third it is **a word of static data**. What they have in
common is that a small value, 0 for the Japanese member and 2 or 4 for the
other, is being handed to the program as configuration.

That it is a region selector is confirmed from the other side. `kod` region 0
contains all three region strings —

```
T H E  K I N G  O F  D R A G O N S ///  9 1 0 8 0 5 ///  J A P A N
T H E  K I N G  O F  D R A G O N S ///  9 1 0 8 0 5 ///    U S A
T H E  K I N G  O F  D R A G O N S ///  9 1 0 8 0 5 ///    E T C
```

— all in the same image, with the same build date. **The ROM contains every
region; the byte says which one to be.**

## the four that differ by megabytes

These are not patched copies. They are different program builds, and the object
says so itself:

* `wof` and `wofj` have the **same initial stack pointer and different entry
  points** — 0x0000754A against 0x000071A2. A one-byte patch cannot move an
  entry point;
* their build stamps sit at different offsets in the two images (0x003A2E
  against 0x003B36 for the same string), which is what happens when code above
  them changes size;
* `wof` and `wofj` also differ in **region 1**, the graphics — 54,846 bytes —
  which no configuration byte can do;
* `knights` / `knightsj` differ over 392,445 bytes of region 0 with the same
  displacement pattern.

For `armwar` / `pgear` and `batcir` / `batcirj` the arithmetic is different
again, because those region 0s hold two images
([07-halves.md](07-halves.md)). `armwar` vs `pgear` differ in 2,232,969 of
8,388,608 bytes — and the encrypted range on those sets is 1 MB, so a program
difference over the plaintext appears twice, once in each half.

## the count that changes

| | |
|---|---|
| ROM images shipped | 14 |
| distinct 68000 programs | **11** |
| pairs that are one program plus a patched constant | 3 (`ffight`, `kod`, `captcomm`) |
| pairs that are two builds | 4 (`knights`, `wof`, `armwar`/`pgear`, `batcir`) |

The product ships **eleven distinct arcade programs in fourteen containers**.
The three patched pairs share region 0 to within two bytes and share regions 1,
2 and 3 exactly (except `ffight`, whose graphics differ in 9,174 bytes — a
title-screen change, in the region that holds tiles).

That is worth 24,150,208 bytes of duplication measured at the region level:
the three near-identical region 0s plus their identical regions 1–3, shipped
twice. In a product where the compressor is already doing the heavy lifting it
is not a scandal; it is an honest number for
[14-leftovers.md](14-leftovers.md).

## the method note

Every figure above comes from a byte-by-byte comparison that reports how many
bytes differ, in how many contiguous runs, and lists them individually when
there are few enough to list. **A hash proves equality and does not measure
difference** — the previous session's lesson, applied here to twenty-eight
region comparisons, and it is the whole reason this chapter exists.
