# 17 — scoring: 10.5 of 12 inherited, 52.0 of 59 open, and the calibration flipped sign

*Measure: every clause in [00-predictions.md](00-predictions.md), scored 1.0,
0.5 or 0.0 against what was actually measured. The two totals are never added.*

Half marks go to clauses whose substance held but whose stated mechanism,
denominator or count did not. That distinction is the point of the exercise: a
clause that was right for the wrong reason is not a clause that was right.

## inherited: 10.5 / 12 (predicted 11.0)

| id | score | note |
|---|---|---|
| I01 | 1.0 | `ARC\0` v7 on 252 of 252; negative control fires on `.exe` and `.tex` |
| I02 | 1.0 | last member ends at EOF, residue 0 — and on 252, not 251 |
| I03 | **0.5** | the accounting closes on 14 of 14 as claimed; the structure I asserted (`+8` = header size) is wrong. See [15-corrections.md](15-corrections.md) |
| I04 | 1.0 | all fourteen sha1s reproduce |
| I05 | 1.0 | the region map reproduces exactly |
| I06 | 1.0 | 1 byte, 2 bytes, and six figures on the rest |
| I07 | 1.0 | closes to 809,632,531, residue 0, gallery 80.09 % |
| I08 | 1.0 | entropy 8.0000 over 4,502,528 bytes; `.bind` present |
| I09 | 1.0 | both class names in `.rdata` |
| I10 | 1.0 | 6 raw, 1 real |
| I11 | **0.5** | every member inflates to its declared length — but it is **426 of 426**, not 425 of 425 |
| I12 | **0.5** | all seven named fonts are present, and there are **eight**; `menu_chS` was not in my list |

I predicted 11.0 and said my weakest was I07 because "a percentage computed
once is the exact shape of the error this branch keeps finding". I07 held. The
three that slipped are all **denominator errors inherited straight from the
pre-briefing** — I re-derived the facts and copied the counts.

That is the lesson of this half of the scorecard: **re-deriving a fact does not
re-derive its denominator**, and I only caught 252/426/13 because `--census`
takes a file list and I passed `root.arc` on the command line by habit.

## open: 52.0 / 59 (predicted 38.0)

**Full marks, 47 clauses:** C02, C04, C06–C16, C19–C21, C23–C25, C28, C31–C36,
C38–C40, C42–C59.

**Half marks, 10 clauses:**

| id | why |
|---|---|
| C01 | region 0 is the 68000 program, but the reset vector is in the first eight bytes only on the ten CPS1 sets; on the four CPS2 sets it is 0x400000 bytes further in |
| C03 | readable text on 10 of 14 as predicted, but what came out was exception names, memory-test labels and build stamps — not the "score labels, stage names, insert-coin strings" I named |
| C05 | Z80 entry sequence on **12** of 14; `wof` and `wofj` do not decode, because they are Kabuki ciphertext |
| C17 | I located the byte in 68000 address space (0xE6B), and could not say "even chip or odd chip" because there are no two chips — see [15-corrections.md](15-corrections.md) |
| C18 | the four megabyte-apart pairs are different builds, shown by different entry points and shifted banner offsets — **not** by "two different build banners or two different date strings", which is what I predicted and which is not there |
| C22 | `TEX\0` on every file opened — 106 of them — not on 342 of 342. A sample, said as one |
| C27 | the fonts are 1024×1024 atlases and localisation is a font-plus-message-table affair, measured; I did not render a glyph atlas |
| C29 | the hash is a CRC-family function as predicted, of the **resource class name**, not of "the resource's extension string" |
| C30 | `0x21D3D8A7` is `rRom` and `0x241F5DEB` is `rTexture` — the substance held, the literal claim (`rom`, `tex`) did not |
| C41 | the naming test is answerable from inside the object and I answered it in prose; I did not put a number on it, which is what the clause promised |

**Zero, 2 clauses:**

| id | why |
|---|---|
| **C26** | *"Decoding the credit resource yields no personal names."* It yields more than a hundred. Discussed below |
| **C37** | *"The `.pdata` exception table gives a function count."* Not done. `.pdata` was measured for size and entropy and never walked |

## the calibration, which changed sign for the first time

```
session n-4   +10.5   over-estimate
session n-3    +7.5   over-estimate
session n-2    +5.0   over-estimate
session n-1    +2.0   over-estimate
this session  -14.0   UNDER-estimate
```

I predicted 38.0 open and scored 52.0. I wrote in the prediction file that "the
straight-line reading says predict about one point under instinct. My instinct
says 39.5, so I write 38.0, and I expect to land within a point either way for
the first time."

I landed fourteen points out, in the other direction, and the correction I
applied made it worse by exactly the amount I applied it.

**What actually happened is not a calibration failure, it is a wrong model.**
The four over-estimates came from sessions where I predicted that hard things
would be easy. This time the hard things — a texture format nobody in this
collection had opened, a CPU-region identification across fourteen sets, a hash
function — were all *mechanical once a tool existed*, and the tools were thirty
to eighty lines each. The three things I flagged as likely losses in the
prediction file (C.4 the textures, C12 the CPS2 ordering, C31 the hash count)
scored 0.5, 1.0 and 1.0 respectively. I mis-ranked my own difficulties, and then
applied a global correction on top of the mis-ranking.

**The prescription for next time, and it is different from the last one.** The
standing prescription — *do not put a numeric threshold in a prediction unless
you can name the mechanism that produces it* — held up well: C31's threshold
("at least 8 of 12") was the only one I flagged as unmechanised, and it was the
one that came in at 13 of 13. Keep it.

The new one is about the totals rather than the clauses:

> **Do not apply a global calibration offset to a session on an object of a
> kind you have not measured before.** The four over-estimates were on optical
> media, where the work is bounded by a medium and the surprises are all
> downside. This was a file tree with self-describing containers, where a
> reader costs thirty lines and pays for four chapters. The trend line was
> fitted to a different population.

## the wrong prediction that was worth the most

C26 is the one to keep. I predicted, in writing, that the credit screen would
contain no personal names — and I wrote in the same sentence that I was
recording it "precisely because the previous session's scanner returned zero on
a disc naming 110 people".

I had the lesson in front of me, quoted it, and predicted against it anyway.
The credit roll names more than a hundred people and credits six companies,
including the only third-party attribution page in the product
([10-credits.md](10-credits.md)).

**A lesson written down is not a lesson applied.** That is worth more than the
half-point it cost.

## and C44, which was the point

C44 predicted, before anything was opened, that the answer to *whose emulator*
would be **"not determinable from the shipped files"**. It scored 1.0.

It should not feel like a win, and it does not. What makes it worth anything is
that the *reason* is not the one I expected: I predicted the tables would be
inside the packed `.text`, and instead they are nowhere, because the product was
built so it never needs them ([12-question.md](12-question.md)). The prediction
was right and its reasoning was wrong, which is exactly the shape this
scorecard gives half marks for — and the clause as written did not name a
reason, so it keeps its point.
