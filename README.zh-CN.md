# RetroBoxDB WonderSwan

[English](README.md) | 中文

万代 WonderSwan的单文件 SQLite 保存库。公开的 Catalog 只含元数据（校验值、DAT 与来源记录、头部字段、打包配方和程序），不含 ROM 数据，不能独立恢复文件；完整库保留在本地。

| 项目 | 数值 |
| --- | --- |
| 原始大小 | 源 ZIP 287 个，150.5 MiB（No-Intro 258 个，RetroAchievements 集合 29 个）；解压后 ROM 287 个，383.0 MiB |
| 入库后大小 | 完整库 81.2 MiB；公开 Catalog 3.8 MiB（不含 ROM 数据） |
| 比例 | 完整库为原 ZIP 的 53.9%，为解压后 ROM 总量的 21.2% |
| 使用的技术 | 存储 v4：64 KiB 块按 SHA256 去重，按 No-Intro 游戏族顺序装入最大 256 MiB 的 LZMA2 实体组（字典 256 MiB）；逐块 SHA256、逐对象 CRC32／MD5／SHA1／SHA256 校验；源 ZIP 由 TorrentZip 配方逐字节重建 |
| 导出性能 | Intel(R) Core(TM) i7-8650U CPU @ 1.90GHz，空闲负载，Python 3.14.4，含全部校验。按最新 DAT 整套导出（`export_set.py`，257 个文件，逐个按 DAT 哈希校验）：53.2 MiB/s，平均 23 毫秒／个；单个文件冷缓存（每次清空缓存，需解压所在组的前段）：ROM 平均 2.225 秒，TorrentZip 平均 2.433 秒 |

## 下载与说明

| 文件／文档 | 内容 |
| --- | --- |
| [RetroBoxDB.WonderSwan.Catalog.sqlite](https://github.com/rshi0212/RetroBoxDB-WonderSwan/releases/latest/download/RetroBoxDB.WonderSwan.Catalog.sqlite) | 公开 Catalog（Release 附件，附 `SHA256SUMS`） |
| [存储 v4 说明](RetroBoxDB.Storage-v4.zh-CN.md)／[English](RetroBoxDB.Storage-v4.en.md) | 各平台的存储评估、内容、RA、中文名与维护 |
| [Technical design](RetroBoxDB.Storage-v4.Technical-Design.en.md) | 存储格式、平台适配、增量更新、校验 |
| [RA 清单](reports/ra-ws-games.csv)／[汇总](reports/ra-ws.json)、[构建报告](reports/ws-build-report.json)、[审计处理](reports/audit-resolution-20261004.md) | 逐项数据 |

## 本平台的存储选择与特殊情况

全部本地收藏实测 18 种块／组组合（`assessment/data/storage-experiment-ws.json`）：最小为 256 KiB / 256 MiB 75.63 MiB；按规则（最小值 0.5% 以内选块最小、再选组最小）采用 64 KiB / 256 MiB 75.97 MiB。ZIP 150.46 MiB，逐文件 LZMA 100.84 MiB。

- 页尾：文件最后 16 字节（远跳转、发行商、彩色标志、游戏 ID、版本、容量、存档类型与大小、方向、总线宽度、RTC，以及其余全部字节的 16 位和）存入 `ws_hardware`。WonderWitch 自制软件的页尾是默认值（校验和为 0），校验和告警大多来自这类文件。
- RetroAchievements 把 WonderSwan 和 WonderSwan Color 放在同一个主机（53）和同一个目录下。两个库都导入该目录：本库收 `.ws` 文件，`.wsc` 文件跳过，由 [RetroBoxDB-WonderSwanColor](https://github.com/rshi0212/RetroBoxDB-WonderSwanColor) 收录。RA 报告只统计与本库有关的游戏。

## 内容

| 项目 | 数值 |
| --- | --- |
| ROM 记录／游戏组／发行版本 | 265／228／257 |
| 各版 DAT 覆盖 | 20260525-011654：257/257 |
| 不在任何 DAT 的本地 ROM | 8 |
| RetroAchievements 集合中的 ROM 文件 | DAT 中有 21，仅 RA 收录 8，哈希不在最新 RA 快照 0（[清单](reports/ra-ws-collection-unknown.csv)）；仍缺本地 ROM 的 RA 游戏见 [缺口清单](reports/ra-ws-missing.csv) |
| No-Intro DB Export＋Dump Log unknown | 257 个档案、278 个文件身份、318 条有文档的硬件声明；Dump Log Verified 58 |
| RetroAchievements（console 53） | 有成就的游戏 23 个：本地有 ROM 23（30 个 ROM），ROM 在兄弟库中 0，仅 DAT 有 0，仅 DB 文件 0，无 No-Intro 对应 0 |
| 中文名 | 264 条记录中 148 条有中文（118 个唯一名）；本地 ROM 141 个有中文名 |
| 完整库审计 | 268 个对象、1 个组、269 个 ZIP 配方，全部通过 |

源 ZIP 均可由 TorrentZip 配方逐字节重建（`v_file_checksums.exported_bytes_equal_source`）。

## 使用

```bash
# 用 Catalog 内嵌引擎做只读审计（stats、checksums FILE_ID、help 同理）
python3 -B -c 'import sqlite3,sys; c=sqlite3.connect(sys.argv[1]); s=c.execute("SELECT content FROM resources WHERE name=?",("engine.py",)).fetchone()[0]; c.close(); exec(compile(s,"RetroBoxDB:engine.py","exec"))' ./RetroBoxDB.WonderSwan.Catalog.sqlite audit
# 完整库：按 DAT 版本、1G1R、RA 成就、TorrentZip／裸 ROM 组合导出
python3 -B tools/export_set.py RetroBoxDB.WonderSwan.sqlite OUT --set 1g1r --ra achievements --container torrentzip --layout ra-category
# 增量加入新 DAT、DB Export／Dump Log、ROM 与 RA 快照
python3 -B tools/update_db.py RetroBoxDB.WonderSwan.sqlite --discover --ra --catalog RetroBoxDB.WonderSwan.Catalog.sqlite
```

只需 Python 3.10+ 标准库。`resources` 中的 `engine.py` 等是可执行代码，只应从自己构建或 SHA256 已核对的 Release 附件中执行。发布由 `.github/workflows/publish-catalog.yml` 完成：工作流从 `release/catalog-release.json` 固定的基础 Catalog 出发，注入本仓库提交中的引擎与文档，核对全部数据表摘要、运行测试与审计后发布。
