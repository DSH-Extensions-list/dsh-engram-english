<div align="center">

<img src="docs/screenshots/hero.jpg" width="100%" alt="dsh-engram · Memory Palace — DeepSeek Harness 跨Session长期Memory插件（Location当索引 · Pin路线定顺序 · 骨架长期复用 · 标Memory独一none二）">

# dsh-engram · Memory Palace

<p align="center"><b>Local-first long-term memory for your coding agent.</b><br>
纯本地 SQLite 的跨Session长期Memory：<b>零外部服务 · 零 API Key · 零 Docker</b>，一条命令装完即用。<br>
23窄ParametersTools · 混合Retrieval（FTS5 + 本地向量 RRF）· 证据门 · 全链路审计 · SM-2 SpacerDuplicatereview · Panel中英俄文。</p>

<p align="center">
  <a href="https://github.com/kenz1117/dsh-engram/blob/main/LICENSE"><img alt="GitHub license" src="https://img.shields.io/github/license/kenz1117/dsh-engram"></a>
  <a href="https://github.com/kenz1117/dsh-engram"><img alt="GitHub last commit" src="https://img.shields.io/github/last-commit/kenz1117/dsh-engram"></a>
  <a href="https://github.com/kenz1117/dsh-engram/actions/workflows/checks.yml"><img alt="CI" src="https://github.com/kenz1117/dsh-engram/actions/workflows/checks.yml/badge.svg"></a>
  <a href="https://www.npmjs.com/package/@kenz1117/dsh-engram"><img alt="npm version" src="https://img.shields.io/npm/v/@kenz1117/dsh-engram"></a>
  <a href="https://www.npmjs.com/package/@kenz1117/dsh-engram"><img alt="npm downloads" src="https://img.shields.io/npm/dm/@kenz1117/dsh-engram"></a>
  <a href="https://github.com/kenz1117/dsh-engram/issues"><img alt="GitHub issues" src="https://img.shields.io/github/issues/kenz1117/dsh-engram"></a>
  <a href="https://github.com/kenz1117/dsh-engram/graphs/contributors"><img alt="GitHub contributors" src="https://img.shields.io/github/contributors/kenz1117/dsh-engram"></a>
  <a href="https://awesome-dsh-plugin.com"><img alt="Awesome DSH Plugin" src="https://awesome-dsh-plugin.com/badge.svg"></a>
</p>

Chinese | [English](README.en.md)

</div>

<details>
<summary><b>目录</b></summary>

- [快速On始](#快速On始)
- [为什么yes它](#为什么yes它)
- [Memory怎么流动](#Memory怎么流动)
- [界面速览](#界面速览)
- [宫殿结构：目录即Room，路径即路线](#宫殿结构目录即Room路径即路线)
- [特性](#特性)
- [Tools（23，窄Parameters）](#Tools23-个窄Parameters)
- [配置](#配置)
- [工作原理](#工作原理)
- [Web 管理Panel](#web-管理Panel)
- [On发](#On发)
- [Model Experience](#model-experience)
- [Known Limitations and Deferred Work](#known-limitations-and-deferred-work)
- [路线图](#路线图)
- [致谢](#致谢)
- [License](#License)

</details>

---

## 快速On始

```sh
dsh plugin --profile web add @kenz1117/dsh-engram
```

Install后none需配置即可使用（默认分Store与ModelCacheIn `~/.dsh/engram`，Image注入Turn on，Automatic摄取Close）。

**让 agent 自己装**——把下面这段话贴给任意 agent：

```text
请Run这条命令Install dsh-engram（DeepSeek Harness 的跨Session长期Memory插件）：
  dsh plugin --profile web add @kenz1117/dsh-engram
装完后Confirm插件中心出现「Memory Palace」侧栏入口，并在新Session里用 engram_stats
Confirm已注册 23 engram_* Tools。
```

## 升级

- **用哪个入口装，就用哪个入口升**：市场里装的就一直在市场里Update；命令行 `dsh plugin add` 装的就Continue用命令行。两边混用会碰到市场的跨Source保护——它提示「先Uninstall，再从市场Install」时照做即可，这yes防止把不同Source的同名插件互相覆盖的Design，memories据在Data目录里，不受Uninstall影响。
- **Install或Update时若出现「允许构建脚本并Try again」**：pnpm 默认拦截依赖的构建脚本，本插件依赖树里唯一带构建脚本的yes嵌入引擎的原生件（onnxruntime-node）——它只downloads可选的 CUDA 加速件，插件实际使用的 CPU/DirectML 二进制随包分发。**点了能过；不点通常也不影响使用**。
- **ModelCache跨升级持久**（`<dbDir>/models`），升级不DuplicatedownloadsModel；0.13.6 起Data目录默认Follow `$DSH_HOME`，换盘搬家不再把Store留在旧盘。
- **弱网Install不Failed**（0.13.9 起）：本地嵌入引擎（transformers.js + onnxruntime 原生件，解包约 300MB）yes optionalDependencies——network停摆时Install会Skipped它并照常completed，插件的Memorywritten to、keywordRetrieval与Panel**All可用**；只yessemanticRetrieval暂缺，Panel「System · Runtime」会DisplayModel列表为empty。network稳scheduledUpdate/重装插件即可补上，存量Memory跑一writes `pnpm backfill` 补算向量。
- 升级后OpenPanel「System · Runtime」即可核对插件/Panel versionyesno一致（mixed会挂重启横幅）。

## 为什么yes它

Memory插件不Less，但More数要your先准备一整套Base设施。这里yes三条主流路线与本插件的对照（对照对象为各Projects公On README 与 npm 依赖声明）：

| | **dsh-engram** | 服务端型（Postgres/pgvector/Milvus/Python 服务） | Document型（纯 Markdown Memory） |
|---|---|---|---|
| Install成本 | 一条 npm 命令，**装完即用** | Docker + DataStore + 向量Store + 迁移 | 一条 npm 命令 |
| 外部依赖 | **none**：嵌入Model离线Reasoning，FailedAutomaticdemote纯keyword并显式标Memory | 必装，通常还要 LLM API Key | none |
| agent Tools面 | **23窄ParametersTools**，覆盖Save/Retrieval/事实链/Timeline/审计/Export/蒸馏/Beliefs/review全生命Wk期 | 常见 1–3（只给Retrieval） | 常见 0–1 |
| 证据门 | **有**：`engram_assess` 由代码强制「声称充足 + 本批writes有效证据 + nextStrategy=answer」三者齐备才判充足 | 一般none | 一般none |
| 审计 | SourceSession/轮writes/seq 三级溯源 + 取代链 + Conflicts边 + op_log 全量流水 | 通常只有Daily志 | none |
| review | **SM-2 SpacerDuplicate**：只给坐标与门牌线索，不给Body，先回忆后揭示 | none | none |
| 注入防护 | protocolTags防提示注入 + 入Store脱敏 + Recall占位防自强化回声 | none | none |
| 可携带 | Markdown / JSON Export + Obsidian/Logseq 友好镜像 | 看实现 | 本来就yesDocument |
| Language | zh / en / ru | 看实现 | 一般单语 |

一句话：**我们把「Memory」做成一个装完就能用的本地Store，而不yes一个需要your运维的服务。**

## Memory怎么流动

```
  SessionDaily志 --+
            +--> 摄取 / Save --> written to四态: accept · merge · defer · drop
  agent Calls+
                                            |
                                            SQLite 双Store: user.db（跟 experts走）+ project-<hash>.db（跟Repository走）
                                            |
      +----------------------+----------------------+----------------------+
      v                          v                        v
      FTS5 keyword道              本地向量道 (RRF)         Off系边一跳扩展
      +----------------------+----------------------+----------------------+
                                            v
                                            engram_search --> 命中行带 id= / ref= 与批writes id
                                            v
                                            engram_assess（证据门）: 不足以回答就ContinueRetrieval
                                            v
                                            每轮注入Image <-- Beliefs · 事实链 · Entities <-- 蒸馏 · 巩固 · 衰减 · SM-2
```

## 界面速览

**Today**——Memory Palace的Dashboards：速览 KPI、6 months活动热力、Tour proposal、Room directory。

<p align="center"><img src="docs/screenshots/today.png" width="820" alt="Today视图：速览 KPI、活动热力图、Room directory"></p>

**满宽Refurb suggestions**——每条建议完整展示决策理由与Memory原文，就地阅读、就地Run（demote / 蒸馏merge），不再挤在小卡里靠弹窗补全。

<p align="center"><img src="docs/screenshots/refurb-fullwidth.png" width="820" alt="Refurb suggestions：满宽铺On，理由与原文完整展示，就地Run"></p>

**陈展**——全量memory base：Room / Status / content过滤，行内Placard展On与取代链edit。

<p align="center"><img src="docs/screenshots/library.png" width="820" alt="陈展视图：过滤、Sort与行内Details"></p>


## 宫殿结构：目录即Room，路径即路线

DeepSeek Harness 跨Session长期Memory插件 —— 把Memory Palace的**信息架构**（而不yes它的神经科学隐喻）真正落进 agent：**Location当索引**（每条Memory钉In “Room#桩位」坐标上）、**Pin路线定顺序**（巡游路线只增不改）、**骨架长期复用**（同主题永远同房同序）、**标Memory独一none二**（门牌纪律：唯一 · 差异化 · 带Date）。配套SpacerDuplicate的Retrieval练习（只给线索、不给Body）与知识飞轮（摄取 → 强化 → 蒸馏 → 衰减）。

Memory Palace真正管用的yes它的**信息架构**，不yes那套生物学机制——前者机器完全能用，后者 AI 既没有也不需要。拆On看只有四件事：

```
主厅 · 常驻核心Memory   Less而稳，每writes都在场            每轮注入的Image（条数 + token Budget双限）
  │
走廊 · 路由索引       先决定进哪个Room，别一上来全StoreRetrieval   Room directory + engram_search room=
  │
  ├─ Fact Hall   fact        用户说过的事实
  ├─ Preference Pavilion   preference  偏好与口味
  ├─ Decision Chamber   decision    决策与约定
  ├─ Episode Gallery   episode     经历与Timeline
  └─ Skill Workshop   skill       方法与技法                 每房容量 9，满员On「房名-2」
  │
门牌 · Placard纪律       唯一 · 差异化 · 带Date         written to即评分，低分进Refurb suggestions
```

| 宫殿原则 | 在插件里yes什么 | 代码 |
|---|---|---|
| Location当索引 | written to即slot assigned `Room#桩位`；Room容量 9（7±2），满员room opened，桩位只增不回收 | [src/palace/slots.ts](src/palace/slots.ts) |
| Pin路线定顺序 | `tour_routes` 只增不改；`engram_tour mode=fixed` 按桩位顺序走全宫 | [src/store/sqlite.ts](src/store/sqlite.ts) |
| 骨架长期复用 | 同主题永远落在同一Room同一序号，Recall靠顺序提取而非重新Retrieval | [src/palace/slots.ts](src/palace/slots.ts) |
| 标Memory独一none二 | 门牌 0-1 评分：全Store唯一 +0.4 / Date锚点 +0.3 / 同房前 6 字不Duplicate +0.3 | [src/imagery/score.ts](src/imagery/score.ts) |
| review纪律 | SM-2 SpacerDuplicate；Retrieval练习只给坐标与门牌、**不给Body** | [src/review/sm2.ts](src/review/sm2.ts) |

感官与情绪维度（气味、温度、情绪权重）刻意不计分：那yes给 experts脑先天限制打的补丁，AI 既没有也不需要。

## 特性

- **跨SessionMemory**：SessionOn始注入用户Image摘要（条数 + token Budget双重上限，可配；`engram_profile_edit` 维护的 curated Image优先于派生事实），Agent 天然"Memory得"youryes谁、在做什么；ToolsRetrieval跨SessionRecall历史事实。
- **双层分Store**：`user.db` GlobalShared；`project-<hash>.db` 按 git origin 标识隔离（none git 时回退 cwd 全量哈希，旧命名StoreAutomatic迁移）——个 experts偏好跟 experts走，Projects约定跟Repository走。**Project palace随Workspace切换**：管理Panel「Projects」scope 默认Follow GUI 当前选中的Workspace（Header DisplayWorkspaceTitle与路径），也可在下拉里Pin到某个Workspace；`engram_save/search` 等Tools的 project 读写同样按当前Session cwd 归属，与Panel同一口径。
- **混合Retrieval**：FTS5（unicode61 + Chinese 2-gram 预切词）与本地向量（默认 `Xenova/paraphrase-multilingual-MiniLM-L12-v2`，384 维，q8；Model与池化可配，见下）RRF 融合 + Off系边一跳扩展 + 新鲜度/命中writes数乘性Sort boost；嵌入Model离线运行，downloadsFailedAutomaticdemote纯keyword并显式标Memory。
- **Memory Palace信息架构**（v0.7.2+）：四原则All落进核心路径，而非展示层皮肤——**Location当索引**（written to按 kind 分房并钉「Room#桩位」坐标，Room容量 9，满员room opened，桩位只增不回收）；**Pin路线定顺序**（`tour_routes` append-only，`engram_tour mode=fixed` 按桩位顺序走全宫）；**骨架长期复用**（同一 topic 永远落在同一Room同一序号，顺序提取而非重新Retrieval）；**标Memory独一none二**（门牌规则 0-1 评分：全Store唯一 +0.4 / 带Date锚点 +0.3 / 同房前 6 字不Duplicate +0.3，低分进Refurb suggestions；Store内 active MemoryLess于 8 条时不扫描，避免小Store噪声）。
- **走廊路由Retrieval**：Image里附Room directory，`engram_search` 支持 `room` Parameters——先决定进哪个Room，再在房内Retrieval，而非一上来全Store RRF。Retrieval命中 top5 附同RoomAdjacent桩位 id 作为编码特异性线索。
- **Retrieval练习闭环**（SpacerDuplicate）：`engram_review_queue` 只给宫殿坐标与门牌线索、**不给Body**，迫使Model先主动回忆；`engram_review` 揭示核对，`engram_report grade`（0-5）自评推进 SM-2 调度（1 → 6 → round(prev × ease) 天，FailedReset，ease 下限 1.3）。进入review调度的条目**不再参与Automatic衰减**——命运由回忆结果决定。SessionOn始注入会提示Today待回忆条数。
- **历史SessionBackfill**（v0.7.3+）：把 dsh 已持久化的历史Session逐轮提炼进宫殿——默认按**每个Session自己的 cwd** 写进对应ProjectsStore（不串Store），同样逐条判作用域（跨Projects通用的个 experts偏好落私 expertsStore），复用实时摄取的节流/脱敏/防回声，靠 (Session, 轮writes) 幂等键支持中断续跑（键Pin在私 expertsStore，与实时路径共用）；Backfill条目不进Todayreview队列（避免一writes性Backfill淹没「Today待回忆」）。**Import规则由your选**（Time window / 单Session轮数 / 总轮数上限 / 辅助Model / yesno含子代理·种子·none cwd Session），先估算（零成本、不调 LLM）再Run；Settings page有独立的**「Backfill」tab**，可看Progress（含**Skipped分布**）与Pause续做。辅助Calls默认**复用your当前在用的Model**（历史Daily志里Memory的yes当年的 provider/model，在当前环境可能已不可用），也可在Panel「辅助Model」下拉里从宿主已注册的 provider/model 中直接指定。
- **知识飞轮**：摄取/Save → contradiction candidates（written to时高相似近邻建 `contradicts` 边并报告，Model/用户Jev）→ 命中强化（confidence +0.05）→ 蒸馏（同主题簇merge为高层规律、supersedes 取代链、置信度Inherit）→ 衰减（低重要性且长期未访问Archived，可Restore）。
- **AutomaticBeliefs巩固**（v0.7.14+，默认Close，yml `reflect: suggest|auto`）：Session结束后把「尚未成为任何Beliefs证据」的原始Memory交辅助 LLM 巩固为一句话**Beliefs（observation）**——与蒸馏的Off键区别yes原始MemoryAll保留，Beliefsyes**叠加的抽象层**：每条Beliefs带证据 id 列表与证据数（proof count），同主题经向量近邻（余弦 ≥0.9）Automatic归并、**持续细化同一条**而非反复Added。新证据与既有Beliefs近邻（≥0.86）时Beliefs先转 `stale`，下writes巩固把原始证据一并喂给Model复核，结果四选一——`form` Start、`refine` 细化、`still` 维持、`refute` no定（保留证据链供审计）。`auto` 档Session结束后台巩固（5 秒timeout、异常不影响Conversation）；`suggest` 档只登Memory待巩固任务，由 `engram_reflect` Tools一键Run（任何档位都可Manual调）。active Beliefs按证据数取前 3 条随Image包注入Session，stale/refuted 不注入。schema 升级到 v12（observations 表，增量迁移、存量Data零搬运）。
- **Jev System一Jev**（v0.7.12+，默认Close，yml `jev` 子配置）：DEFER band的三路判定（merge / accept / defer）与Conflicts边Confirm可交外部 Jev Model判决；Jev 不可用（networkFailed / timeout / 未解析出Provider或Model）时静默fallback纯规则四态，**不阻断written to**。每writesJev旁路留观测（判定结果、Jev yesno参与、耗时、fallback原因，进程内最More 50 条、重启Clear），Panel「Jev」视图一眼排查「为什么这条被defer」。yml 的OnOff / Provider / Model / 端点 / timeout五项可在Panel覆盖（存 `<dbDir>/jev-config.json`，0600 Document + 0700 目录），Save即时activenone需重启；端点留empty时按Provider推导为 `{baseURL}/systemone`；「Test connection」按钮用当前Input即时验证（未Save也按当前值测、不落覆盖Document）；令牌不yes插件设置，Calls时从所选Provider的密钥Store解析，因此插件手里没有第二份可掩码、可同步、可轮换的副本。三阈值保持 yml 高级配置，PanelRead-only展示。
- **EntitiesDictionary**（v0.7.10+）：Automatic摄取与 `engram_save` 的辅助 LLM Output同时抽取Entities提及（ experts物/Projects/Tools/Concepts，可带alias），按归一化名与既有Entities的 name/aliases 精确匹配消解——命中复用并Refresh「最近提及」，未命中Start；Memory与EntitiesMore对Morelinks，随SourceMemory所In scope 分Store。`engram_search` 命中行附linksEntitiesTags；Entities消解故障只静默Skippedlinks，不影响Memory落Store。Panel「Entities」视图按类别筛选、按Name/aliasSearch，点On单Entities看它牵出的所有Memory。
- **Automatic摄取**（`ingest` 配置Turn on时）：新一轮第一步从SessionDaily志提取上一轮的候选事实，Session结束时补摄取最后一轮（Failed留 pending 键，下writesSessionAutomatic补做，幂等不Duplicate），低 confidence written to，嵌入可用时按四态处置近邻（复述merge强化 / 疑似Conflicts建边待Jev / 全新written to）——不说"Memory住"也能攒Memory。**逐条判宫殿**：提炼时同步判定作用域，只跟当前Projects/Repository有Off的（技术选型、Projects约定、架构决策）进当前Session cwd 对应的ProjectsStore，跨Projects通用的（个 experts偏好、习惯、本 experts经历）进私 expertsStore——偏好跟 experts走、约定跟Repository走。
- **Source审计**：每条MemoryRecordsSourceSession、轮writes与事件 seq，`engram_review` 完整回查Source链、取代链、Conflicts与操作Daily志；Allwritten to/Edited/遗忘/蒸馏/衰减入操作Daily志表。
- **Web 管理Panel**（v0.7.0+）：v0.8.0 起从「设置 → memory base tab」升级为**顶层侧栏入口**（`sidebar.panellist` + `main` 双槽位，与「Hyreal FDE 工作台」平级），主列满宽渲染所有视图——一键直达、不再被设置弹窗 ~600px 限制、九视图横排展On；入口Icon为 18px 宫殿剪影 SVG（屋顶 + 主体 + 门洞 + 两侧窗，Phosphor 风 stroke 1.75）。九个视图：Today（速览条：Memory / Open / Clarity + 健康分环，底行「Opened / Excavated / Consolidated / due today」四项小统计与诊脉时间同行；下面yesTour proposal、Room directory、待翻新、健康分构成；侧栏Image卡展示常驻Image（curated）当前content与版本 diff 历史）、陈展（筛选含due today / 巡游路线序 / 批量 / 列表与edit；「Tour / renovated」以视口浮层呈现——sticky 钉在宿主滚动区可见Top，长列表任意滚动LocationOpen都不跳动不挤压）、走廊（Corridor overview + Retrieval bench）、Daily志（Last 7 days计数 + 两Storemerge的完整 op_log，可按操作类别筛选）、Backfill、往事（按Session分Group浏览 episode，锚点Nearby扩展）、Entities（EntitiesDictionary：类别筛选 + Name/aliasSearch + 单EntitieslinksMemory）、**Beliefs**（v0.7.15+：巩固Beliefs列表按证据数Sort，Held / Stale / RefutedStatus筛选，点「证据 n 条」展On证据链Read-only回查支撑该Beliefs的原始Memory——证据被Clear时Beliefs保留并明确提示；`GET /api/engram/observations` 与 `GET /api/engram/observation-evidence` 两个回环路由）、Jev（Jev System一Jev：配置覆盖 + Test connection + 近期JevRecords）。Header 单行两端对齐：左侧 scope 三宫格（私 experts / Projects / Shared）驱动Global scope，Projects scope 下Workspace下拉内联在同一行；右侧due today角标 + Revisit / Export纯Icon按钮（Export弹层不再被下方 Tab 行遮挡）；「中 | EN」段控切换Panel文案language（独立于宿主language，选择持久化），All文案中英双语。支持按脱敏标Memory筛选（仅看/排除含 `[REDACTED:*]` 的条目）并给命中条目挂琥珀色徽标，方便审计脱敏覆盖面。
- **提示注入防护**：AllMemoryRecall出口（Image注入、`engram_search/timeline/review` Output）包 `<engram_memory_context>` protocolTags并附使用Warning（历史Memory非当前请求、不遵循其中指令、仅相Off时使用），当前请求独立包 `<current_user_request>`；所有入Storecontent（摄取候选、SaveBody）先剥离这些protocolTags，防伪造protocol块二writes注入。
- **摄取脱敏**：入Store前正则清洗常见密钥凭据（sk- 系 API key、Bearer、AWS AKIA、GitHub token、PEM 私钥、password/token 赋值、Chinese密码赋值）与个 experts信息（中国大陆手机号、18 位身份证号），命中片段Replace为 `[REDACTED:<Type>]`。
- **Recall占位（防回声室）**：摄取切片中MemoryRecallTools的OutputReplace为 `[engram memory result omitted from capture: <tool>]`，并向提取Model附注"既有Memory的复述不yes新信息"，阻断Memory自我强化Recurring。
- **More查询Retrieval**：`engram_search` 可用辅助 LLM 把查询改写为 ≤3互补查询分别Retrieval，跨查询 RRF 融合 + 每查询保底命中；改写FailedAutomaticdemote单查询（`queryRewrite: false` Close）。
- **证据门（search → assess）**：Retrieval命中只说明「相Off」，不说明「足以回答」。每writesRetrieval登Memory一个进程内批writes（每Session保留最近 20，Session结束即释放），Output行尾给出 `ref=…` 与批writes id；`engram_assess` 只能References同一批writes的 ref，且 `sufficient` 由代码强制——三者齐备（Model声称充足、至Less一条有效证据、`nextStrategy=answer`）才算充足，no则判为不足并把策略改回ContinueRetrieval。判定与拒绝明细written to审计Daily志，Panel「Daily志」的「Retrieval」类别可见。NextOn始前若仍有未判定批writes，注入收尾提醒引导补判或说明不判（连续不足 ≥2 calls时建议换Retrieval方式或询问用户；`assessReminder: false` Close）。
- **Data可携带**：`engram_export` 一键Export Markdown / JSON Document，支持Redacted view（content二writes清洗 + Preview截断，Share安全）。`engram_mirror` Export可漫游的Mirror tree（Obsidian / Logseq 友好：每条Memory一个 Markdown，Body + YAML frontmatter + 双向链接 `[[id]]`），让「宫殿」也成为可 experts读的私 experts知识Store。
- **认知架构探索（dsh-market · AGI 架构探索）**：本Repositoryyes dsh-market「AGI 架构探索」类目下，对 agent 长期Memory的认知科学方法论重构——Memory Palace（意象Tags + RoomPlacard）、走廊拓扑（力导向图）、闭环提问（摄入时让Model主动追问用户细节）、巩固merge（启发式去重 + 余弦相似度），与 MemGPT/Letta 同层「agent Memory架构」叙事。

## Tools（23，窄Parameters）

| Tools | 作用 |
|---|---|
| `engram_save` | Save（**written to四态回报**：复述merge强化既有条目（merge，不Start）、疑似Conflicts落Store建边待Jev（defer）、全新written to（accept），低熵content丢弃（drop）；嵌入不可用时All accept）；支持 `items` 数Grouponce批量Save ≤10 条，统一清洗/批量内去重，逐条回报处置（`disposition`/`mergedInto`），单条Failed不影响其余（`count`/`items`/`failed` 汇总Back）；`placard` 挂门牌（按唯一·差异化·带Date评分，低分附改写建议）；`entities` 附Entities提及（与Memory消解links进EntitiesDictionary）。`scope=project` 落当前Session cwd 对应的Project palace |
| `engram_search` | semantic + keyword混合Retrieval（命中强化置信度）；`room` Parameters做走廊路由——只在指定Room内Retrieval；命中 top5 附同房Adjacent桩位线索，命中行附linksEntitiesTags；`asOf` 做时点回看——只认该时刻有效的Entities事实，命中行尾附linksEntities的时点事实。Output行尾给 `id=` 与 `ref=`，end给批writes id。`scope=project` 查当前Session cwd 对应的Project palace |
| `engram_facts` | Entities事实链读写。written to模式传 `facts` 数Group（≤50 条，每条 `entity`/`entityId` + `content`），`replaces` 声明取代旧事实（软失效，历史链保留），坏条目进 `failed` 不中断其余；查询模式传 `entity`（按名查询，未命中不Start、歧义列候选、近邻给建议）或 `entityId`（精确），`includeInvalid` 看全链含superseded，`asOf` 只看该时点有效的事实 |
| `engram_assess` | 证据门：作答前判定「Retrieval到的contentyesno足以回答」。提交 `batchId` + ≤8 条 `evidenceRefs`（只能取该批writesOutput里的 `ref=`）+ `missing` + `nextStrategy`；代码强制 `sufficient` 需同时满足「声称充足」「至Less一条属于本批writes的有效证据」「nextStrategy=answer」，no则判为不足并把策略改回ContinueRetrieval；非本批writes的 ref 会被拒绝并列出，判定written to审计Daily志 |
| `engram_timeline` | Timeline浏览：默认按Create时间倒序；`order: 'tour'` 改按Pin巡游路线桩位顺序（Output附宫殿坐标，未上路线者排end），让 agent 也能沿Pin路线复述 |
| `engram_episode_timeline` | episode 情景独立Timeline：按DateScope与SourceSession浏览经历，Output按Session分Group（Group间新→旧、Group内时间升序，`sessionId` 可精确回查单Session，Group头附摄取期或Session结束时生成的一句话Session摘要）；`around` 传锚点Memory id 时切换为Time proximity expansion——列出锚点时刻 ± Window（`windowMinutes`，默认 60）内的情景，回答「当时前后还发生了什么」 |
| `engram_update` | 修正（supersedes 取代链）；可同时改挂 `placard` 门牌 |
| `engram_forget` | 遗忘（软删可Restore） |
| `engram_report` | 回报使用效果（skill 类首选）：success 提权 +0.05 / failure 降权 -0.1，持续none效的Memory被衰减自然淘汰。传 `grade`（0-5）则按 SM-2 推进review调度，作为Retrieval练习的自评入口 |
| `engram_review_queue` | Today待回忆队列：只给宫殿坐标（Room#桩位）、门牌与逾期天数，**不给Body**——先回忆、再揭示、后自评 |
| `engram_review` | 审计单条：Source链、取代链、Conflicts、操作Daily志 |
| `engram_stats` | 全Store统计与信噪比；附Room directory（各房占用桩位与最新门牌） |
| `engram_examine` | 渐进式披露：按 id 批量拉完整Placard（建议 ≤16，先Retrieval拿 id 再取全文） |
| `engram_neighbors` | 走廊漫步：从一间Walk走 1-3 跳Off系边，Back邻居简Table |
| `engram_tour` | 巡游路由：`mode=fixed` 按Pin桩位路线走全宫（路线恒定，顺序提取）；`mode=thematic` 按主题动态规划 3-7 站 |
| `engram_audit_forgotten` | 闭馆考古：列最近closed条目与墓志铭，复核过去的遗忘yesno得当 |
| `engram_ingest_history` | 历史SessionBackfill：把 dsh 历史Session逐轮提炼进宫殿（按Session cwd 分Store、already captured轮writesAutomaticSkipped）。每Session轮writes摄取completed后用辅助 LLM 生成一句话Session摘要落Store（Failed静默不阻断），实时Session结束时同样收尾生成，供TimelineGroup头回看。`dryRun` 缺省 true 只估算；`dryRun=false` 才Run。大批量建议用Settings page「Backfill」tab |
| `engram_export` | Export Markdown / JSON Document（Data可携带）；`redactedView: true` OutputRedacted view（二writes清洗 + 40 字Preview截断，可安全Share） |
| `engram_distill` | 蒸馏：同主题簇merge为高层规律（LLM） |
| `engram_reflect` | Beliefs巩固：把尚未巩固的原始Memory经辅助 LLM 抽象为带证据数的高层Beliefs（form/refine/still/refute 四动作，向量近邻Automatic归并，原始Memory保留不Archived）；同时yes reflect=suggest 待巩固登Memory的Run出口 |
| `engram_profile_edit` | Image curated block edit：`view` 看当前content与Version history；`edit` written to新版本（乐观锁 `expectedVersion`，Conflict loud Failed）；`rollback` rollback到历史版本（作为新版本追加，历史永不改写）。curated Image优先于派生Image注入；editRecords入操作Daily志，PanelToday页Image卡可查版本 diff |
| `engram_language_guide` | OutputPlugin language指南（`<包根>/docs/agent-language-guide.ru.md` 的Body）。首启时对未Built-inlanguage的 experts承诺「agent 会Output一份说明」，这份承诺由本Tools兑现；Body以俄文写成草稿，由 agent 按提问者的language自行Translate |

## 配置

可选配置（cordis.yml）：

> **Data目录跟Harness home走**：不写 `dbDir` 时，插件用宿主的Data根 `$DSH_HOME`（缺省 `~/.dsh`）下的 `engram/`——和Session、配置、Other插件的Data放在同一个根里。把 `DSH_HOME` 搬到别的盘，memory base也跟着搬。若旧目录 `~/.dsh/engram` 里还留着Store，插件启动时先把它收编过来（同盘直接改名、跨盘复制后校验再删旧），并把结果写进启动Daily志；显式写了 `dbDir` 的 experts不受影响，那一行说了算。Panel「System · Storage」会同时报出Data目录与Harness home，Store不In home 之下时会明确提示。

```yaml
- id: dsh-engram
  name: '@kenz1117/dsh-engram'
  config:
    dbDir: '~/.dsh/engram'          # 分Store与ModelCache根目录；不写则默认 $DSH_HOME/engram
    injectProfile: true             # SessionOn始注入用户Image摘要
    profileTopN: 8                  # 注入条数上限（1-64）
    injectTokenBudget: 1024         # 注入 token Budget（128-8192，Chinese按 1.5 token/字、其余按 4 字符/token 估算，装不下的条目demote为索引行）
    injectItemBudgetStart: 160      # 分级递减Budget：首条Body字符数（40-2000，逐条按 decay 递减、装不下先截断）
    injectItemBudgetDecay: 0.9      # 分级递减Budget：逐条递减系数（0.5-1）
    injectItemBudgetFloor: 24       # 分级递减Budget：单条Body字符下限（8-200，不得超过 start）
    assessReminder: true            # 证据门收尾提醒：存在未判定Retrieval批writes时在NextOn始前注入 engram_assess 提醒
    modelCacheDir: '~/.dsh/engram/models'  # 嵌入ModelCache目录
    hfEndpoint: 'https://huggingface.co'   # Modeldownloads端点，network受限可配镜像
    # 嵌入器：整段不写就yes local + 默认Model；kind 决定用本地Model还yes远程端点
    embedder:
      kind: 'local'                   # local（transformers.js 离线Reasoning）| remote（OpenAI 兼容 /embeddings）
      localModel: 'Xenova/paraphrase-multilingual-MiniLM-L12-v2'  # 384 维；旧默认 Xenova/bge-small-zh-v1.5 为 512 维
      localPooling: 'mean'            # mean（More数句向量Model）| cls（bge-m3 and XLM-R 同族）
      # kind: 'remote'
      # model: 'text-embedding-3-small'      # remote：Model名，原样传给服务端
      # baseUrl: 'https://api.example.com/v1' # remote：含版本号的根地址
      # apiKeyEnv: 'EMBEDDING_API_KEY'        # remote：密钥从这个环境变量读（优先于写死在配置里的 apiKey）
      # batchSize: 32                         # remote：每批条数
      # timeoutMs: 30000                      # remote：once请求timeout（毫秒）
    # 换Model要重建向量：维度与Store中不同时written to和比较都直接拒绝（EMBEDDER_DIMENSION_MISMATCH），不截断后假装相似
    ingest: 'off'                   # Automatic摄取：off | light（仅用户Messages，每轮≤2条）| eager（含助手Messages，每轮≤5条）
    reflect: 'off'                  # AutomaticBeliefs巩固：off | suggest（Session结束只登Memory待巩固，engram_reflect Run）| auto（Session结束后台巩固）；任何档位均可Manual调 engram_reflect
    # provider 与 model 必须成对提供：摄取/蒸馏的辅助 LLM 路由覆盖（缺省从SessionDaily志解析）
    # provider: 'deepseek'
    # model: 'deepseek-v4-flash'
    decayAfterDays: 30              # 衰减：最近访问超过该天数（同时yesRetrieval recency boost 的衰减Window）
    decayImportanceBelow: 0.3       # 衰减：且 importance 低于该值 → Archived（可Restore）
    rankRecencyWeight: 0.2          # RetrievalSort新鲜度因子权重（0-2，0 Close）
    rankProofWeight: 0.1            # RetrievalSort命中writes数因子权重（0-2，0 Close）
    queryRewrite: true              # engram_search 用辅助 LLM 改写 ≤3查询做 RRF 融合（FailedAutomaticdemote单查询）
    autoSlot: true                  # written to期Automaticslot assigned（按 kind 分房、钉「Room#桩位」坐标、登Memory巡游路线）
    reviewScheduling: true          # written to期Automatic排入review调度（1 天后首writes到期；Off则新条目不进 SM-2 队列）
    panelAccess: 'loopback'         # Panel API 访问面：loopback 仅Local回环 (default)| lan accept私网（见下方说明）
    # 历史Backfill默认规则（Panel/Tools的初始值；总轮数上限yes硬顶，按writes只能调低）
    historyBackfillDays: 7                  # 默认Time window天数，0 = no limit
    historyBackfillMaxTurnsPerSession: 20   # 单个Session默认最More摄取轮数
    historyBackfillMaxTotalTurns: 200       # once运行的总轮数硬上限（1-5000）
    historyBackfillIncludeSubagents: false  # 默认排除子代理Session
    historyBackfillIncludeSeeded: false     # 默认排除种子Session
    historyBackfillIncludeNoCwd: false      # 默认排除none cwd Session（这类只能进 user Store）
    # Jev System一Jev（默认Close；Provider与Model取自 DSH，插件不Save密钥；Jev 不可用时静默demote回纯规则四态）
    # DEFER band三路Jev：概率≥deferMergeAbove 判同一条（merge），概率≤deferAcceptBelow 判不同条（accept），中间保持 defer；
    # defer 落Store前Conflicts边先经 Jev Confirm（概率≥contradictMinProbability 才建 contradicts 边）。
    jev:
      enabled: false                        # OnOff
      # provider: ''                        # Jev 取自哪个 DSH Provider（用它的 baseURL 与密钥Store）；留empty = 本Session正在用的那个
      # model: 'jev-latest'                 # 判决Model名；留empty = 该Provider的第一个 Jev Model
      # endpoint: ''                        # System One 完整端点（POST /v1/systemone）；留empty = 按Provider推导 {baseURL}/systemone
      # timeoutMs: 3000                     # once请求Timeout (ms, 1000-60000)
      # deferMergeAbove: 0.85               # band概率≥此值判同一条（merge）
      # deferAcceptBelow: 0.15              # band概率≤此值判不同条（accept）
      # contradictMinProbability: 0.8       # ConflictsConfirm概率≥此值才建 contradicts 边
    # 以上 enabled/provider/model/endpoint/timeoutMs 五项可在管理Panel「Jev」tab 覆盖（存 <dbDir>/jev-config.json，字段级覆盖，Save即时active）；三阈值仅此处可配。令牌不yes插件设置：Calls时从所选Provider的密钥Store解析，插件手里没有可掩码、可同步、可轮换的副本。此处或覆盖Document中遗留的 apiKey/baseUrl 会被忽略，并给出一writes性告警，指明字段与密钥的去处
```

**Panel访问面（`panelAccess`，#49）**：Panel API 默认只接受Local回环（peer socket + Host 头双校验，写操作再校验 Origin 与 Content-Type）。手机或局域网Other设备要看Memory Palace时，把 `panelAccess: 'lan'` 与宿主 webServer 的 `host: 0.0.0.0` 一起Open：私网 peer（RFC1918 / IPv6 ULA / 链路本地）即可访问，Host/Origin 接受字面 IP、localhost、单Tag name与 `*.local`（MoreTags域名仍拒，防 DNS rebinding）。公网 IP 任何档位都拒绝——公网暴露请走反向代理到回环上游（此时none需改任何配置）。API 没有鉴权，`lan` 档等于把memory base的读写向整个局域网公On，只应在信任的家庭/办公网Turn on。

### 推荐Group合（按使用方式抄一行）

Memory的「成条」程度由摄取与巩固两段共同决定：**摄取**抓Asset，**巩固**把Asset蒸馏成规律。只On摄取不On巩固，一Wk后的Memory就yes散的——这yes最常见的失望Source。

| 使用方式 | 建议配置 | 说明 |
|---|---|---|
| **编程为主**（FastAPI/Vue 这类ProjectsOn发） | `ingest: 'eager'` + `reflect: 'auto'` | 每轮抓用户+助手Messages（≤5 条），Session结束Automatic蒸馏成Projects规律/决策；ProjectsStore按Repository隔离，跨Session续接 |
| **写作 / 文书Consolidated** | `ingest: 'light'` + `reflect: 'auto'` | 只抓用户Messages（≤2 条/轮），减Less噪声；巩固把偏好与约定蒸馏成Image与Beliefs |
| **轻量试用** | `ingest: 'light'` + `reflect: 'suggest'` | suggest 在Session结束时登Memory待巩固并在下writesSessionOn始提醒 agent Calls `engram_reflect`——不想让后台Automatic跑 LLM 时用这个 |
| **只想Manual管理** | `ingest: 'off'` + Tool calls | 由 agent 用 `engram_save` / `engram_update` 显式written to；其余全Off |

巩固与蒸馏走辅助 LLM 路由（缺省从SessionDaily志解析）：**轻量档Model（如 flash 类）的蒸馏质量有限**，Memory散时先换强一点的Model跑几天 `reflect`，再评价插件。

## 工作原理

插件由宿主半（Node）与浏览器半（React）Group成：

```
Session Agent                                 宿主半（Node）
  │                                          │
  ├─ 每轮第一步 ◀─────────────────────────── ├─ 用户Image快照注入（plugin Source user 快照）
  ├─ engram_save / search / review … ──────▶ ├─ SQLite 双Store（user.db / project-<origin hash>.db）
  │                                          ├─ FTS5 keyword道 + 本地向量道 RRF 融合 + Sort boost
  ├─ engram_distill ───────────────────────▶ ├─ 辅助 LLM 蒸馏（簇merge → supersedes 链）
  ├─ Session结束（reflect Turn on）──────────────▶ ├─ Beliefs巩固（未巩固证据 → observations：近邻归并 / stale 复核）
  │                                          └─ Automatic摄取：SessionDaily志 → 候选事实（含Session结束的末轮，ingest Turn on时）
  └─ Settings page「memory base」tab ◀────────────────── ─── 回环 API /api/engram/*（写操作校验回环 Origin）
```

- **双层分Store**：`user.db` GlobalShared；`project-<hash>.db` 按 git origin URL 归一化哈希命名（`git@github.com:a/b.git` 与 `https://github.com/a/b` 同Store；worktree 沿指针解析到主Repository origin）；none git 或none origin 时按 **cwd 全量 sha256** 命名（v0.7.6 起；旧的「cwd 前 12 字符」截断命名会同前缀撞Store，启动时Automatic rename 迁移，新旧并存则不动并告警）。
- **Project palace路由**：`GET /api/engram/workspaces` 给出可选Project palace清单（Source = 宿主 `workspaceRegistry` Workspace → Session header 里出现过的 cwd → 插件进程目录兜底，按分Store名去重；同Repository的More个 worktree 共用一个宫殿）；所有Projects scope 的接口与Tools读写接受选择器（HTTP `?project=<dbName>` / POST body `project`，Tools用当前Session `session.header.cwd`），Unknown选择器 HTTP 回 404、Tools回退进程目录。Panel「Projects」scope 默认Follow GUI 当前Workspace并可Pin到某个Workspace。
- **宫殿结构（目录即Room，路径即路线）**：**主厅** = 每轮注入的常驻核心Image（Less而稳，每writes都在场）；**走廊** = Image里附的Room directory + `engram_search room` Parameters（先定Room，再Retrieval）；**Room** = 按 kind 分房（Fact Hall / Preference Pavilion / Decision Chamber / Episode Gallery / Skill Workshop），容量 9，满员On「房名-2」；**门牌** = 每条Memory的 `placard` Placard，受唯一·差异化·带Date纪律评分。存量Store首writesOpen时Automatic补slot assigned（幂等，room opened会告警提醒 experts工命名）。
- **Automatic摄取**（`ingest` Turn on时）：新一轮第一步从SessionDaily志提取上一轮的候选事实；Session结束（session/disposed）补摄取最后一轮，5 秒timeout，Failed/timeout把 pending 键written to操作Daily志，下writesSession首步Automatic重放补做；already captured的 (Session, 轮writes) 幂等去重（键Pin在私 expertsStore，实时与历史Backfill共用一份，跨路径不Duplicate）。读取源yesSessionDaily志；辅助Calls的请求审计走插件自身操作Daily志，不向SessionDaily志 append Unknown事件。候选以低 confidence written to并按**Goal分Store内**的嵌入近邻去重。**作用域逐条判**：提炼Output带 `scope`，`project` 落当前Session cwd 的ProjectsStore、`user` 落私 expertsStore；none cwd 的Session只能进私 expertsStore（此类Session不做逐条判定，避免标Memory与实际分Store不符）。历史Backfill沿用同一判定，默认按各Session自己的 cwd 落ProjectsStore。
- **Source链**：每条MemoryRecordsSourceSession、轮writes与事件 seq，`engram_review` 可完整回查；操作Daily志表RecordsAllwritten to/Edited/遗忘/蒸馏/衰减。
- **嵌入离线**：Model首writes使用需联网downloads（q8 约 50MB，端点可配镜像），此后完全离线；Failed时插件照常工作，Retrievaldemote纯keyword并显式标Memory。**维度yes硬约束**：Store中向量与当前Model的维度mixed时，written to与相似度比较都直接拒绝并报 `EMBEDDER_DIMENSION_MISMATCH`（说清两边各yesMoreLess维），而不yes逐维截断后给出一个像样的相似度。
- **界面本地化**：client 半经宿主 locale 服务注册 zh/en Dictionary，Follow宿主language设置实时切换；Status/种类等Data枚举仅在Display层映射，Storage值保持英文。

## Web 管理Panel

Install后在宿主**侧栏**出现「Memory Palace」入口（v0.8 起为独立PanelApply，不再寄居Settings page），v0.13 起Panelyes**六Workfaces控制台**：左侧竖排导航切Workfaces，面内再切二级视图。

| Workfaces | 二级视图 | content |
|---|---|---|
| **Today** | — | 真实 KPI（Memory / Open / Clarity / due today）+ 6 months活动热力图 + Tour proposal + **满宽Refurb suggestions** + Room directory + Image；To Do区只放真实事实源（GovernanceTo Do、dead/queued 作业、Search exposures），健康分标注为启发式 |
| **Recall** | — | 真实Recall实验台：命中行给Stable ID、revision id、得分、命中通道（keyword / semantic / 边扩展）与demoteStatus，点On看Placard |
| **Memory** | Records / Timeline / Entities / Atlas | Records＝全量memory base（Room / Status / content过滤、双Sort、批量遗忘 / Restore、行内「身份与修订」抽屉）；Timeline＝episode 情景Timeline与Nearby扩展；Entities＝ experts物 / Projects / Tools / ConceptsDictionary；Atlas＝Floorplan / 力导向Graph / 回忆螺旋 |
| **Governance** | Conflicts / Beliefs | Conflicts队列（双侧Memory与稳定 id，处置前说清后果）；Beliefs证据链与Held / Stale / RefutedGovernance |
| **Replay** | — | 注入快照 head / detail：逐字 packet、完整哈希、Selected memories id、curated 与压缩元Data，只展示事实源不重建 |
| **System** | Runtime / Storage / Model与Dictionary / 历史Backfill / Jev / Daily志 | Version handshake与Durable job queue；三个分Store的规模与Daily志占用；宫殿language、Panellanguage与Dictionaryedit器；历史Backfill（估算 → Run → 续跑）；Jev Jev配置与观测；双Storemerge的 op_log 审计流水 |

Language随Panel切换（Chinese / English / Русский），服务端理由文案FollowPanellanguage。Data经回环 API `/api/engram/*`（写操作校验回环 Origin + 环绕三重防线）；Panel与后端进程版本mixed时Top出现重启提醒横幅。headless 等none webServer 的Group合不MountPanel，其余能力不受影响。v0.7 时代逐版本的Panel演进Records见 [docs/panel-history-v0.7.md](docs/panel-history-v0.7.md)。

## On发

```sh
pnpm install            # prepare 会把 @deepseek-ai/* peer 从 ../deepseek-harness symlink 进来（需先In harness Repository pnpm install && pnpm run build）；lib/ 缺失时AutomaticRun pnpm bundle
pnpm test               # 单测 + Group合测试；真实嵌入 e2e：ENGRAM_E2E=1（可配 HF_ENDPOINT）且network可达时Run
pnpm typecheck
pnpm bundle
```

### 提 PR 前自检

```sh
node scripts/check-messages.mjs   # 代码请求的键都已声明、none孤立键、zh/en/ru 三语配平
node scripts/check-patterns.mjs   # 密钥清洗与闲聊拒收在所有已声明language下的行为
pnpm typecheck
pnpm test
```

前两项在干净的检出上即可运行，CI（`.github/workflows/checks.yml`）跑的正yes这两项，另加 `pnpm install --frozen-lockfile --ignore-scripts`。后三项不行：`@deepseek-ai/*` peer 不In npm 上（见 `.npmrc`），只In `deepseek-harness` Workspace内才能解析。提 PR 前请在本地把四项都跑一遍——字典漏配会在Local大声报错，而依赖language环境的断言（断言Chinese文本，而 `language: 'auto'` 落到On发者自己的language环境）只在别 experts的机器上变红。

Install与构建脚本的两点说明：

- **GitHub 源码快捷Install**（`dsh plugin add github:kenz1117/dsh-engram`）拿的yes源码，`prepare` 会In `lib/` 缺失时AutomaticRun `pnpm bundle` 现场构建——Failed时看 prepare Daily志的 bundle 段，而不yes宿主报的 "no entry point"。
- **原生依赖构建许可**：`@huggingface/transformers` 传递的原生包（`onnxruntime-node`、`sharp`、`protobufjs`）需要 pnpm 允许运行构建脚本。pnpm 11 已忽略包内 `pnpm.onlyBuiltDependencies` 声明，许可必须写在**使用方**（profile / workspace）侧——pnpm 11 用 `pnpm-workspace.yaml` 的 `allowBuilds`，pnpm 10 用同Document的 `onlyBuiltDependencies`。未许可时 pnpm 会Skipped这些构建并把Skipped列为错误，嵌入功能不可用；许可后重装即可。

## Model Experience

### Request context and condition

#### What the model sees

Session每轮第一步追加一条 plugin Source的 user 快照：`User memory profile (dsh-engram, cross-session) — Grand Hall (always present):` 加用户级Memory列表（默认至More 8 条且整段不超过 1024 token Budget，超Budget bar目demote为 `#id` 索引行，`injectProfile: false` Close），行内带宫殿坐标（`Room#桩位`）；有到期review条目时end追加一行提示Today待回忆条数。Tool calls结果为纯文本行列表（含 `id=`、scope/kind 标注、桩位坐标、contradiction candidates提示与demote说明）。Automatic摄取与蒸馏各产生一writes辅助 LLM Calls（独立于主Conversation计费路径，带 purpose 归因）。

#### Token effect

Image注入为条件性Pin成本（受条数上限与 token Budget双重约束）；Tools schema 为常驻成本（23窄ParametersTools）。

#### KV Cache effect

Image文本随memory basecontent变化——变化只体Now新Session或MemoryUpdate后的轮writes边界；同一Session内注入content不变时前缀保持稳定；Tools schema 恒定，不影响前缀。

## Known Limitations and Deferred Work

- **Conflicts判定默认none LLM** —— written to时默认仅按向量相似度（≥0.88）报告候选并建边，semanticConflicts的Confirm留给Model/用户Jev与蒸馏；可配 Jev Jev（Panel「Jev」tab 或 yml `jev`）让band与Conflicts边走外部判决，Jev 不可用时fallback本默认。
- **嵌入器demote期间的Memorynone向量** —— Model未就绪时written to的Memory不参与semantic道；semantic上线后跑一writes `pnpm backfill` 补算存量向量（`pnpm build` 的ModelCache就绪后Run，可经 `HF_ENDPOINT` 配镜像）。**换Model同理**：换了 `embedder.localModel`（或从 local 换到 remote）之后，Store中旧向量与新Model维度不同，written to会被拒绝——要么把Model改回去，要么用新Model重建All向量：`pnpm backfill -- --force --model <id>`（`--pooling mean|cls` 可选，`--cache-dir` 指向已有ModelCache可省一writesdownloads）。脚本扫描 `<dbDir>` 下的All分Store（`user.db`、`shared.db`、`project-<hash>.db`）；不带 `--force` 时若Model维度与Store中已有向量不符，它在written to前就报错退出，绝不会写出混合维度的Store。远程嵌入器（`kind: remote`）不在脚本覆盖Scope——它跑的yes本地 transformers.js Reasoning，复现不了远程端点的向量。

## 路线图

宫殿 IA（Location当索引 / Pin路线 / Retrieval练习）yes索引与审计骨架，按三阶段向 AI 原生的Memory分层与时序事实Atlas演进；实际发版按 0.0.1 递增，阶段代号仅为规划Tags：

- **阶段一 · 飞轮闭环补强**（Done，v0.7.8）：written to四态回报（ACCEPT Added / MERGE merge强化 / DROP 低熵 / DEFER Conflicts待Jev，`engram_save` 与摄取逐条Back处置）；Image注入改分级递减Budget（首条 160 字、逐条 ×0.9、下限 24，可配置）；脱敏扩充Chinese密码 / 身份证 / 手机号三类；证据门收尾提醒（轮writes结束存在未判定 assess 批writes时注入提醒）。
- **阶段二 · Memory分层**：ImageEditor（**Done，v0.7.9**：`engram_profile_edit` Tools + 版本链rollback + Panel diff 审计，Letta core-memory 路线）；episode 情景Memory独立Timeline索引（**Done，v0.7.9**：`engram_episode_timeline` Tools，DateScope、按Session分Group、Time proximity expansion，episode 专用索引；Panel「往事」视图 + 摄取期与实时Session摘要Group头同批completed）；BeliefsAutomatic巩固（**Done，v0.7.14**：observations 表（schema v12）带证据链与证据数，Session结束按 `reflect: suggest|auto` 触发，近邻归并持续细化、stale 新鲜度复核四动作 form/refine/still/refute，active Beliefs随Image注入；Manual `engram_distill` 蒸馏保留）；Room容量与门牌评分可配置化（默认与现状一致）。
- **阶段三 · 时序事实Atlas**：Entities表与written to期Entities抽Cancel歧（**Done，v0.7.10**：entities + node_entities 两表，摄取/Save的辅助 LLM Output带 entities 提及并消解落Store，`engram_search` 命中附EntitiesTags，Panel「Entities」视图 + Entities回环路由）；事实级三元Group携带 `valid_at` / `invalid_at` Time window，Conflict事实软失效不Deleted（**Done，v0.7.11**：facts 表（Entities + Time window + `replaced_by` + SourceMemory），摄取辅助 LLM Output带 facts 并按Entities归一化匹配落表，`replaces` 声明软失效链，`engram_search` 加 `asOf` 时点回看，`engram_facts` Tools读写事实链，PanelEntities页事实链Details）；supports / refines / related Automatic建边；LoCoMo-zh 评测集进 CI 作Retrieval质量回归门（Confirm性 Recall@k / MRR 指标）。

## 致谢

感谢社区贡献者让这个Projects更好：

- **[@dsv180](https://github.com/dsv180)** —— 最高产的社区贡献者。[PR #13](https://github.com/kenz1117/dsh-engram/pull/13)：宿主Messages目录双语化（zh/en）+ language模式验收脚本（`check-patterns` 的骨架）。[PR #22](https://github.com/kenz1117/dsh-engram/pull/22)：CI 门禁，让两道不依赖私有 peer 的检查在托管 runner 上真实Run。[PR #23](https://github.com/kenz1117/dsh-engram/pull/23)：`peerDependenciesMeta` 全 peer optional——修复了 0.8.0–0.8.14 十五个版本在干净环境装不上的打包事故。另有 [PR #20](https://github.com/kenz1117/dsh-engram/pull/20)（Jev 凭据上收宿主、插件不再存密钥）与 [PR #21](https://github.com/kenz1117/dsh-engram/pull/21)（嵌入器成为部署Property + 维度硬守卫）在评审中。每个 PR 都附双语说明、逐项验证证据与诚实的限制清单——协作范本。
- **[@FeiLZX3Km](https://github.com/FeiLZX3Km)** —— [Issue #19](https://github.com/kenz1117/dsh-engram/issues/19)：三轮高质量 UI 反馈（侧栏Icon回归单色、Refurb suggestions满宽铺On + 弹窗可读性），每轮都附自己的代码排查，v0.8.15 / v0.9.1 的界面修复直接源于此。
- **[@lujfsd](https://github.com/lujfsd)（路杰锋）** —— [PR #2](https://github.com/kenz1117/dsh-engram/pull/2)：适配新版 dsh 的 `sessionPersistence` Read-only句柄（新版已Remove `load()`）、摄取逐条判宫殿（提炼OutputAdded `scope`）、Project palace随Workspace切换（`GET /api/engram/workspaces` + PanelWorkspace选择器），并把none git 时的分Store命名从「cwd 前 12 字符」改为 cwd 全量 sha256（修掉同前缀目录撞Store）。随 PR 附 17 条测试与两份DesignDocs。
- **[@f0909172434](https://github.com/f0909172434)** —— [PR #4](https://github.com/kenz1117/dsh-engram/pull/4)：遗留ProjectsStore迁移的归属Governance——eager 迁移改名时补写 JSON 墓碑 sidecar（`<旧Store名>.migrated-to`，独占Create + 0o600，Records `migratedTo` / `claimedByCwd` / `claimedAt`），后续Workspace撞上同一旧命名时收到指向先前归属的告警；Added `legacyMigration` 配置（`eager` 默认Automatic迁移 / `conservative` 不动旧Store只告警），迁移结果细化为 `renamed` / `kept-both` / `deferred` / `already-migrated` 等Status。随 PR 附测试。

## License

[MIT](LICENSE) © 2026 KenZ (kenz1117)

### 旧ProjectsStore迁移策略

`legacyMigration: eager` (default)保留现有Automatic改名行为：只有旧Store存在时迁移，
并written to私有 JSON Records `<旧Document名>.migrated-to`，包含 `migratedTo`、
`claimedByCwd` 和 `claimedAt`（ISO 时间）。后续Workspace碰到相同的旧命名时，
会收到指出先前归属的告警。Records证明的yesDocument迁移，**不证明每条Memory的归属**。
相同 origin 的 worktree 仍共用新Store。

若需保守升级，在升级／Open旧Data**之前**于插件 `cordis.yml` 配置中设置
`legacyMigration: conservative`。旧Store保持原状，告警列出旧名和拟用的新名，
SessionContinue使用Start的emptyProjectsStore。none论何种策略，新旧Store并存时都不移动或merge。
此选项不能Undo已经发生的迁移。

 experts工处理前，Stop使用该目录的所有宿主，备份DataStore和 SQLite sidecar，
通过 `engram_review` / `engram_export` 审核、Export相OffMemory。
**新Store可能已有新Memory，不要直接用旧Store覆盖它**；应审核两边后 experts工重新归属。
不AutomaticCategory、split、merge或DeletedData。

Records包含本地Workspace路径，请作为私有元Data保管。Records损坏或不可读时迁移会报错；
Restore旧Store备份后若旁边已有迁移Records，也需 experts工Confirm。改名与写Records不yes原子事务，
亦不提供跨进程锁；请先StopOther宿主。写RecordsFailed会明确报出旧名和新名，
此时DataStoreedited名，不Automaticrollback。若在两步之间崩溃，Try again前应检查DocumentStatus。
本功能不会为历史迁移补造Records。
