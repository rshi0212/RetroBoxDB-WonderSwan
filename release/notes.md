WonderSwan Catalog, storage v4 (64 KiB blocks, 1 solid LZMA2 group of up to 256 MiB). Metadata only: **no ROM payloads are published**; `compression_groups`, `chunks` and `object_chunks` are empty.

- First release: WonderSwan cartridges (16-byte footer) from the No-Intro folder and the `.ws` files of the shared RetroAchievements WonderSwan set; 1 Parent-Clone DAT version, DB Export and Dump Log unknown, RetroAchievements snapshot and Chinese names.
- Storage measured on the whole collection: 64 KiB blocks, 256 MiB groups (`assessment/data/storage-experiment-ws.json`).
- RetroAchievements lists both platforms of the pair under one console and one folder; each database keeps its own files and looks up its sibling (WonderSwan<->WonderSwan Color).
- Source: 287 ZIPs (nointro 258, retroachievements 29), 150.5 MiB (287 ROM files, 383.0 MiB uncompressed). Populated database: 81.2 MiB (53.9% of the ZIPs). All source ZIPs are reproduced byte-for-byte.
- Contents: 265 ROM records, 228 games, 257 releases; DAT versions: 20260525-011654.
- RetroAchievements: 23 of 23 games with achievements have a local ROM.
- Chinese names: 148 of 264 CSV rows translated; 141 local ROMs with Chinese names.
- Export (Intel(R) Core(TM) i7-8650U CPU @ 1.90GHz, idle, all checks): whole newest-DAT set with export_set.py 53.2 MiB/s (257 files); single file with a cold cache 2.225 s (ROM) / 2.433 s (TorrentZip) on average.
- Full audit of the populated database: 268 objects, 1 group, 269 archive plans, no errors.

The release workflow starts from the base Catalog pinned by SHA256 in `release/catalog-release.json`, injects the engine and documents of the tagged commit, checks every data-table digest, SQLite integrity and foreign keys, runs the Catalog audit and the repository tests. Verify the download with `SHA256SUMS`.

[中文说明](https://github.com/rshi0212/RetroBoxDB-WonderSwan/blob/main/README.zh-CN.md)
