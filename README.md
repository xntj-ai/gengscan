# GengScan · 耿同学

[![License: MIT](https://img.shields.io/badge/License-MIT-blue.svg)](./LICENSE)
[![Claude Code Skill](https://img.shields.io/badge/Claude%20Code-Skill-d97757.svg)](https://claude.com/claude-code)

**AI-assisted, large-scale first-pass screening for data-integrity fingerprints in scientific papers.**

**用 AI 做学术论文数据完整性的规模化「第一遍」筛查。**

GengScan is a [Claude Code](https://claude.com/claude-code) / Agent skill. It turns a plain field-observed idea — *numeric fabrication tends to leave fingerprints in the data* — into a reusable pipeline: from a list of scholars, to a list of papers, to the Source Data behind them, to the arithmetic fingerprints inside those spreadsheets, and optionally to image forensics. The machine compresses thousands of papers down to a handful of candidates; a human decides what they mean.

GengScan 是一个 [Claude Code](https://claude.com/claude-code) / Agent 技能。它把一条一线观察到的朴素经验 —— **数值造假往往会在数据里留下指纹** —— 工程化成一条可复用的流水线:从一份学者名单,到一份论文清单,到论文背后的 Source Data,再到这些数据表里的算术指纹,以及可选的图像取证。机器把上千篇论文压成几十个候选,定性交给人。

> **Naming · 命名** — `Geng` honors the numeric-fingerprint approach of a front-line research-integrity practitioner; `Scan` denotes large-scale screening. This project is an independent engineering implementation of that idea and **does not imply that person's endorsement**. `Geng` 致敬一线学术打假实践者的数值指纹思路,`Scan` 表规模化筛查。本项目是对该思路的独立工程化实现,**不代表其本人对本项目的背书**。

## Why GengScan · 为什么

Integrity volunteers keep hitting the same wall: fabrication is detectable, but there are too many papers and too few eyes. GengScan moves the bottleneck from *reading every paper* to *reviewing a short candidate list*, so a person spends their scarce judgment only where the data already looks odd.

学术打假志愿者反复撞上同一堵墙:造假能查,但论文太多、人根本看不过来。GengScan 把瓶颈从「逐篇读」搬到「核一份短候选清单」,让人把稀缺的判断力只花在数据本身已经显得反常的地方。

- **The value is triage, not verdicts.** The machine never declares fabrication; it surfaces leads at scale. **价值在分诊,不在判决。** 机器从不宣布造假,它只规模化地把线索摆到人面前。
- **Built to not falsely accuse.** Every detector has known benign look-alikes baked into its gates, because a wrong accusation is far costlier than a missed one. **生来就为「不冤枉人」。** 每个检测器都内置了已知良性「同形」的闸门,因为错误指控的代价远高于漏检。
- **Honest about blind spots.** It states plainly what numeric tools cannot catch, instead of implying full coverage. **诚实交代盲区。** 它明说数值工具碰不到什么,而不是暗示自己无所不查。

## Features · 功能特性

- **Scholar → paper resolution.** Match a Chinese scholar list against OpenAlex with triple anchoring (pinyin name + institution + research field), then ORCID identity check and a per-paper institution gate. **学者 → 论文匹配。** 用三重锚定(姓名拼音 + 依托机构 + 研究领域)把中文学者名单匹配到 OpenAlex,再做 ORCID 核身与逐篇机构门。
- **Robust Source Data fetching.** Proxy-first fetch through anti-scraping walls, archive (`.zip`) extraction, and recording of external-repository links (figshare / Zenodo / Dryad) for human review. **稳健的 Source Data 抓取。** 代理优先穿过反爬墙、解压 `.zip`、记录外部仓库外链(figshare/Zenodo/Dryad)供人审。
- **Four numeric "hard fingerprints".** Column- and row-level detectors (A constant inter-column difference / G block-level decimal lock / F duplicate blocks / H row-level duplicates), each gated against its benign look-alikes. **四类数值「硬指纹」。** 列向与行向检测器(A 两列恒差 / G 块级小数锁定 / F 同图重复块 / H 行级重复),每类都对良性同形设了闸。
- **Omics false-positive gate.** Bulk row duplication typical of omics / bioinformatics tables (abundance matrices, radiomics features, phylogenetic trees, sequencing IDs) is identified and suppressed, so a few real copy-pastes are not buried under hundreds of benign repeats. **组学假阳闸。** 自动识别并抑制组学/生信表里本质性的批量行重复,避免真正的几处 copy-paste 被几百条良性重复淹没。
- **Optional image forensics.** ORB local-feature matching with RANSAC geometry, plus within-image copy-move detection — catches partial / rotated panel reuse that perceptual hashing misses. **可选图像取证。** ORB 局部特征匹配 + RANSAC 几何验证,并支持同图内 copy-move 检测 —— 抓感知哈希漏掉的局部/旋转 panel 复用。
- **Built-in positive control.** A synthetic workbook with planted A/G/F/H signals (and one clean negative-control sheet) lets you verify the detectors fire — and don't false-trigger — after any threshold change. **内置阳性对照。** 一个植入 A/G/F/H 信号(外加一张干净负对照)的合成表,任何阈值改动后都能验证检测器既抓得到、又不误报。

## When to use · 适用场景

Reach for GengScan when you need a scalable first pass over many papers — given a scholar list or a set of DOIs — to surface candidate data anomalies, when you already have Excel Source Data and want to run the numeric fingerprints, or when you want to reproduce / adapt an integrity-screening toolchain. It is **not** for legally characterizing already-confirmed misconduct, and **not** a literature search engine.

当你需要对一批论文(给定学者名单或一组 DOI)做可规模化的第一遍筛查、把候选数据异常摆出来,或者已经有 Excel Source Data 想跑数值指纹,又或者想复现/改造一套学术诚信筛查工具链时,用 GengScan。它**不**用于给已确认的不端做法律定性,也**不**是文献检索引擎。

## Install · 安装

Clone this repository into your Claude Code skills directory, then install the Python dependencies:

把本仓库克隆到你的 Claude Code 技能目录,再装好 Python 依赖:

```bash
git clone https://github.com/xntj-ai/gengscan.git ~/.claude/skills/gengscan
cd ~/.claude/skills/gengscan
pip install -r requirements.txt
```

The numeric core needs only `openpyxl` and `numpy`. Scholar matching additionally uses `requests` and `pypinyin`. Image forensics is optional and pulls in `opencv-python-headless`, `pillow`, `imagehash`, and `pymupdf` — install those only if you run the image module.

数值核心只需 `openpyxl` 和 `numpy`。学者匹配额外用到 `requests` 和 `pypinyin`。图像取证是可选项,依赖 `opencv-python-headless`、`pillow`、`imagehash`、`pymupdf` —— 只在跑图像模块时才需要安装。

## Usage · 用法

**With Claude** — describe what you want, e.g. *"use gengscan to screen the Source Data in this folder for numeric fingerprints, and report confirmed anomalies vs. suspected vs. benign separately."* Claude runs the scripts, reads the candidate output, opens the flagged columns to check semantics, and writes a graded report — never an accusation. **对 Claude 说** —— 描述你的目标,比如「用 gengscan 筛查这个文件夹里 Source Data 的数值指纹,把确认的客观异常 / 存疑 / 良性分开报」。Claude 会跑脚本、读候选输出、开列核对列头语义、写一份分级报告 —— 绝不是指控。

**Run a directory of spreadsheets through the numeric detectors · 对一批 xlsx 跑数值指纹检测:**

```bash
python scripts/forensic.py --indir ./papers --out forensic_report.txt
```

**Verify the detectors work (synthetic positive control) · 验证检测器有效(合成阳性对照):**

```bash
python scripts/make_synthetic.py                       # 生成带植入信号的合成表
python scripts/forensic.py synthetic_positive.xlsx     # 应命中植入的 A/G/F/H 信号,负对照不报
```

**Full pipeline, from a scholar list · 完整流水线(从学者名单开始):**

```bash
# 1) Build the Nature-family source set once · 构建 Nature 系期刊源集(只跑一次)
python scripts/match_scholars.py build-sources --sources nature_sources.json
# 2) Scholar list (name,org[,dept]) -> matched authors + papers · 名单 → 作者+论文
python scripts/match_scholars.py match --input scholars.csv --sources nature_sources.json --out matched.json
# 3) ORCID identity check (in place) · ORCID 核身(原地更新)
python scripts/match_scholars.py orcid --matched matched.json
# 4) Per-paper institution gate for medium matches · 中等可信者逐篇机构门
python scripts/match_scholars.py gate --matched matched.json --sources nature_sources.json
# 5) Fetch Source Data (proxy optional) · 抓 Source Data(代理可选)
GENGSCAN_PROXY=http://HOST:PORT python scripts/fetch_sources.py slug=DOI_OR_URL --outdir ./papers
# 6) Numeric fingerprints · 数值指纹
python scripts/forensic.py --indir ./papers --out forensic_report.txt
# 7) (optional) Image forensics · (可选)图像取证
python scripts/fetch_figures.py --indir ./papers
python scripts/image_forensic.py --indir ./papers
```

> Network fetching is at the mercy of each site's anti-scraping. `fetch_sources.py` supports a proxy (`GENGSCAN_PROXY`), `.zip` extraction, and figshare / Zenodo / Dryad external links. Dryad's proof-of-work wall is handled by `dryad_dl.py`. 抓取受各站点反爬影响。`fetch_sources.py` 支持代理(`GENGSCAN_PROXY`)、`.zip` 解压、figshare/Zenodo/Dryad 外链。Dryad 的工作量证明墙由 `dryad_dl.py` 处理。

## Methodology · 方法论

GengScan's design philosophy: **the machine collapses thousands of papers into dozens of candidates; the human characterizes each candidate.** Every fingerprint family has a legitimate "same-shape" explanation — which is exactly why a person must look. Full write-up in [`docs/methodology.md`](docs/methodology.md).

GengScan 的设计哲学:**机器把上千篇论文压成几十个候选,人来定性每一个。** 每一类指纹都有合法的「同形」解释 —— 这正是必须人看的原因。完整论述见 [`docs/methodology.md`](docs/methodology.md)。

The four hard fingerprints, with the benign look-alikes each one must rule out:

四类硬指纹,以及每类必须排除的良性同形:

| Fingerprint 指纹 | Signal 判据 | Must exclude · 必排除的良性解释 |
|---|---|---|
| **A · Constant inter-column difference** 两列恒差 | Two columns differ by a fixed non-zero constant. 两列之差恒为非零常数。 | Absolute-vs-relative derived values, coordinates (e.g. 1 bp start/end), stacked-plot offsets, background subtraction. 绝对/相对派生值、坐标(如 1bp 起止)、堆叠绘图偏移、背景扣除。 |
| **G · Block-level decimal lock** 块级小数锁定 | A block shares decimal parts while integer parts differ ("kept decimals, changed integers"). 一块区域小数位相同、整数不同。 | Coarse-grid data (all `.0` / `.5`) where misaligned columns coincide by nature — gated by requiring ≥6 distinct decimals, dominant <60%. 粗网格数据错位天然重合 —— 闸:小数须 ≥6 种且主导 <60%。 |
| **F · Duplicate blocks** 同图重复块 | An identical numeric sequence appears across columns (copy-paste). 同一序列跨列精确相同。 | Shared monotonic axes, baseline-zero columns — gated by excluding monotonic and ≥40%-zero columns. 共享单调坐标轴、基线零列 —— 闸:排除单调列与 ≥40% 为 0 的列。 |
| **H · Row-level duplicates** 行级重复 | A whole measurement row is copied to another sample / species row. 整行测量被复制到另一个样本行。 | Omics / bioinformatics tables where identical rows are the pipeline's nature — gated by an omics suppressor and a requirement that the copy spans ≥2 distinct sample labels. 组学/生信表行雷同是本质 —— 闸:组学抑制器 + 要求跨 ≥2 个不同样本标签。 |

The scanner also computes softer column signals (terminal-digit lock, decimal-tail repetition, in-column arithmetic progression, single-value domination). These share their shape with benign mechanisms (rounding, averaging, on-figure digitization), so they are kept conservative and treated as leads for human reading, not auto-list verdicts.

扫描器还会计算一些更弱的列信号(末位锁定、小数末位雷同、列内等差、单值碾压)。它们与良性机制(四舍五入、取均值、图上数字化)同形,因此保持保守,只作为供人阅读的线索,不进自动榜定论。

## Limitations · 局限与盲区

- **Only covers papers with downloadable structured data tables.** Old papers, PDF-only supplements, and theory papers have no data table and are out of reach. **只覆盖能下载到结构化数据表的论文。** 老论文、纯 PDF 补充材料、理论文没有数据表,够不着。
- **Fabrication types the numeric tools cannot reach:** image PS / splicing (western blots), fill-type, terminal-digit-type, selective outlier removal, and row-order manipulation — they are either not in the numeric table or are indistinguishable from normal data. **数值工具碰不到的造假类型:** 图像 PS/拼接(western blot)、填充型、末位型、选择性剔除离群点、行序型 —— 要么不在数值表里,要么与正常数据同形。
- **Coverage must be counted by data actually fed to the detectors, not papers attempted.** A paper with no fetched data is not a clean paper. **覆盖率要按「实际喂进检测器的数据」算,不是「跑过的篇数」。** 一篇没抓到数据 ≠ 这篇干净。
- **Characterization always requires a human.** The machine only provides clues; whether something is *misconduct* must be settled by author explanation and institutional investigation. **定性永远要人。** 机器只给线索;是否「学术不端」须由作者解释与机构调查认定。

## ⚠️ Disclaimer · 免责声明

> **A GengScan hit is a "candidate awaiting human verification", not a "finding of fabrication".** A fingerprint match can have a perfectly legitimate explanation (derived columns, shared axes, bulk omics data, and more). **Any allegation must go through human verification, the authors' explanation, and an institutional investigation.** Users bear sole responsibility for any action taken on this basis; the authors are not liable for misuse, misjudgment, or any resulting consequences. This project is for **defensive** research-integrity work and method sharing — use it responsibly, and never use a machine hit to pin a fabrication label on a named scholar.
>
> **GengScan 的命中是「待人核实的候选」,不是「造假认定」。** 指纹命中可能有完全合理的解释(派生列、共享坐标轴、组学批量数据等)。**任何指控都必须经过人工核实、作者解释与机构调查。** 使用者须自行承担据此采取行动的责任;作者不对误用、误判或由此产生的任何后果负责。本项目用于学术诚信的**防御性**研究与方法分享,请负责任地使用,**绝不**据机器命中给实名学者扣造假帽。

## Examples · 示例

The fastest way to see GengScan honestly is the synthetic positive control. `make_synthetic.py` builds one workbook in which each sheet plants exactly one fabrication pattern, plus a `NEG_normal` sheet of ordinary noisy measurement that must stay silent:

最快又诚实地看懂 GengScan 的方式是合成阳性对照。`make_synthetic.py` 造一个工作簿,每张表恰好植入一种造假形态,外加一张 `NEG_normal` 正常带噪测量表,它必须保持沉默:

```bash
python scripts/make_synthetic.py                       # -> synthetic_positive.xlsx
python scripts/forensic.py synthetic_positive.xlsx --out demo_report.txt
# Expect hits on the planted sheets (constant +0.3 difference, last-digit lock,
# decimal-tail repetition, value domination); NEG_normal stays clean.
# 预期:植入表命中(恒 +0.3 差、末位锁定、末两位雷同、单值碾压);NEG_normal 不报。
```

For batch auditing across many paper folders, `audit_dir.py` runs `forensic.py` on one directory per subprocess (with a size gate and a parent-side timeout), so a single pathological 100 MB+ matrix that hangs the parser kills only its child instead of freezing the whole run.

要跨大量论文文件夹批量审计,`audit_dir.py` 以「每目录一个子进程」的方式跑 `forensic.py`(带文件大小门 + 父进程超时),这样某个让解析器卡死的 100 MB+ 异常矩阵只拖死它自己的子进程,而不冻结整批运行。

## How it works · 技术原理

The pipeline is four stages, each a standalone script you can run on its own:

流水线分四段,每段都是可独立运行的脚本:

```
  match_scholars   →   fetch_sources   →   forensic   →   image_forensic
  scholars→papers      fetch Source Data   numeric          image forensics
  学者名单匹配         抓 Source Data       指纹检测         (optional·可选)
  OpenAlex triple      proxy + zip +        A/G/F/H hard     ORB + RANSAC +
  anchoring·三重锚定   external links·外链  fingerprints     copy-move
```

`match_scholars.py` resolves a Chinese scholar list to OpenAlex authors and their Nature-family papers via three-way anchoring (pinyin name + CN institution id + field), then verifies identity through ORCID and, for medium-confidence matches, keeps only papers where the author was affiliated with the institution *on that paper*. `forensic.py` reads every sheet with `openpyxl`, excludes scan-axis / independent-variable columns to cut false positives, runs the A–H detectors with their benign-look-alike gates, and writes a graded candidate list. `image_forensic.py` extracts ORB keypoints, matches them with a brute-force matcher + Lowe ratio + RANSAC homography, requires a high inlier ratio for a real copy, filters out "star images" (generic elements matching many papers), and detects within-image copy-move.

`match_scholars.py` 用三重锚定(姓名拼音 + 中国机构 id + 领域)把中文学者名单匹配到 OpenAlex 作者及其 Nature 系论文,再用 ORCID 核身;对中等可信匹配,只采信「该作者在那篇论文上确实挂靠依托单位」的论文。`forensic.py` 用 `openpyxl` 读每张表,先排除扫描轴/自变量列以降假阳,再跑 A–H 检测器及其良性同形闸门,输出分级候选清单。`image_forensic.py` 提取 ORB 关键点,用暴力匹配 + Lowe ratio + RANSAC 单应验证,要求高 inlier 占比才算真复制,剔除「明星图」(和很多篇都匹配的通用图元),并检测同图内 copy-move。

## FAQ · 常见问题

**Does a hit mean a paper is fabricated?** No. A hit is a candidate for human review. Many fingerprints have legitimate explanations; characterization needs a person reading the columns in their experimental context. **命中就代表造假吗?** 不。命中是待人核实的候选。许多指纹有合法解释;定性需要人结合实验语境读列。

**Why only four fingerprints when the code computes more?** The scanner also runs softer signals (terminal-digit, decimal-tail, arithmetic progression, value domination), but those overlap too much with benign mechanisms to put on an auto-list, so the headline set is the four hard fingerprints. **代码算了更多,为什么只讲四类?** 扫描器也跑更弱的信号(末位、小数末位、等差、单值碾压),但它们与良性机制重叠太多,不宜进自动榜,所以头牌是四类硬指纹。

**Why does it focus on omics false positives so much?** Because in omics / bioinformatics tables, identical rows are the nature of the computation, not fabrication — without an omics gate, a few real copy-pastes would drown under hundreds of benign repeats. **为什么这么在意组学假阳?** 因为在组学/生信表里,行雷同是计算的本质而非造假 —— 没有组学闸,真正的几处 copy-paste 会被几百条良性重复淹没。

**Can I run it without Claude?** Yes. Every script has a CLI and runs standalone; Claude is a convenience for orchestrating the pipeline and writing the graded report. **不用 Claude 能跑吗?** 能。每个脚本都有 CLI、可独立运行;Claude 只是编排流水线、写分级报告的便利。

**Do I need a proxy?** Only for fetching. OpenAlex / ORCID are reached directly; publisher Source Data is often behind anti-scraping, so `fetch_sources.py` tries the proxy first when `GENGSCAN_PROXY` is set. **需要代理吗?** 只在抓取时。OpenAlex/ORCID 直连;出版商 Source Data 常有反爬,设了 `GENGSCAN_PROXY` 时 `fetch_sources.py` 会代理优先。

## Related · 相关

- [cross-review](https://github.com/xntj-ai/cross-review) — multi-model adversarial review; pair it with GengScan to have several models sanity-check a graded candidate report before it goes anywhere. 多模型交叉审查;和 GengScan 搭配,让多个模型在分级候选报告外发前先互审一遍。
- [ppvi](https://github.com/xntj-ai/ppvi) — the light visual identity GengScan's docs site is built on. GengScan 文档站所沿用的浅色视觉体系。
- [xntj.tv](https://xntj.tv) — more Claude Code workflows and skills from 张拼拼 · XNTJ. 更多来自张拼拼·XNTJ 的 Claude Code 工作流与技能。

## License · 许可证

[MIT](./LICENSE) © [张拼拼 · XNTJ](https://xntj.tv)
