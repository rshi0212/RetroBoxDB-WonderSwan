WonderSwan Catalog, storage v4 (64 KiB blocks, 1 solid LZMA2 group of up to 256 MiB). Metadata only: **no ROM payloads are published**; `compression_groups`, `chunks` and `object_chunks` are empty.

- One schema for all twenty-three platforms: the header tables of the eight new platforms (Game Gear, PC Engine, SuperGrafx, MSX, MSX2, Virtual Boy, Game & Watch, Super A'Can: `pce_hardware`, `msx_hardware`, `vb_hardware`) and `rom_annotations` exist in every Catalog; tables of other platforms and provider tables have no rows.
- RetroAchievements reports look up sibling databases (NES<->FDS, SNES<->Satellaview, WonderSwan<->WonderSwan Color, NeoGeo Pocket<->NeoGeo Pocket Color, PC Engine<->SuperGrafx, MSX<->MSX2): a game whose ROM is stored there is `local_other_platform`, not a gap.
- Source: 287 ZIPs (nointro 258, retroachievements 29), 150.5 MiB (287 ROM files, 383.0 MiB uncompressed). Populated database: 81.4 MiB (54.1% of the ZIPs). All source ZIPs are reproduced byte-for-byte.
- Contents: 265 ROM records, 228 games, 257 releases; DAT versions: 20260525-011654.
- RetroAchievements: 23 of 23 games with achievements have a local ROM.
- Export (Intel(R) Core(TM) i7-8650U CPU @ 1.90GHz, idle, all checks): whole newest-DAT set with export_set.py 53.2 MiB/s (257 files); single file with a cold cache 2.225 s (ROM) / 2.433 s (TorrentZip) on average.
- Full audit of the populated database: 268 objects, 1 group, 269 archive plans, no errors.

The release workflow starts from the base Catalog pinned by SHA256 in `release/catalog-release.json`, injects the engine and documents of the tagged commit, checks every data-table digest, SQLite integrity and foreign keys, runs the Catalog audit and the repository tests. Verify the download with `SHA256SUMS`.

[中文说明](https://github.com/rshi0212/RetroBoxDB-WonderSwan/blob/main/README.zh-CN.md)
