# 14 — leftovers: an object with no medium still has 989 megabytes to account for

*Measure: the counts below, each with its own command. There is no medium here,
so there is no slack, no ECC, no gap and no dummy sector; the candidates are
different in kind and had to be chosen rather than inherited.*

## what is not here

No sectors, no error correction, no cue sheet, no unclaimed space, no `IP.BIN`,
no lead-out. **The file bytes are all the bytes**, and 268 of 268 files are
claimed by the accounting in [01-object.md](01-object.md) with residue 0. Every
category of waste this collection normally measures is absent, and their
absence is the first entry in the list.

## the leftovers this object does have

### 1. the gallery — 648,440,086 bytes, 80.09 %

Not waste, and the nearest thing this product has to it: four fifths of the
weight for a function that is not playing a game. Nothing in the install is
larger, and the seven games it exists to sell are 9.01 % of it.

### 2. the executable that cannot be read — 4,502,528 bytes, 71.41 % of it

`.text` at entropy 8.0000 plus `.bind` at 7.9554: 4,709,144 bytes that this
repository can measure the size and entropy of and nothing else. From the point
of view of measurement it is a leftover even though from the point of view of
the product it is the whole thing.

### 3. what the compressor makes disappear — 924,049,533 bytes

```
sum of uncompressed members  1,707,290,593
sum of compressed members      783,241,060
                             ---------------
difference                     924,049,533
```

Nobody had quantified this. A 809 MB install unpacks to about 1.7 GB. The
gallery accounts for most of it: 1,488,896,704 bytes of texture squeezed into
709,939,450.

### 4. archive header padding — 8,257,536 bytes

Every archive, single-member or not, reserves a 32,768-byte header block, of
which 8 + 80×members bytes are used and the rest is 0x00 fill (verified on 242
of 242 single-entry archives). 252 × 32,768 = 8,257,536, and that figure is
exactly the difference between the 791,498,596 bytes the archives occupy and
the 783,241,060 bytes of compressed member data.

### 5. ROM region allocated and not used — 32,449,970 bytes, 42.98 %

Region 0 is sized to the 68000's address window rather than to the program.
Measured as the trailing run of 0x00/0xFF ([05-ibis.md](05-ibis.md)):
75,497,472 bytes allocated across 14 sets, 43,047,502 used, **32,449,970
filler** — 79.1 % of it on `ffight`, 0.0 % on `armwar`.

### 6. the three duplicated ROM sets — 24,150,208 bytes

`ffight`/`ffightj`, `kod`/`kodj` and `captcomm`/`captcomj` are the same program
shipped twice with one or two bytes changed
([08-pairs.md](08-pairs.md)). Uncompressed, the second copy of each is
6,651,968 + 8,749,120 + 8,749,120 = 24,150,208 bytes that differ in four bytes
in total.

### 7. seven byte-identical archive members

Two `dummy` textures in `game_win.arc` and one `onlineIcon` repeated in all
seven `game_bgNN.arc`. 419 distinct sha1s over 426 members.

### the four countable ones, added up

```
what the compressor makes disappear     924,049,533
archive header padding                    8,257,536
ROM region allocated and not used        32,449,970
the three duplicated ROM sets            24,150,208
                                       --------------
                                        988,907,247
```

Those four are disjoint — the first counts compressed-versus-uncompressed, the
other three count uncompressed bytes — so the total is a sum of different
things and is printed as a bound rather than as a single quantity. The
gallery and the unreadable executable are deliberately not in it.

### 8. **zero text files, and that is the entry that matters**

```
find "$CB" -type f | sed 's/.*\.//' | sort | uniq -c
    252 arc    8 tex    4 sngw    1 mfx    1 fsd    1 exe    1 dll
```

Seven extensions. **No readme, no manual, no licence file, no third-party
notice, no `.txt` of any kind, in an 809-megabyte installation.**

That absence was going to be this chapter's headline: a product incorporating
third-party software with nothing on disk to say so. It survives as a fact
about the *files* and it does **not** survive as a fact about the product,
because the attributions exist — GGPO, Digital Hearts, Fontworks, Founder,
Keywords, and the *Tenchi wo Kurau* rights holders — **painted into a 2048×512
texture in the credit roll** ([10-credits.md](10-credits.md)).

So the honest form of the finding is narrower and more interesting than the one
this chapter was going to make:

> **Every legal notice this product carries is an image.** There is no machine-
> readable licence text anywhere in 809,632,531 bytes. A user cannot grep for
> it, a tool cannot index it, and a scanner looking for third-party
> attributions finds nothing — the same reason `contacts.py` found none of the
> hundred people the product names.

That is not a waste category. It is the leftovers chapter's contribution to
[12-question.md](12-question.md), and it is why the answer there is "not
determinable" rather than "no attribution exists".
