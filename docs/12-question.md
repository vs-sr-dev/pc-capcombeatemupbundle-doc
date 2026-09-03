# 12 — whose emulator: not determinable, and here is why that is a finding

*Measure: everything in chapters 03 to 11. This chapter adds no new
measurement; it weighs the ones that exist against a bar that was written down
before any of them were taken.*

## the question, and the half of it that was already answered

The session was commissioned to answer one thing: **is this a native
reimplementation of seven arcade games, or an emulator running the original
ROMs — and if it is an emulator, whose?**

The first half was settled before the session opened a file, and this session
re-derived every piece of it independently:

* fourteen resources of an engine type whose hash resolves to the class name
  **`rRom`** ([04-arc.md](04-arc.md));
* inside each, four regions carrying a 68000 program with a legal reset vector,
  a Z80 program opening `DI ; IM 1`, graphics, and samples with a validating
  pointer table ([06-regions.md](06-regions.md));
* the original arcade build banners still in place — ` version 2.00 `,
  ` 1.16b /CPS2      1996  /APRIL     `, `T H E  K I N G  O F  D R A G O N S ///
  9 1 0 8 0 5`;
* the boards' own power-on memory test still there, naming `WORK RAM`,
  `SCROLL 1`, `OBJ RAM`.

**This product runs the arcade code.** A reimplementation does not ship the
1991 sound driver of the original board and then start it.

## the bar, set before the measurements

`_pre\question.txt` fixed what would and would not count, and this chapter is
bound by it.

**Refused as evidence, and the reasons hold up after measuring:**

* *the four-region layout* — the board has a program ROM, a graphics ROM, a
  sound ROM and a sample ROM. Four regions is four things;
* *the region sizes* — 0x18000, 0x40000, 0x50000, 0x400000 are what the chips
  held;
* *the presence of Z80 and 68000 code and samples* — Capcom's own property,
  from Capcom's own boards;
* *the absence of `MAME`, `FinalBurn` or `FBA` strings* — 71.41 % of the
  executable is at entropy 8.0000. That absence is a measurement of a wrapper.
  This session's own scan for `FBA` returned eight hits, all eight of which
  were in its own output's offset column
  ([03-executable.md](03-executable.md)). Even the absence was wrong before it
  was read by hand.

**And the new one this session adds to the refused list:** *a resemblance is
not a derivation.* Two implementations of the same hardware resemble each other
because the hardware is the same. The evidence of derivation is a shared
**arbitrary** choice, never a shared **necessary** one, and telling those apart
is the whole job.

## the four tests, worked

### 1. an arbitrary name that only one project uses — weak, and it is the strongest thing here

The fourteen names are in the executable, at `.rdata+0x3368`, as
compiler-emitted string literals in source order, Japanese member first in
every pair ([03-executable.md](03-executable.md)).

What can be measured about them, from inside the object:

* they are **all eight characters or fewer**, lower case. That is a constraint,
  and it is doing work: the Japanese member of `captcomm` is `captcomj`, with
  one `m`. A stem-plus-`j` convention applied to `captcomm` would give
  `captcommj`, nine characters; truncating that to eight gives `captcomm`,
  which collides with the World member. **So a letter was dropped to fit, and
  the result is a name no unconstrained namer would invent.**
* the convention is **internally inconsistent**. `wof` / `wofj` names both
  members after the World title, *Warriors of Fate*, even though the Japanese
  release has an entirely different title. `armwar` / `pgear` does the
  opposite: each member is named after its own territory's title, *Armored
  Warriors* and *Powered Gear*. **Two different policies for the same problem,
  inside fourteen entries.**

An inconsistency like that is what a list that **grew** looks like, rather than
one designed in a sitting. That is suggestive of inheritance from somewhere.

**And it is not evidence, for a reason the object itself supplies.** Capcom
had internal short names for its own arcade boards in the 1990s; the eight
character limit is the same limit every 1990s tool had; and abbreviating
*Final Fight* to `ffight` and *Battle Circuit* to `batcir` is what anyone
would do. The question.txt test was *"would an independent namer have landed on
all fourteen"* — and the honest answer this object supports is **"probably not
on all fourteen, and this object cannot tell you who did not"**. Testing it
properly means comparing against a public list, which means bringing an outside
corpus into a repository whose whole discipline is measuring the bytes in front
of it, and doing it with a *published* list rather than from memory. **That
comparison was not made here.** It is named in "what would close this" below.

### 2. an arbitrary layout decision that hardware does not force — found, and it does not discriminate

This was the promising thread and it paid out. Region 0 on the two CPS2 titles
is doubled: encrypted image and decrypted image side by side, with the halves
byte-identical outside the cipher's range. The Q-Sound sound CPU has the same
arrangement one region over, the Kabuki-encrypted image at 0x00000 and its
plaintext at 0x28000 ([07-halves.md](07-halves.md)).

Those are software decisions. Nothing in the hardware requires 0x28000.

**They still do not discriminate**, and the reason is worth stating precisely:

* keeping both images is not a habit, it is a **requirement**. On this
  hardware the cipher applies to instruction fetches and not to data reads, so
  any implementation that decrypted in place would corrupt every constant the
  program reads out of its own ROM. Every correct implementation keeps two
  images. That is a necessary choice, not an arbitrary one;
* what *is* arbitrary is the offset — 0x400000, 0x28000 — and a number is only
  a signature if you can show the same number somewhere else. This branch does
  not run other implementations and has none to compare against.

### 3. a data table that is not in the ROMs and not derivable from them — **designed out of the product**

This was named as the single most discriminating test available, with the
expectation that the tables would be inside the packed `.text`.

They are not inside `.text`. **They are not in the product at all, because the
product does not need them.**

There are exactly three things about this hardware that cannot be derived from
the ROMs — each needs a key or a table that lived in silicon:

| the secret | where it is in this product |
|---|---|
| the CPS2 program cipher | **already applied**; plaintext shipped beside ciphertext |
| the Kabuki sound-CPU cipher | **already applied**; plaintext at region 2 + 0x28000 |
| the CPS graphics interleave | **already applied**; region 1 ships as flat 16×16 4bpp tiles, measured by correlation and confirmed by rendering recognisable sprites |

All three transformations were performed offline, before the data was packed,
and the results were shipped. The Kabuki case carries its own proof that it was
done to a buffer rather than on demand: the transform was applied across the
0xFF padding too, dropping that block's filler fraction from 13.4 % to 0.4 %.

**So the most discriminating test is unavailable, and not because of the
packer.** It is unavailable because the shipping build was constructed so that
the running program never has to know any of these secrets. Whatever tables
were used to bake the data lived in the build pipeline, and the build pipeline
was not shipped.

That is a stronger and more useful answer than "it is behind the encryption",
and it would remain true if the `.text` section were handed over tomorrow.

### 4. verbatim text — none, over 28.59 % coverage

No licence header, no identifier, no comment, no error string naming any
emulation project appears in any readable byte of any of the 268 files. That
covers the data files **in full** — they are not packed — and the executable
over **28.59 %** of its length.

The one place in the product whose entire job is third-party attribution is the
credit roll's legal page, and it credits GGPO for networking, Digital Hearts
for QA, Fontworks and Founder for fonts, Keywords for localisation, Shueisha
and Hiroshi Motomiya and Thirdline for the *Tenchi wo Kurau* licence, and zlib
for compression ([10-credits.md](10-credits.md)). **It credits no emulator.**

That is a much better-aimed absence than a string search — it is an absence
from the page where a presence would belong. It is still an absence.

## what the product says about who wrote it

One place, two words:

```
PROGRAMMING            KOBUTA
                       MUUMUU
```

Two entries under the heading that names whoever wrote the code, neither in the
`FIRSTNAME SURNAME` form the other hundred-odd credits use. Nothing else in the
product expands either token. Whether they are handles for individuals or names
of outside parties **is not determinable from the shipped files**, and this
repository does not go outside them to find out.

## the answer

**Whose emulator this is cannot be determined from the shipped files.**

Not because the code is encrypted — though 71.41 % of it is — but because the
three pieces of knowledge that would carry an author's fingerprint were used at
build time and left out of the build. What ships is arcade data that has been
correctly unwrapped, in a container Capcom designed, loaded by an engine Capcom
wrote, under a resource class Capcom named `rRom`, executed by code nobody
outside Capcom has read.

Hypothesis A (Capcom wrote it), B (Capcom contracted it) and C (it derives from
a public emulator) are **all consistent with every measurement in this
repository**, and the credit roll's `PROGRAMMING KOBUTA / MUUMUU` is mild
evidence for B over A without saying anything about C.

**No finding of licence infringement is published, in either direction.** The
evidence is circumstantial, this repository documents bytes, and an accusation
against a company on this material would be exactly the error `question.txt`
was written to prevent. The measurements are published so that somebody with
the missing piece can close it.

## what would close it, and what this branch will not do

| what would close it | available here |
|---|---|
| the contents of `.text` | no — packed, and this branch does not unwrap packers |
| a run-time memory image | no — this branch does not execute the object |
| a per-game configuration table in a shipped file | **no — measured, and it is not there; see test 3** |
| a verbatim string | no — measured over 28.59 % of the executable and 100 % of the data |
| a published set-name list from another project, compared entry by entry | **not done here** — it needs an outside corpus and a published source, not a memory |
| an expansion of `KOBUTA` or `MUUMUU` | not in the product |

Three of those six are forbidden by this branch's rules. One is measured and
answered in the negative. One is not in the product. **The remaining one — the
name comparison — is the only route left that does not require running or
unwrapping anything**, it is the natural next session's work, and it must be
done against a published list with the list cited, not from recollection.

That is where this stops, and it stops there deliberately. *A non-measurement
labelled as one is worth more than a conclusion drawn from fourteen file
names.*
