# 15 — corrections: twelve in the pre-briefing, four of mine

*Measure: each correction names the claim, the measurement that overturned it,
and the command.*

The pre-briefing in `_pre\` was written in one pass by the same process that
wrote the previous ten, which carried 6 to 18 errors each. This one carries
twelve that this session found. It was right about far more than it was wrong
about, and the twelve are listed because that is what the list is for.

## in the pre-briefing

**1. The `IBIS` header is not what it said.** It described `+8 u32 header size`
followed by `+12 pairs of (offset, size)`. Parsing that way gives overlapping
regions and a residue of −33,554,624 on `armwar`. The pairs start at **+8**,
and the value there is the offset of region 0, which is 0x40 on every file —
so the wrong structure produces the right total.
`python tools/ibis.py --validate --map _work/rom/bin/*`

**2, 3, 4. Every archive denominator is one too small.** 251 archives, 425
members, 12 type hashes — because `root.arc` was never opened. It is an `ARC\0`
version 7 file that closes with residue 0 like the others. The figures are
**252, 426, 13**.

**5. The texture type's uncompressed total.** Reported as 1,275,599,399;
the 29-bit size field gives **1,488,896,704**, confirmed by inflating all 426
members rather than trusting the field.
`python tools/mtarc.py --census` + `--inflate-check`

**6. "the last member ends exactly at end-of-file on 251 of 251".** True, and
it is **252 of 252**.

**7. The accounting mixes two denominators.** The gallery line
(648,440,086) counts bytes of files on disk; the ROM line (72,473,696) counts
bytes of compressed members. The two are not comparable. On disk the ROM
archives are **72,932,448**, and the difference — 458,752 — is 14 × 32,768 of
archive header. The total still closes either way.

**8. Seven menu fonts, and there are eight.** `menu_chS` was missed.
Eight `rGUIMessage` tables, eight fonts, 932 strings each, and language index 6
is absent from the numbering. `python tools/gmd.py --validate`

**9. `.bind` is 206,616 bytes, not 206,104.**

**10. "There is no in-binary credits list at all beyond zlib."** There is a
full third-party attribution page — GGPO, Digital Hearts, Fontworks, Founder,
Keywords Studios, and the *Tenchi wo Kurau* rights holders — in
`ui\1_menu\18_credit\tex\credit05_BM_NOMIP`. The pre-briefing's claim is true
of the executable's strings and false of the product.
See [10-credits.md](10-credits.md).

**11. "The readable part of the executable carries no personal name at all."**
Also true of the strings and false of the product: the credit roll names
**more than a hundred people**, in five textures. The pre-briefing had flagged
the risk itself — it wrote down that the previous session's scanner returned
zero on a disc naming 110 people — and then reached the same conclusion anyway.

**12. The collection denominators.** Given as 102 repositories and 73 with hash
lists. Measured at the start of this session: **106 and 74**, and 75 once this
repository has a `notes\`. Published before the crossing was run, per rule.

### and one that belongs to the brief rather than the pre-briefing

The session brief says of the Studio field: *"nell'eseguibile leggibile non
c'è"* — the string "Capcom" is not in the readable executable. It is:
`CompanyName CAPCOM CO., LTD.` in the `VS_VERSIONINFO` resource, which is not
packed. `.rdata` does not contain it; `.rsrc` does.
`python tools/pe.py --version "$CB/CBEUB.exe"`

### a description worth sharpening, not an error

The pre-briefing called region 0 "byte-interleaved … the way the hardware wires
two 8-bit chips onto a 16-bit bus". The fix is the same operation either way,
but there is no even-chip/odd-chip separation anywhere in the file: it is one
stream whose 16-bit words are byte-swapped relative to the 68000's byte order.
Worth saying precisely because the *graphics* region genuinely is a
de-interleave question ([06-regions.md](06-regions.md)) and conflating the two
would hide the finding.

## mine

**A. I published a wrong `IBIS` parser before I published a right one.** My
first reader implemented the pre-briefing's structure and produced
`residue=-33554624` on every file. It failed loudly, which is why it took two
minutes rather than an afternoon — but the reason it failed loudly is that I
added a contiguity check the pre-briefing's version of the format would have
passed without. **Every patch must be able to fail noisily**, and this is the
session's example of the rule earning its keep.

**B. My `FBA` scan fired eight times on its own output.** Searching the
readable sections for emulator tokens returned 8 hits for `FBA`, every one of
them inside the hexadecimal offset column my own string dumper prints —
`.rdata+0x03DFBA`, `.rsrc+0x011FBA`. Had I counted instead of reading, this
repository would have published eight hits for the name of an emulator project
in a chapter about whether the product derives from one.
*A scan that fires is a scan to be read by hand*, and this time what needed
reading was the tool.

**C. I called format `0x2A` BC7 and rendered noise.** The size prediction
matched, the mode histogram looked plausible at a glance, and the image was
structured garbage. The format is BC3-shaped with a hard-wired green field and
luminance in the alpha channel ([09-textures.md](09-textures.md)). A size that
matches is not a format identified.

**D. I tried YCoCg before YCbCr and picked scale factors by eye.** The YCoCg
reconstruction produced a magenta-and-green image that I nearly rationalised
with a per-channel scale of 1.0 and 0.25 — a fudge factor of exactly 4, which
was the object telling me the colour space was wrong and not the scale. The
correct answer needed no fudge factor. **A constant you have to invent to make
a theory fit is the theory failing**, and the chroma scale that *is* still not
exact is recorded as an open gap rather than tuned until it looked right.

## a prediction that was wrong, and it was the right kind of wrong

**C26** predicted that decoding the credit resource would yield **no personal
names**, on the reasoning that a 2018 corporate re-release credits companies.
It yields more than a hundred. I wrote in the prediction file that I was
recording it "precisely because the previous session's scanner returned zero on
a disc naming 110 people" — and then predicted zero anyway. The lesson had been
written down and not applied.
