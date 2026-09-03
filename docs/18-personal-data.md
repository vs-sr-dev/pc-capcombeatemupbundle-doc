# 18 — personal data: the scanner found nothing and the product names a hundred people

*Measure: `python tools/contacts.py` over 268 files and 809,632,531 bytes —
5 raw occurrences, 5 noise, 0 published, 0 telephone patterns. Then five
textures decoded and read.*

## the criterion, unchanged

> A name its owner put inside a product they made and sold to the public is
> published. A pseudonym a third party put inside a document that is not the
> product is not.

## what the scanners say

| scan | result |
|---|---|
| `contacts.py`, 268 files, 809,632,531 bytes | 5 raw, 5 distinct, **5 noise, 0 published**, 0 telephone patterns |
| `paths.py` | 6 drive-lettered paths raw, five of them 3–4 character fragments inside compressed texture data, **1 real** |
| `protscan.py` | 0 hits on every copy-protection marker; 2 on the positive control |

The one real path is `C:\capdev_ibis\BSAC\buildout\MasterReleaseDX11x64\output\
XBSACMasterReleaseDX11.pdb`. **It names a machine directory and a project, not
a user.** For the collection's absolute-path table, this object's honest count
is **1, over 28.59 % coverage** — the first row in that table with a coverage
figure attached, because 71.41 % of the executable cannot be searched.

## and what the product says

The credit roll, five BC7 textures in `game_win.arc`, names **more than a
hundred people** by first name and surname, with roles: programming, UX design,
artwork, sound, submission, marketing, sales, QA, director, producer, executive
producer, MT Framework development, package and manual design.

Every one of them satisfies P.1 exactly: a name its owner put inside a product
they made and sold, displayed to any player who reaches the credits. They are
published in [10-credits.md](10-credits.md), with the roles the product prints
beside them.

**`contacts.py` is not broken.** It scanned 809,632,531 bytes correctly and
found what is there in text, which is nothing. The names are pixels. This is
the second consecutive session in which that has happened, and the second
consecutive one in which the tool's zero was nearly published as a measurement
of the product rather than of the file bytes.

The standing note for the next session, which now has two data points:

> **On any object with a GUI, `contacts.py` returning zero is a result about
> the text and says nothing about the product until the images have been
> decoded.** Both objects that produced this outcome had a credit resource
> visible in a file listing, and in both cases opening it took under an hour.

## what is not published

**The two entries under PROGRAMMING** — `KOBUTA` and `MUUMUU` — are printed in
[10-credits.md](10-credits.md) because they are in the product's credit roll,
and they are printed *as tokens*, with the observation that they are not in the
personal-name form the rest of the roll uses. This repository does not resolve
them against outside sources, does not assert whether they name individuals or
organisations, and draws no conclusion about anyone's conduct from them.

**The Steam manifest's owner fields.** `appmanifest_885150.acf` contains the
account id that owns the licence and the timestamp of the last play session.
Both belong to this machine's owner rather than to the object, and neither
appears in this repository. The build fields from the same file — app id, depot
id, build id, size, language — are in [13-provenance.md](13-provenance.md).

**No user data was searched for.** Save files go to `savedata.bin` outside this
folder and were not sought. `cSavedata`, `sSavedata` and `uUiSaveAccess` are in
`.rdata`; nothing else about them was measured.

## the question, and the person

[12-question.md](12-question.md) asks who wrote an emulator, and that question
can end in an assertion about a *company*. It does not end in an assertion about
a person, and this repository's answer — **not determinable from the shipped
files** — is stated about engineering, not conduct.

No individual is named in connection with the question. No finding of licence
infringement is published in either direction. The measurements are published
so somebody with the missing piece can close it.
