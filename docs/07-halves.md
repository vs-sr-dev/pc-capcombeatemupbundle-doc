# 07 — ciphertext and plaintext, side by side: the product ships the keys' output

*Measure: `python tools/pairdiff.py` between the halves of region 0 on the four
CPS2 sets, and block profiling of region 2 on the Q-Sound sets. This is the one
shape in the object that the hardware does not force.*

## the CPS2 program region

Region 0 on `armwar`, `pgear`, `batcir` and `batcirj` is 0x800000 bytes for a
program the board addresses as 0x400000. The pre-briefing called this the most
promising single thread in the object and did not pull it. Pulling it:

```
                        first half            second half
armwar  entropy          7.9456                6.4420
        first 16 bytes   9f7bc4c31fc3c249...   ff00d6010000d602...
        68000 vectors    SP=0x7B9FC3C4  bad    SP=0x00FF01D6  ok
                         PC=0xC31F49C2  bad    PC=0x000002D6  ok
batcir  entropy          7.9162                6.2413
        68000 vectors    SP=0xF34E2539  bad    SP=0x00FF4DD8  ok
                         PC=0x3FD1056D  bad    PC=0x000008E8  ok
```

The first half is at entropy near 8 with no legal vector table. The second half
is at entropy near 6 with a stack pointer in the board's RAM window and an even
entry point. **The first half is the encrypted program image; the second half
is the decrypted one.**

Then the sharper measurement, which says how much of it is encrypted:

```
python tools/pairdiff.py <first half> <second half>

batcir   identical on 2,105,427 of 4,194,304 bytes (50.1973 %)
            0x000000-0x1FFFFF  differ
            0x200000-0x3FFFFF  IDENTICAL
armwar   identical on 3,149,917 of 4,194,304 bytes (75.0999 %)
            0x000000-0x0FFFFF  differ
            0x100000-0x3FFFFF  IDENTICAL
```

The two halves are the **same image**, differing only over the encrypted range:
2 MB on `batcir`, 1 MB on `armwar`, block-aligned, with everything above it
byte-identical. That is not two builds and it is not two dumps. It is one
program stored twice, once as it sits on the board and once with the cipher
undone over exactly the range the cipher covers.

*A hash proves equality and does not measure difference.* Two sha1s would have
said "the halves are different" and stopped. The diff says which 25 % of
`armwar` is ciphertext.

## the Q-Sound sound CPU

The same shape, one region over, on the games whose Z80 is behind Capcom's
Kabuki custom processor. `wof` region 2, profiled in 32 KB blocks:

```
0x00000  ent=6.709  filler=13.4%  head=63418e61ffffe100   <- does not decode
0x08000  ent=0.000  filler=100 %  0xFF
0x10000  ent=6.155  filler=16.3%  banked data
0x18000  ent=6.239  filler=20.8%  banked data
0x20000  ent=0.000  filler=100 %  0xFF
0x28000  ent=7.666  filler= 0.4%  head=f3ed5631dbdb2174   <- DI ; IM 1 ; LD SP
0x30000-0x4FFFF                   0xFF
```

The image the board would hold is at 0x00000 and does not decode as Z80. **The
decrypted copy is at 0x28000 and opens with the same `DI ; IM 1 ; LD SP`
sequence as every other set in the product.** Its filler fraction is 0.4 %
against 13.4 % for the original, which is the tell: the transform was applied
across the whole block including the 0xFF padding, turning constant fill into
noise. A transform applied to padding is a transform applied offline to a
buffer, not one applied on demand to fetched opcodes.

## what this does and does not prove

**It proves**, with the measurement beside it:

* the shipped product does not execute the CPS2 cipher, because the plaintext
  is in the file;
* it does not execute the Kabuki cipher either, for the same reason;
* both decryptions were performed **before shipping**, by whoever built the
  data, and the results were placed at chosen offsets — 0x400000 for the CPS2
  program, 0x28000 for the Kabuki sound program;
* keeping the ciphertext as well as the plaintext is deliberate and necessary:
  on this hardware the cipher applies to instruction fetches and not to data
  reads, so a program that decrypted in place would corrupt every constant it
  reads from its own ROM. Two images is the correct design, not a lazy one.

**It does not prove** anything about whose emulator this is.

Capcom holds the original keys. A public project holds keys that were
reverse-engineered. Both produce the same plaintext — that is what it means for
a reverse-engineered key to be correct — so **the plaintext cannot distinguish
them.** The question.txt criterion asked for "an arbitrary layout decision that
hardware does not force", and this is one; but the decision it reveals is
*where to put the second copy*, and a single number like 0x28000 is not a
signature. To make it one you would need the same number in another
implementation, and this branch does not have another implementation to hand
and does not run one.

## and the consequence that matters more

The CPS2 cipher, the Kabuki cipher and the graphics interleave
([06-regions.md](06-regions.md)) are the three things about this hardware that
**cannot be derived from the ROMs themselves** — they need a key or a table
that lived in silicon. All three have been applied before shipping.

**So the shipped program does not need any of them.** The most discriminating
test available — "is there a data table here that is not in the ROMs and not
derivable from them?" — is not blocked by the packed `.text` so much as
*designed out of the product*. That is the subject of
[12-question.md](12-question.md), and it is a better answer than the one the
session went looking for.
