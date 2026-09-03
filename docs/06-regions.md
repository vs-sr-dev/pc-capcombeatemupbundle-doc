# 06 — the four regions: identified from the bytes, not from the sizes

*Measure: `python tools/romcensus.py --regions _work/regions`, plus
`tools/m68k.py`, `tools/z80head.py` and `tools/tilepeek.py`. Twelve of the
fourteen maps had never been looked at; one region of four was identified.*

The rule for this chapter: **a region is not a Z80 program because 0x18000 is
what a CPS1 sound board held.** It is a Z80 program because its first three
bytes decode as `DI ; IM 1 ; JP`. Every identification below is a positive test
on the contents, and where a test does not apply the chapter says so instead of
extending it.

## region 0 — the 68000 program

The bytes at the start of `ffight` region 0 are

```
ff 00 00 10 05 00 ac e7 07 00 dc 18 07 00 e6 18 ...
```

which is not a 68000 vector table. Exchange the two bytes of every 16-bit word
and it is:

```
00 ff 10 00   ->  SP = 0x00FF1000
00 05 e7 ac   ->  PC = 0x0005E7AC
00 07 18 dc, 00 07 18 e6, 00 07 18 f0, 00 07 18 fa, ...
```

— exception vectors ten bytes apart, pointing at consecutive handlers. The
stack pointer lands in `0x00FF0000`, which is the CPS work-RAM window. That is
the test `tools/m68k.py --vectors` applies, and it names the 0xFF0000
assumption as the board's rather than hiding it.

| set | SP | PC |
|---|---|---|
| ffight, ffightj | 0x00FF1000 | 0x0005E7AC |
| kod, kodj | 0x00FF0E18 | 0x00000340 |
| captcomm, captcomj | 0x00FF81D6 | 0x000005FE |
| knights, knightsj | 0x00FF81D6 | 0x000005FE |
| wof | 0x00FF62EE | 0x0000754A |
| wofj | 0x00FF62EE | 0x000071A2 |
| armwar, pgear (second half) | 0x00FF01D6 | 0x000002D6 |
| batcir, batcirj (second half) | 0x00FF4DD8 | 0x000008E8 |

Ten of ten CPS1 sets pass directly. The four CPS2 sets pass **only in the
second half** of their doubled region 0, which is [07-halves.md](07-halves.md).

Note `wof` and `wofj`: same stack pointer, **different entry point**. They are
different builds, not one build patched — which matters in
[08-pairs.md](08-pairs.md).

### the byte swap is not a de-interleave of two chips

The pre-briefing described region 0 as "byte-interleaved, i.e. a 68000 program
stored the way the hardware wires two 8-bit chips onto a 16-bit bus". The
operation that fixes it is the same one either way, but the description is
worth getting right: the region is a single stream in which each 16-bit word
has its bytes in little-endian order, and the 68000 is big-endian. There is no
separation into an even chip and an odd chip anywhere in the file — the halves
are not stored apart.

The proof is the text. The pre-briefing quoted `batcir` as reading

```
      UB SREOR R  DARDSE SREOR R  LIELAG LNITS
```

Swap the bytes of each word and it reads

```
     BUS ERROR    ADDRESS ERROR   ILLEGAL INST
```

— the 68000 exception names, in the program's own exception handler. The same
table appears in `captcomm`, `knights`, `wof` and `kod`, and `knights` adds
`WORK RAM`, `SCROLL 1`, `SCROLL 2`, `SCROLL 3`, `OBJ RAM`, which is a
power-on memory test naming the board's own memory regions.

`kod` carries a build stamp, in the arcade program, spaced one character per
cell for a tile-based font:

```
0x001EBA  T H E   K I N G   O F   D R A G O N S///   9 1 0 8 0 5///   J A P A N
0x001F18  T H E   K I N G   O F   D R A G O N S///   9 1 0 8 0 5///     U S A
0x001F74  T H E   K I N G   O F   D R A G O N S///   9 1 0 8 0 5///     E T C
```

**All three region strings are in the same ROM**, which is what turns the
one-byte difference between `kod` and `kodj` into a selector rather than a
build difference. Both members carry the same build date, 91-08-05.

## region 1 — the graphics, already de-interleaved

No vector table, no Z80 entry, and no tile layout that this collection's usual
guesses produce: rendering it as 8×8 planar, 8×8 split-planar, 8×8 packed and
16×16 four-plane all give noise. So it was measured instead of guessed:

```
match fraction at stride k   k=1 0.5151  k=4 0.3845  k=8 0.5833  k=16 0.4957
fraction of bytes whose two nibbles are equal:  0.7232
fraction 0xFF: 0.5061   fraction 0x00: 0.0426
```

A correlation peak at **8 bytes** and 72 % of bytes having equal nibbles is the
signature of **4bpp data packed two pixels to a byte, sixteen pixels to a
row** — flat areas produce equal nibbles, and a 16-pixel row is 8 bytes.
Rendering `ffight` region 1 as 16×16 tiles of packed 4bpp produces recognisable
Final Fight sprites: heads, torsos, limbs, in sprite-strip order.

**That is a finding, and it is one of the two that matter most in this
object.** CPS graphics ROMs do not sit on the board in that form. On the board
they are spread across four 16-bit chips in an interleave that has to be undone
with per-game knowledge. Here the de-interleave has already been applied and
the shipped region is flat. See [12-question.md](12-question.md).

## region 2 — the Z80 sound program, on 14 of 14

`tools/z80head.py` decodes the opening bytes and refuses to guess at any
opcode it does not have a rule for.

| set | opening | banner |
|---|---|---|
| ffight, ffightj | `DI ; IM 1 ; JP 0x0067` | ` version 2.00 ` |
| kod, kodj | `DI ; IM 1 ; JP 0x006A` | ` version 4.25 ` |
| captcomm, captcomj | `DI ; IM 1 ; JP 0x006A` | ` version 4.25 ` |
| knights, knightsj | `DI ; IM 1 ; JP 0x006A` | ` version 4.25 ` |
| armwar, pgear | `DI ; IM 1 ; LD SP,0xFFFF ; LD A,0x77` | ` version 1.05A /CPS2  1993 / JUNE ` |
| batcir, batcirj | `DI ; IM 1 ; LD SP,0xFFFF ; LD A,0x77` | ` 1.16b /CPS2  1996  /APRIL ` |
| wof, wofj | **does not decode** | ` version 1.00   1992 / JUNE  S,m-` |

**Fourteen of fourteen carry a version banner**; twelve of fourteen open with a
Z80 reset sequence. `wof` opens with `63 41 8e 61 ff ff e1 00`, which the
decoder refuses, and that refusal is the finding — see
[07-halves.md](07-halves.md), where the decrypted copy is at region 2 + 0x28000
and opens `DI ; IM 1 ; LD SP` like every other set.

## region 3 — the samples

Entropy 6.46 to 7.50, filler 2.4 % to 15.4 %, no code. On the four CPS1 sets
with an OKI sample chip, the first 0x400 bytes are a **pointer table** and it
validates: 8-byte entries holding 3-byte big-endian start and end offsets,
entry 0 is (0,0), entry 1 starts at 1024 = 0x400 — immediately after the table
— and every subsequent entry starts where the previous ended.

```
ffight    (0,0) (1024,1919) (1920,2735) (2736,4527)   15 of first 16 plausible
kod       (0,0) (1024,1437) (1438,3335) (3336,4609)   15 of 16
captcomm  (0,0) (1024,2627) (2628,4385) (4386,6228)   15 of 16
knights   (0,0) (1024,2673) (2674,4098) (4099,4178)   15 of 16
```

The table's own size and the first sample's start are the same quantity encoded
twice, and they agree, on 4 of 4.

**The test does not apply to the other three games.** `wof`, `armwar` and
`batcir` use Q-Sound, whose sample addressing is not this table, and running
the OKI test on them returns 2 to 3 plausible entries of 16 — which is the
noise floor, not a partial result. It is reported as "not applicable", not as
a failure and not as a measurement.

## the region order

Program, graphics, sound CPU, samples — the container's order matches the
board's functional order rather than being sorted by size, on 14 of 14. On
`armwar` region 1 (20 MB) is larger than region 0 (8 MB), so size ordering is
positively excluded rather than merely unobserved.
