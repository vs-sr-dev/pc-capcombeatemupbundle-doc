# 09 — the .tex format, derived, and the eighty per cent finally looked at

*Measure: `python tools/mttex.py --census`, then `--png`. 350 textures,
1,488,896,704 bytes uncompressed, 80.09 % of the product by weight, and this
collection had never opened one.*

## the header

```
+0   4    'TEX\0'
+4   u32  flags / version  -- 0x200080A1 on every 2D texture here
+8   u32  mip count in bits 0-5, width in bits 6-18, height in bits 19-31
+12  u32  format code in bits 8-15
+16  u32  header size -- 0x18 on every 2D texture
+20  u32  unused on 2D textures
```

The dimension packing was derived by trying the obvious field splits against
files whose payload length was known independently, and it is confirmed by
reconstruction rather than by inspection:

```
name          w    x h     mip  computed     observed
splash      1920 x 1080   1   0x21C1E001   0x21C1E001   MATCH
sysfont     1024 x   16   1   0x00810001   0x00810001   MATCH
Font_jpn    1024 x 1024   1   0x20010001   0x20010001   MATCH
Font_chS_02 1024 x  128   1   0x04010001   0x04010001   MATCH
Font_index   512 x  512   1   0x10008001   0x10008001   MATCH
```

**The test that turns this into a structure rather than an arithmetic:** the
width and height this header predicts, multiplied by the bits per pixel the
format code implies, must equal the payload length that the `.arc` entry
declared in a completely different place. It does, on every texture measured:

```
g0001_BM_HQ_NOMIP   1920x2560  payload 4,915,200  predicted 4,915,200  MATCH
g0100_BM_HQ_NOMIP   1512x2160  payload 3,265,920  predicted 3,265,920  MATCH
g0301_BM_HQ_NOMIP   2712x1896  payload 5,141,952  predicted 5,141,952  MATCH
g0500_BM_HQ_NOMIP   1656x2160  payload 3,576,960  predicted 3,576,960  MATCH
g0700_BM_HQ_NOMIP   1528x2160  payload 3,300,480  predicted 3,300,480  MATCH
```

Two encodings of the same quantity, in two files, agreeing.

## the three formats

| code | bits/px | what it is | how it was decided |
|---|---:|---|---|
| `0x19` | 4 | BC1 | 0.5 bytes per pixel; blocks with a zero first byte occur, which BC7 cannot produce |
| `0x30` | 8 | BC7 | decodes cleanly; mode histogram over 20,000 blocks is 4/5/6/7, which is what a real BC7 encoder emits |
| `0x2A` | 8 | **not a standard format** — see below | |

Format `0x2A` decoded as BC7 gives structured noise, and it is the format the
whole gallery uses, so it had to be worked out.

## format 0x2A, derived

The blocks are BC3-shaped: eight bytes of interpolated alpha, then a
DXT1-style colour block. But:

* the alpha channel decoded on its own is **a clean greyscale photograph** —
  the picture, in full 8-bit tone;
* across 50,000 sampled colour endpoints, **bits 5–10 of both RGB565 endpoints
  are set in 100 % of blocks**. That is the six green bits, hard-wired to
  `111111`. The decoded green channel has exactly **one distinct value** over a
  1920×2560 image;
* so the colour block carries two 5-bit fields, not three, and the 8-bit alpha
  carries the luminance.

Luminance at 8 bits plus two chroma fields at 5 bits is a **YCbCr** layout.
Treating (alpha, B-field, R-field) as (Y, Cb, Cr) and converting produces a
correct-looking colour image; treating the same fields as YCoCg does not.

**Honest limit, and it is a real one.** The conversion is right in structure
and **not yet exact in scale.** On most gallery textures the result is
close to the game's own output — the repository's owner opened the game and
compared one rendered image against what the gallery menu displays, and
reported it as very close but not exact — but on the most saturated image in
the sample
(`g0001`, the 2018 key art) the colours are visibly off. The chroma fields are
5-bit and something about their quantisation or range is not being undone
correctly. **The format is derived; the exact chroma scale is not, and that is
recorded as an open gap rather than smoothed over.**

For the record, the decoder relies on Pillow's public BCn decoder by wrapping
the payload in a DDS header (`tools/ddswrap.py`) rather than reimplementing
block decompression. That is a public implementation used deliberately and
named, per the branch rule on public formats.

## what the eighty per cent is

Twelve gallery textures were extracted and rendered — **12 of 342, a sample,
and it is a sample rather than a census because 342 of them unpack to
1.28 GB**. They are:

* scanned **arcade flyers and promotional posters** from the original releases;
* **character design sheets** — line art of each fighter in several poses,
  laid out as reference material;
* **painted key art** and cover illustrations;
* **sprite reference sheets** showing the pixel characters at large scale;
* new 2018 artwork drawn for the bundle itself.

Sizes run from 1512×2160 to 4080×1428; the shapes are page-shaped and
poster-shaped, not screen-shaped, which is what scanned paper looks like.

**Four fifths of the weight of this product is a museum of the paper that
advertised seven arcade games**, and the games themselves are 9 %. That ratio
is the reason [01-object.md](01-object.md) had to fix a definition before
computing anything.

## the fonts

`Font_jpn_00..07`, `Font_chS_00..02` and `Font_index_jpn_00` are 1024×1024
BC3-shaped glyph atlases at 1,048,600 bytes each — eight megabytes of Japanese
glyphs, three of Simplified Chinese. Their companion members of type
`rGUIFont` carry magic `GFD\0` and hold the metrics; that format was not
derived and is recorded as an open gap.

The 4-bit-per-pixel BC1 `sysfont_AM_NOMIP.tex` at 1024×16 is the debug font:
one row of 8×16 ASCII cells.
