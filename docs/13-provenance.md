# 13 — provenance: the version was not undeclared, it was just outside the folder

*Measure: `I:\SteamLibrary\steamapps\appmanifest_885150.acf`, read-only, one
directory above the install. Cross-checked against two strings inside the
executable.*

## the gap, as it stood

The pre-briefing recorded this object's equivalent of "no chain of custody":

> NOBODY HAS RECORDED THE BUILD ID. The Steam depot manifest, the app id and
> the branch are all outside this folder and none of them was written down. If
> the session can find them without executing anything, it should; if not, it
> must say the version is undeclared.

It can be found without executing anything. It is a text file two levels up.

## the manifest

```
appid            885150
name             Capcom Beat 'Em Up Bundle
installdir       CBEUB
buildid          11421272
SizeOnDisk       809632531
LastUpdated      1690779623        = 2023-07-31 UTC
InstalledDepots  885151  manifest 2548830031298218182  size 809632531
SharedDepots     228990 -> 228980
MountedConfig    language italian
```

Two fields of that file are personal to this machine's owner — the account id
that owns the licence and the timestamp of the last play session — and neither
is reproduced here or anywhere in this repository. Everything above describes
the build, not the buyer.

## the cross-check, which is the point

A manifest sitting beside an install proves the two were downloaded together
and nothing more. Three independent confirmations tie it to these bytes:

1. **`SizeOnDisk` is 809,632,531** — byte for byte the total this session
   measured by walking the tree and hashing 268 files. Steam's own accounting
   and `hashall.py` agree exactly;
2. **the executable carries the ids.** `CBEUB.exe`'s resource directory holds
   two icon groups named `CLASS_885150` and `CLASS_885151`
   ([03-executable.md](03-executable.md)) — the app id and the depot id, built
   into the binary at compile time. The manifest's `appid` and its one
   `InstalledDepots` entry are those two numbers;
3. **`LastUpdated` is 2023-07-31**, which is the mtime of `CBEUB.exe` on this
   machine, and the `.exe`'s own PE link timestamp is **2022-04-18**. Those are
   three different clocks measuring three different events — depot publication,
   file write, and link — and they are consistent with each other in the right
   order.

## so the version is declarable

| | |
|---|---|
| Steam app | **885150** |
| depot | **885151** |
| build id | **11421272** |
| depot manifest id | 2548830031298218182 |
| branch | none recorded — the manifest names no beta, so this is the public branch |
| depot published | 2023-07-31 |
| executable linked | 2022-04-18 06:32:27 UTC |
| product version, from `VS_VERSIONINFO` | **1.0.0.2** |
| shared depot | 228990 (Steamworks common redistributables) |
| install language | italian |

**This is the first object in this branch that has a real version declaration
rather than a description of one.** Every disc-based object in the collection
carries a dump whose provenance is somebody's word; this one carries a build id
issued by the distributor, confirmed by two numbers compiled into the binary
and by a byte count that matches to the byte.

## what is still not declared

* **which of the seven games' data came from which board revision.** The ROMs
  carry their own build banners — 91-08-05 for `kod`, ` version 1.05A /CPS2
  1993 / JUNE ` for `armwar` — but nothing says which physical board Capcom
  dumped or when;
* **whether this build differs from the 2018 release.** Product version 1.0.0.2
  and a 2022 link date suggest at least one patch after launch, and there is
  nothing in the install to compare against;
* **the `installdir` is `CBEUB` and the codename is `ibis`.** The manifest uses
  neither `ibis` nor `BSAC`. Those two live only inside the product.
