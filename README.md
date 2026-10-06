# RetroBoxDB WonderSwan

English | [中文说明](README.zh-CN.md)

Single-file SQLite preservation database for Bandai WonderSwan. The public Catalog holds metadata only (checksums, DAT and provenance records, header fields, archive recipes and the processing code); it contains no ROM data and cannot restore files. The populated database stays local.

| Item | Value |
| --- | --- |
| Original size | 287 source ZIPs, 150.5 MiB (No-Intro 258, RetroAchievements sets 29); 287 ROM files, 383.0 MiB uncompressed |
| Stored size | populated database 81.4 MiB; public Catalog 4.0 MiB (no ROM data) |
| Ratio | 54.1% of the source ZIPs, 21.3% of the uncompressed ROM files |
| Technology | storage v4: SHA256-deduplicated 64 KiB blocks packed in No-Intro family order into solid LZMA2 groups of up to 256 MiB (256 MiB dictionary); per-block SHA256 and per-object CRC32/MD5/SHA1/SHA256 verification; source ZIPs reproduced byte-for-byte from TorrentZip plans |
| Export performance | Intel(R) Core(TM) i7-8650U CPU @ 1.90GHz, idle, Python 3.14.4, all checks included. whole newest-DAT set with `export_set.py` (257 files, each checked against the DAT hashes): 53.2 MiB/s, 23 ms per file on average; single file with a cold cache (the group is decoded up to the file): ROM 2.225 s, TorrentZip 2.433 s on average |

## Downloads and documents

| File / document | Content |
| --- | --- |
| [RetroBoxDB.WonderSwan.Catalog.sqlite](https://github.com/rshi0212/RetroBoxDB-WonderSwan/releases/latest/download/RetroBoxDB.WonderSwan.Catalog.sqlite) | Public Catalog (Release asset with `SHA256SUMS`) |
| [Storage v4 guide](RetroBoxDB.Storage-v4.en.md) / [中文](RetroBoxDB.Storage-v4.zh-CN.md) | Storage evaluation, contents, RA, names and maintenance for every platform |
| [Technical design](RetroBoxDB.Storage-v4.Technical-Design.en.md) | Storage format, platform adapters, incremental updates, verification |
| [RA list](reports/ra-wswan-games.csv) / [summary](reports/ra-wswan.json), [build report](reports/wswan-build-report.json), [audit resolution](reports/audit-resolution-20261004.md) | Detailed data |

## Storage choice and platform specifics

18 block/group combinations measured on the whole local collection (`assessment/data/storage-experiment-wswan.json`): smallest 256 KiB / 256 MiB at 75.63 MiB; by the rule (within 0.5% of the smallest, the smallest block, then the smallest group) 64 KiB / 256 MiB at 75.97 MiB. ZIPs 150.46 MiB, per-file LZMA 100.84 MiB.

- Footer: the last 16 bytes (far jump, publisher, colour flag, game id, version, ROM size, save type and size, orientation, bus width, RTC and the 16-bit sum of all other bytes) are stored in `ws_hardware`. WonderWitch homebrew carries a default footer with checksum 0, which accounts for most checksum warnings.
- RetroAchievements lists WonderSwan and WonderSwan Color under one console (53) and one folder. Both databases import that folder; this one keeps `.ws` files and skips `.wsc` files, which [RetroBoxDB-WonderSwanColor](https://github.com/rshi0212/RetroBoxDB-WonderSwanColor) holds. The RA report covers only games tied to this database.

## Contents

| Item | Value |
| --- | --- |
| ROM records / games / releases | 265 / 228 / 257 |
| DAT coverage per version | 20260525-011654: 257/257 |
| Local ROMs in no DAT | 8 |
| ROM files of the RetroAchievements set | in a No-Intro DAT 21, RA only 8, hash not in the latest RA snapshot 0 ([list](reports/ra-wswan-collection-unknown.csv)); RA games still without a local ROM: [gap list](reports/ra-wswan-missing.csv) |
| No-Intro DB Export + Dump Log 20260525-011654 | 257 archives, 278 file identities, 318 documented hardware assertions; Dump Log Verified 58 |
| RetroAchievements (console 53) | 23 games with achievements: 23 with a local ROM (30 ROMs), 0 with the ROM in a sibling database, 0 DAT only, 0 DB file only, 0 without a No-Intro counterpart |
| Chinese names | 148 of 264 rows translated (118 unique); 141 local ROMs have a Chinese name |
| Populated-database audit | 268 objects, 1 groups, 269 archive plans, all passed |

Every source ZIP is reproduced byte-for-byte from its TorrentZip plan (`v_file_checksums.exported_bytes_equal_source`).

## Usage

```bash
# Query-only audit with the Catalog's embedded engine (also: stats, checksums FILE_ID, help)
python3 -B -c 'import sqlite3,sys; c=sqlite3.connect(sys.argv[1]); s=c.execute("SELECT content FROM resources WHERE name=?",("engine.py",)).fetchone()[0]; c.close(); exec(compile(s,"RetroBoxDB:engine.py","exec"))' ./RetroBoxDB.WonderSwan.Catalog.sqlite audit
# Populated database: export by DAT version, 1G1R, RA achievements, TorrentZip or plain ROMs
python3 -B tools/export_set.py RetroBoxDB.WonderSwan.sqlite OUT --set 1g1r --ra achievements --container torrentzip --layout ra-category
# Add new DATs, DB Export / Dump Log snapshots, ROMs and an RA snapshot incrementally
python3 -B tools/update_db.py RetroBoxDB.WonderSwan.sqlite --discover --ra --catalog RetroBoxDB.WonderSwan.Catalog.sqlite
```

Python 3.10+ standard library only. `engine.py` and the other `resources` entries are executable code; run them only from a database you built or a Release asset whose SHA256 you verified. Releases are produced by `.github/workflows/publish-catalog.yml`: it starts from the base Catalog pinned in `release/catalog-release.json`, injects the engine and documents of this commit, checks every data-table digest, runs the tests and the audit, then publishes.
