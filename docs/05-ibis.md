# 05 — the IBIS container: fourteen arcade programs in a Capcom wrapper

*Measure: `python tools/ibis.py --validate --map _work/rom/bin/*`. Fourteen
files, 214,565,760 bytes, four regions each, residue 0 on 14 of 14.*

## the format, corrected

The pre-briefing described the header as

```
+8   u32  header size (0x40)
+12  ...  pairs of (offset, size)
```

That reading makes the accounting close, and it is wrong. Parsing it that way
produces regions that overlap and a residue of −33,554,624 on `armwar`. The
actual layout is:

```
+0   4    'IBIS'
+4   u32  version          -- 4 on 14 of 14
+8   ...  pairs of (u32 offset, u32 size), terminated by a zero pair
then the regions, back to back.
```

The value at +8 is the **offset of region 0**, which happens to be 0x40 on
every file — so a reader who calls it "header size" gets the right total by
accident. The header proper is 8 + 8×4 + 8 = 48 bytes and is padded to 0x40.

*An arithmetic that closes is not a structure demonstrated.* This is the
cleanest example of that rule the collection has produced: the pre-briefing's
figures were all correct and its structure was not.

The stronger test, which the corrected reader applies, is that the regions must
**tile the file contiguously** — each region's offset equals the previous
region's offset plus its size — and the last must end at end-of-file. That
holds on **14 of 14**, residue 0, contiguous 14 of 14. Two encodings of the
same quantity, agreeing.

Negative control: `python tools/ibis.py --validate "$CB/CBEUB.exe"` →
`NOT-IBIS  magic b'MZ\x90\x00', not b'IBIS'`, rc=1.

## the fourteen, and the map

| set | region 0 | region 1 | region 2 | region 3 | file |
|---|---:|---:|---:|---:|---:|
| ffight, ffightj | 0x0400000 | 0x0200000 | 0x018000 | 0x040000 | 6,651,968 |
| kod, kodj | 0x0400000 | 0x0400000 | 0x018000 | 0x040000 | 8,749,120 |
| captcomm, captcomj | 0x0400000 | 0x0400000 | 0x018000 | 0x040000 | 8,749,120 |
| knights, knightsj | 0x0400000 | 0x0400000 | 0x018000 | 0x040000 | 8,749,120 |
| wof, wofj | 0x0400000 | 0x0400000 | 0x050000 | 0x200000 | 10,813,504 |
| armwar, pgear | **0x0800000** | 0x1400000 | 0x050000 | 0x400000 | 33,882,176 |
| batcir, batcirj | **0x0800000** | 0x1000000 | 0x050000 | 0x400000 | 29,687,872 |

The fourteen decompressed sha1s reproduce the pre-briefing's list exactly, all
fourteen; they are in `notes/sha1-rom-regions.txt` together with the 56
per-region hashes, which nobody had published.

## what the sizes are, and what they are not

`0x18000` of Z80, `0x50000` of Q-Sound Z80, `0x40000` of OKI samples,
`0x200000` and `0x400000` of Q-Sound samples: **those are what the boards
held.** A layout that matches the board is evidence that the product emulates
the board. It is not evidence about whose emulator it is, and this repository
declines to count it as such — see [12-question.md](12-question.md).

**One shape is not the board's**, and it is the only one: region 0 on the two
CPS2 titles is 0x800000 where the hardware addresses 0x400000. That is a
software decision, it is the most informative single fact in the object, and
[07-halves.md](07-halves.md) is about what it turned out to be.

## allocated and not used

Region 0 is allocated far larger than the program it holds on every CPS1 set.
Measured as the tail of 0x00/0xFF filler:

| set | allocated | used | unused | |
|---|---:|---:|---:|---:|
| ffight, ffightj | 4,194,304 | 874,550 | 3,319,754 | 79.1 % |
| kod, kodj | 4,194,304 | 1,047,039 | 3,147,265 | 75.0 % |
| knights, knightsj | 4,194,304 | 1,048,574 | 3,145,730 | 75.0 % |
| wof, wofj | 4,194,304 | 1,048,576 | 3,145,728 | 75.0 % |
| captcomm, captcomj | 4,194,304 | 1,310,720 | 2,883,584 | 68.8 % |
| batcir, batcirj | 8,388,608 | 7,808,947 | 579,661 | 6.9 % |
| armwar, pgear | 8,388,608 | 8,385,345 | 3,263 | 0.0 % |
| **total, 14 of 14** | **75,497,472** | **43,047,502** | **32,449,970** | **42.98 %** |

Forty-three per cent of the program region across the whole product is filler.
It is not waste in any interesting sense — the region is sized to the 68000's
address window, not to the program — but it is 32 MB of the 214 MB the
product ships as ROM, and it belongs in [14-leftovers.md](14-leftovers.md).

The two CPS2 sets are the exception because their region 0 holds two images,
not one.
