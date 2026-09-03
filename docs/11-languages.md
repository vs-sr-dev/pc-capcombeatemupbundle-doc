# 11 — eight languages, and the gallery captions are the biggest part of them

*Measure: `python tools/gmd.py --validate` over the eight `rGUIMessage`
members of `game_win.arc`. 932 strings each, 8 of 8 validate.*

## the GMD format

```
+0   4    'GMD\0'
+4   u32  version -- 0x00010302 on 8 of 8
+8   u32  language index
+12  u64  unresolved
+20  u32  key count
+24  u32  string count
+28  u32  size of the key block
+32  u32  size of the string block
+36  u32  length of the table's own name, then the name, NUL-terminated
```

then per-entry records, the key block, and finally the string block: `count`
NUL-terminated UTF-8 strings back to back, the last of which ends at
end-of-file.

**The accounting test:** the number of NUL-terminated strings found in the last
`string block size` bytes must equal the declared string count. It does, **8 of
8**, 932 of 932 every time. Negative control: `tools/gmd.py --validate` on the
neighbouring `rGUIFont` member reports `NOT-GMD magic b'GFD\x00'` and exits 1.

## the eight

| member | lang index | string block | 
|---|---:|---:|
| `menu_jpn` | 0 | 38,867 |
| `menu_eng` | 1 | 29,328 |
| `menu_fre` | 2 | 34,923 |
| `menu_spa` | 3 | 35,838 |
| `menu_ger` | 4 | 33,590 |
| **`menu_ita`** | **5** | **34,477** |
| `menu_chT` | 7 | 29,400 |
| `menu_chS` | 8 | 28,067 |

**Eight, not seven.** The pre-briefing listed seven menu fonts and missed
`menu_chS`; there are eight message tables and eight matching font entries.

**Language index 6 is not present.** The numbering runs 0,1,2,3,4,5,7,8 with a
hole where a ninth language would have sat between Italian and Traditional
Chinese. Something was numbered and not shipped. What it was is not
determinable from these files.

## Italian, since the previous session spent a day on the subject

The last object this collection measured was an Italian pressing that contained
no Italian text at all, because its localisation had been painted into bitmaps.
This one is the opposite in every respect: 932 strings, 33,440 characters, 930
of them non-empty, in UTF-8, in a table whose only Italian-specific asset is
the message file itself. The font atlas is shared.

```
  10  eng=Japanese                ita=Giapponese
  11  eng=English                 ita=Inglese
  12  eng=French                  ita=Français
  13  eng=Italian                 ita=Italiano
  16  eng=Traditional Chinese     ita=Cinese tradizionale
```

Note entry 12: the Italian table renders French as `Français` and German as
`Deutsch`, i.e. each language is named in itself, while English names them all
in English. That is a deliberate localisation convention, not a bug, and it is
visible only because both tables were read side by side.

A large share of the 932 entries are **gallery captions** — `Grafica delle
animazioni di gioco`, `Documenti degli appunti di gioco` — which is consistent
with [09-textures.md](09-textures.md): the biggest thing in the product needs
the most words to describe it.

## the front end this belongs to

`game_win.arc` holds 145 members and is the entire shell: title, options,
controller and keyboard setup, main menu, game select, pause, how-to-play,
gallery menu, credit, the eight fonts, the eight message tables, fourteen sound
effects, and `ui\1_menu\14_netWork\` with `lobby`, `room_select` and
`session_list`. Those, plus `nNetwork::SessionDriver` and `192.168.0.%d` in
`.rdata` and `WS2_32.dll` with 21 imports, say there is online play — which
[10-credits.md](10-credits.md) shows is credited to GGPO. Nothing else about
the networking was measured.
