# 10 — the credit roll: a hundred names no text search could find

*Measure: five textures from `ui\1_menu\18_credit\tex\`, decoded with
`tools/mttex.py` and read. `contacts.py` returns zero over all 268 files and
809,632,531 bytes; the names below are in the product.*

## the scanner said zero, and the product names a hundred people

```
python tools/contacts.py "I:/SteamLibrary/steamapps/common/CBEUB"
   raw occurrences 5, distinct 5
   function 0 · person-shaped 0 · published 0 · noise 5
   TEL / FAX PATTERN : 0 occurrences
```

That is the same result the previous session's scanner gave on a disc that
named a hundred and ten people, and for the same reason. **A text search cannot
find a name that was drawn.** The credit roll here is five BC7 textures,
1,048,576 bytes each, and every name in it is pixels.

This is the eleventh consecutive session in this branch in which decoding an
image and looking at it decided something, and it is the second consecutive one
in which the thing it decided was that a zero from `contacts.py` meant nothing
at all.

## the personal-data position

The P.1 criterion is unchanged: *a name its owner put inside a product they
made and sold to the public is published; a pseudonym a third party put inside
a document that is not the product is not.*

These names are in the credit screen of a commercial product, placed there by
the company that made it, shown to every player who reaches the end. They are
published here. Roles and departments are quoted as the product prints them.

## what the roll says

**credit01 — development**

```
PROGRAMMING            KOBUTA
                       MUUMUU
UX DESIGN PRODUCTION   manager TORU YAMAGUCHI
                       senior designer YOICHI TANOUE
                       designer MOEKA SHINOHARA, AOI YOKOTA
ARTWORK TEAM           director SHOEI OKANO
                       illustrator BENGUS
                       title logo designer CHISATO MITA
                       senior manager KAZUYA NURI
                       design specialist KEISUKE MIZUNO
                       creative editor ISAO TOKI
                       NANASE KIKUCHI, CHIEKO MATSUZAKI, SAKI KAWANO
SOUND                  audio director YASUYUKI TSUJINO
                       composer REO URATANI
                       sound designer YASUYUKI TSUJINO
SUBMISSION             release manager KOHEI AKIYAMA
                       submission coordinator MAYUKO ALFONSO
                       NOBUYA YOSHIZUMI, HIROSHI YAMAGUCHI,
                       KOTARO FUJIOKA, KENJI YAMAGUCHI,
                       TAKUYA KAMIURA, TAKEHIRO KIDA
```

**credit02** is marketing and sales: Global Marketing Strategy & Planning,
Promotion Planning, Brand Promotion, Interactive PR, Japan & Asia Marketing,
Online Strategy, Consumer Games Sales, e-Commerce (e-CAPCOM). Roughly thirty
names, all in `FIRSTNAME SURNAME` form.

**credit03** is Special Thanks and Quality Assurance: lead tester, assistant
lead testers, senior testers, compliance testers, network testers, then a
column of around thirty testers.

**credit04** is the top of the production tree:

```
DIRECTOR             TOSHIYUKI YAMAMOTO
PRODUCER             KANSUKE SAKURAI
EXECUTIVE PRODUCER   YOSHINORI ONO
MT FRAMEWORK Development Team
                     MAKOTO KITAMURA, YOJI MIKAMI, miss,
                     SHINGO ITO, KATSUHIKO SOMETANI
PACKAGE & MANUAL     designer NAOYUKI TSUCHIYA, SHINICHIRO KOMIZU, KAW.TLD
```

**credit05** is the legal and third-party page, and it is quoted in full below.

## the two entries under PROGRAMMING

```
PROGRAMMING            KOBUTA
                       MUUMUU
```

Two entries. **Every other person credited anywhere in the roll is printed as
two words, a given name and a surname, in capitals.** These two are single
words. So are `BENGUS` (illustrator), `miss` (in the MT Framework team) and
`KAW.TLD` (package design) — the roll does contain single-token credits
elsewhere, and Japanese credit rolls routinely use handles for individuals.

**So the measurement is a typographic one and it is stated as exactly that:**
the two entries under the heading that names whoever wrote the code are the
only two entries in that section, and neither is in the personal-name form the
rest of the roll uses. Whether they name individuals working under handles, or
outside studios, **is not determinable from the shipped files** — nothing else
in the product expands either token, and this repository does not resolve names
against outside sources.

It is recorded because [12-question.md](12-question.md) asks who wrote the
emulator, and this is the only place in the entire product where the product
itself answers that question. It answers it with two words, and they are the
two words there are.

## the third-party page, which the pre-briefing said did not exist

The pre-briefing recorded, correctly for the executable's strings and
incorrectly for the product:

> NOT PRESENT anywhere readable: any licence text, any "third party" notice,
> any acknowledgements block. **There is no in-binary credits list at all
> beyond zlib.**

`credit05_BM_NOMIP`, 2048×512, reads:

```
(c) HIROSHI MOTOMIYA (c) Thirdline (c) SHUEISHA
                     (c) CAPCOM CO., LTD. 1992, 2018 ALL RIGHTS RESERVED.
(c) [the same line in Japanese]
Networking provided by GGPO
DIGITAL HEARTS Co., Ltd.  [and in Japanese]
Font Design by Fontworks Inc.
Special Thanks: Founder Electronics Corp., Ltd. & Founder International Inc.
Keywords STUDIOS
```

Read against the session's question, the important thing about this page is
**what it credits and what it does not**:

| credited | for |
|---|---|
| GGPO | networking — the rollback netcode |
| Digital Hearts | quality assurance |
| Fontworks | font design |
| Founder Electronics / Founder International | fonts (the Chinese glyph sets) |
| Keywords Studios | localisation |
| Hiroshi Motomiya, Thirdline, Shueisha | the *Tenchi wo Kurau* licence, which is `wof` |
| zlib (in the executable) | compression |

**Nothing on this page credits an emulator, an emulation library, or a CPU
core.** That is a real measurement over a page whose entire purpose is
third-party attribution, and it is a much better measurement than a string
search of ciphertext — but it remains an absence, and
[12-question.md](12-question.md) treats it as one.

The rendered credit textures are not committed. This repository publishes
measurements and the code to remake them, not Capcom's bytes; the five sha1s
are in `notes/sha1-arc-members.txt` and the four commands that reproduce the
images are in [02-datasheet.md](02-datasheet.md).
