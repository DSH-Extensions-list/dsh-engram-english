# Changelog

本Projects遵循 [Keep a Changelog](https://keepachangelog.com/zh-CN/1.1.0/) 与 [semantic化版本](https://semver.org/lang/zh-CN/)。

- npm 包：`@kenz1117/dsh-engram`
- Install：`dsh plugin --profile web add @kenz1117/dsh-engram`
- Repository：<https://github.com/kenz1117/dsh-engram>

> **v0.x 阶段的版本约定**：`0.MINOR.PATCH` 里的 `MINOR` yes**功能批writes**而不yes稳定性刻度——0.7.x 整批yes「Memory Palace信息架构」，0.8.x 整批yes「Panel重构」，0.9.x 整批yes「稳定性与可审计」，0.10.x 整批yes「Morelanguage与Data驱动」。同一批writes内的 `PATCH` 位通常yes修复与加固。

## [0.13.10] - 2026-11-11

### Fixed

- **`topActive` 第二Sort键从 `confidence` 换成 `created_at DESC, id ASC`（#53）**：蒸馏候选池（30 条）、反思池（300 条）、巩固merge与**SessionImage注入**共用的候选查询此前按 `importance DESC, confidence DESC` Sort，但 0.12.0 把「Search exposures → confidence 增量」拆掉之后，confidence 的written to端只剩两条缺省路径——`engram_save` 缺省 0.5、蒸馏产物Inherit簇均值（实测落 0.3~0.35）；会真实改它的 `reinforce`/`reportOutcome` 只由主动反馈触发，none任何流程AutomaticCalls。同一个 importance 档位里，手写条目恒排在摄取/蒸馏产物前面，候选池与Image注入的实际writes序等于「按谁写的排」。换成 `created_at DESC` 给同分并列一个 recency calls序，`id ASC` 让 LIMIT 截断点落在并列区时结果Confirm（SQLite 不保证none tiebreak 的Back顺序稳定）。报告 experts zhongcrazyx completed静态分析、SQL 对拍与Runtime验证（注入快照与新Sort吻合）；测试钉住三件事：旧Sort霸榜的高 confidence 旧条目被截掉、同刻并列由 id ASC 决出且与 confidence noneOff、DuplicateCalls结果一致。

## [0.13.9] - 2026-11-07

### Fixed

- **弱网Install不再整体Failed（#50 的根因面，保证用户先能Install）**：本地嵌入引擎（transformers.js + onnxruntime 原生件，解包约 307MB）从 `dependencies` 移到 `optionalDependencies`——pnpm/npm 对 optional 的Failedyes宽容的（对照实验：不存在的包直接Skipped、install exit 0）。network停摆时InstallSkipped重型引擎并照常completed，插件的Memorywritten to、keywordRetrieval、Panel与AllGovernance能力**不受影响**；只yessemanticRetrieval暂缺，已有的demote路径全程显式：启动 `core.warnEmbedderUnavailable` 告警、`vectors-missing` 审计、Panel「System · Runtime」Model列表为empty。network稳scheduledUpdate/重装插件补上引擎，存量Memory跑 `pnpm backfill` 补算向量。`backfill-embeddings.mjs` 对引擎缺位给出 experts话指引而非裸栈。

## [0.13.8] - 2026-11-07

### Added

- **布局门禁（`pnpm check:layout`，已接 CI）**：0.13.x 连发七个补丁几乎全靠用户截图打回——typecheck/lint/tests/Dictionary没有一项验证渲染，缺的正yes这一层。门禁把真实 CSS 模块拼进Pin DOM，headless Chrome 实测 14 项不变量：Workfacescontent区自滚且宿主不出现第二条滚动条、导航Title单行、页面Title唯一且顺序正确、Confirm按钮宽度有绝对上限、抽屉Body占满、身份芯片/Model名单行、批量条Layout。本批它自己就抓到两处：Confirm态按钮只有容器相对上限（长Confirm语仍会占 364px），改为 24em 绝对截断；抽屉Body宽度断言补上 padding 容差。

## [0.13.7] - 2026-11-07

### Fixed

- **`reflect: 'suggest'` 的巩固Recurringyes断的（#50）**：Session结束登Memory `reflect-pending`，但没有任何机制提醒 agent Calls `engram_reflect`——登Memory永远躺在Store里，Memory只进不出Consolidated。Now待巩固提醒骑Image包注入（与「due today」同法）：SessionOn始与压缩重注时若存在登Memory，包尾附一行提示Calls `engram_reflect`；Tools消费后计数归零，hash 变化自然触发重注撤掉提醒。Added配置 `reflectReminder`（默认 true）可Off。提醒只数当前Workspace的两个Store（user + 当前ProjectsStore），OtherProjectsStore由它们自己的Session提醒。

### Added

- **「只摄取不Consolidated」的Group合守卫（#50）**：`ingest` Turn on而 `reflect` 为 `off` 时——Memory只收集不蒸馏、读起来就yes散的——启动Daily志明确告警，Panel「System · Runtime」同步提示（`GET /api/engram/version` Now带出 ingest/reflect 档位，旧客户端忽略Unknown字段）。
- **README 推荐Group合表**：按使用方式（编程为主 / 写作文书 / 轻量试用 / Manual管理）各给一行可直接抄的 `ingest`+`reflect` 配置；「Memory散」的最常见成因（巩固没On、巩固路由用了轻量Model）写在表前。Added「升级」一节：市场/命令行的跨Source保护、「允许构建脚本」提示的真相（只影响可选 CUDA 件）、ModelCache跨升级持久。

## [0.13.6] - 2026-11-06

### Fixed

- **Data目录不再none视 `DSH_HOME`（#51）**：`dbDir` 默认值此前写死 `~/.dsh/engram`，而宿主把用户Data放In `$DSH_HOME`（缺省 `~/.dsh`）。把 home 搬到别的盘之后，memory base、Dictionary与ModelCache仍然静默留在旧盘上—— experts以为在用新盘，插件读的yes旧盘。Now默认值跟着 `$DSH_HOME` 走（契约与宿主的 `@deepseek-ai/dsh-home-paths` 一致：显式值 > `$DSH_HOME` > `~/.dsh`，empty白视同Not set）；显式写了 `dbDir` 的 experts不受影响。
- **旧目录收编，避免Store静默分裂**：默认值改跟 home 之后，旧盘上还留着的Store会变成「另一处的一份」——插件读新盘， experts以为Memory丢了。插件启动时先把 `~/.dsh/engram` 收编过来：同盘直接改名，跨盘先复制到临时目录、逐个Document核对大小再改名并删旧；两边都有Store时不Automaticmerge（Continue用旧目录并报出两个路径），搬运Failed也Continue用旧目录——Data看不见比Data待在新盘上更糟。每个结局都写进启动Daily志。`legacyMigration: conservative` 只报不动。
- **Panel说清Data在哪儿**：「System · Storage」同时报出Data目录与Harness home，Store不In home 之下时明确提示（此前这件事完全静默，只能去读源码才知道）。

## [0.13.5] - 2026-11-06

### Fixed

- **回归：Memory列表与Bulk actions条的布局规则被误删**（0.13.0–0.13.4 均受影响）。0.13.0 的「死 CSS Clear」脚本在删单行规则时，越过了本该停下的Location，把中间所有活着的规则一起吃掉——包括 `.item` 的Base布局（列表行不再yes复选框 / Body / 操作三列Grid，而yes塌成一列）、`.itemList`、`.batchBar` 的计数与按钮Layout，以及 `@keyframes itemIn` / `barUp` 两个Motion。结果：选中行只剩一条竖线、Bulk actions条里的「已选 N 条」字号失控、按钮yes默认尺寸，行内操作按钮掉到下一行。AllRestore。
- 顺带补上两条一直缺失的规则：热力图零级格子（`.heatmapL0`，此前 className 里带着 `undefined`）与ImportPreview行（`.importRow`）。

### Added

- **CSS 类覆盖门禁**（`scripts/check-css-classes.mjs`，已接入 CI）：CSS Modules 的类名yes字符串，Type检查完全看不见——规则被删掉时，编译、单测、lint 全绿，只有真机才发现。这writes事故之后补上这道门：凡yes代码里 `styles.X` 用到的类，必须在某个客户端样式模块里有对应规则，no则 CI 红灯。门禁同时报出「声明了但没 experts用」的类（信息项，供后续Clear）。

## [0.13.4] - 2026-11-06

### Fixed

- **Refurb suggestions里理由被挤成一列竖字**：Confirm按钮把整句说明当Tags（「Confirmmerge 2 条相似Memory？将触发 engram_distill 把它们归纳为 1 条高层规律。」），而该按钮yes `flex: none`，于yes它吃掉整行宽度、把理由压到几个像素宽——Chinese一个字一行。Confirm按钮改用短词（「Confirmmerge」），完整说明移到悬浮提示；Confirm态的按钮也加了宽度上限，长Tags不再能撑爆所在行。Governance面的「Close this memory」Confirm语同样处理。

## [0.13.3] - 2026-11-06

### Fixed

- **Runtime页的嵌入Model列表叠字**：Model行借用了Daily志行的Pin五列（色点 · 时间 · 操作 · 宫殿 · 摘要），Model名这种长串落进第一列那 8px 的轨道，被压成「bge- / small / zh」竖排，还和Type列叠在一起。Daily志行yes短字段专用，Model与作业改用各自的行样式（Name可省略、不换行，Type与数值各自一列）；版本卡的Title也不再和第一行Duplicate（改「Version handshake」）。
- **Storage页把五个数字挤在一格**：总数 / Active / Archived / Forgotten / Daily志行数原本塞进一个会被截断的单元格里，改成一张表，一列一个数。
- **Memory placard抽屉Layout**：PlacardBody原本落在双栏布局的左半栏，右半栏In “没有修订链」时整片empty着，Body一行只有十几个字；uuid 芯片还被拆成两行。抽屉放宽到 720px，Details区改单列（Body占满、修订链与操作Records依writes排在下面），Stable ID与Current revision收成一行元信息（超出省略、完整值In title 里），Body用更宽的行距。

## [0.13.2] - 2026-11-06

### Fixed

- **二级导航上方冒出Duplicate的WorkfacesTitle**：`ContextNav` 用 `<fieldset>+<legend>` 做可访问Name，但 `.visuallyHidden` 只定义In `ui.module.css`，shell 自己那份yes `undefined`——图例于yes以可见Title的样子漏出来，「Memory」在页面上出现两遍。改用 `<nav aria-label>`，不再依赖看不见的图例。
- **页眉与二级导航的顺序**：二级导航原本在页眉之上（导航先、Title后），与「先认路、再选视图」的阅读顺序相反，也和其余Workfacesmixed。Now页眉统一提到Workfacescontent之上：Title → 副Title → 二级导航 → content；页眉与页签之间的两道横线并排改为一道（分隔线交给页签行）。

## [0.13.1] - 2026-11-06

### Fixed

- **Contradiction governance里两个同名「Close this memory」按钮**：一个闭左侧Memory、一个闭右侧Memory，但按钮文字完全一样—— expertsnone法知道点的yes哪一侧，闭错一条就yes不可逆的错删。Now闭馆按钮长在它要闭的那条Memory卡片上（卡片里本来就有Stable ID），点哪张卡闭哪条；「Reclassify as related」留在卡片下方，并注明它作用于这一对Conflicts、两条都保留。闭馆改成两段式Confirm，Confirm语写明后果：软删该条Memory，并以它为证据的Beliefs按证据死亡规则处置。

## [0.13.0] - 2026-11-06

### Changed

- **Panel改造成六Workfaces控制台**：十个横向页签换成左侧Workfaces导航——Today / Recall / Memory / Governance / Replay / System，每个Workfaces内部再切二级视图。宫殿隐喻（Floorplan、走廊Atlas、回忆螺旋）demote为「Memory」Workfaces里的可视化模块，不再yes导航language；品牌色从主色降为强调色，只用在选中态、focus 与Off键Status上。
- **TodayToday**：Remove合成 sparkline——没有真实时间序列的指标不画线；改为展示真实written to序列，并把GovernanceTo Do、dead/queued 作业、作业队列不可用与真实Search exposures分On呈现；健康分明确标注为启发式。
- **RecallWorkfaces**：真实Recall实验台独立成面，命中行展示Stable ID、revision id、得分、命中通道（keyword / semantic / 边扩展）与demoteStatus；demoteRetrieval必须显式标出，不能让keyword结果冒充完整Recall。
- **MemoryWorkfaces**：分Records / Timeline / Entities / Atlas。Records列表自己持有查询Status与请求——Data只在Open这一面时才拉，OtherWorkfaces不再为一份看不见的列表付请求代价；Details抽屉升级为「身份与修订」Panel，展示稳定 `memoryId`、当前 revision id、修订链、取代与ConflictsOff系及Source。
- **GovernanceWorkfaces**：分Conflicts / Beliefs。Conflicts队列展示双侧Memory与稳定 id，处置前说清后果（Reclassify as related保留两条、闭馆指定侧软删并触发证据死亡传播）；Beliefs按Held / Stale / Refuted给出不同动作。
- **SystemWorkfaces**：分Runtime / Storage / Model与Dictionary / 历史Backfill / Jev / Daily志。Runtime展示插件与PanelVersion handshake、Durable job queue与嵌入ModelStatus；Storage展示三个分Store的规模与Daily志占用；Model与Dictionary收编宫殿language、Panellanguage与Dictionaryedit器。
- **控制台To Dodemote**：后端进程版本落后（路由不存在）时如实说「不可用」，不再把版本偏斜当故障甩给 experts看。
- **Panel代码分层**：视图与共用件不再回引 `EngramPanel.tsx`——client 半按 CJS 闭包工厂打包，Recurring依赖在产物里表现为「用到时还yes undefined」。Added `src/client/ui/model.ts`（Memory行 / 列Table / 审计Back形状与纯Display辅助）与 `src/client/ui/panel-kit.tsx`（Translate签名、kind Tags、Room色相、select 包装、empty态、页眉、指标卡），视图落In `src/client/views/`（Recall / Governance / Memory四Panel / System）。Panel主体从 6,500 行降到 2,700 行。
- **色相单一事实源**：Room色相与Entities色点统一到 CSS 变量。原先 `KIND_PALETTE` 的硬编码 hex 与 CSS 的 `--room-*` yes两套不同色值，pills、Room索引、Floorplan、Graph与时间螺旋各画各的；Now JS 只References变量名，暗色换色不必再改十六进制。
- **死代码Clear**：十个旧 tab 时代的样式类（25）与对应文案键（24）Deleted；`scripts/check-messages.mjs` 改为在整个 client 目录里查找 `SKIP_KEY`，卡片搬家不再让门禁失去支点。

### Fixed

- **左侧Workfaces导航字竖排**：导航项用 `grid-template-columns: 18px minmax(0,1fr)` 预留Icon列，Icon缺席时ChineseTitle落进 18px 的那一列，一个字一行读起来像坏掉了。改为 flex 布局、Title不换行、导航列Pin 132px，并给六个Workfaces补上Icon。
- **Panelnone法上下滚动**：content列不yes flex 容器，内层滚动区的 `flex: 1` 与 `min-height: 0` 失效，content溢出后被Panel根节点的 `overflow: hidden` 裁掉——整页既不滚也不Display。补上content列的 flex 容器与WorkfacesGrid的 `flex: 1`，滚动回到Workfacescontent区，宿主页面不再出现第二条滚动条。
- **Title与二级导航**：二级视图名仍yes旧页签名（「陈展 / 往事 / Entities / 回忆螺旋」），且胶囊按钮读起来像筛选器。改为Workfaces名做Title、副Title随二级视图切换，二级导航换成下划线页签。
- **破坏性操作不再弹SystemDialog**：翻Start议的demote / merge改用两段式Confirm按钮，与Other写操作一致——原生 `window.confirm` 会弹出脱离Panel样式的Window，也读不到Dictionary。
- **滚动条样式作用域化**：顶层 `::-webkit-scrollbar` 伪ElementIn CSS modules 里会Globalactive，把整个 DSH 界面的滚动条一起changed；Now限定在Panel根节点之内。
- **Status条Duplicate表达作用域**：Status条里的「Palace」与 Header 的作用域三宫格yes同一件事，删掉；Status条没有要报的事时不占Location。

## [0.12.0] - 2026-11-06

### Added

- **0.12.0 工程契约**：Added `docs/engram-0.12.0.md`，frozenStable ID/修订、统一写边界、曝光与有效反馈分离、session 隔离、持久作业、v13 Data迁移与回退、真实观测和分阶段验收口径。
- **稳定逻辑身份（schema v15）**：`MemoryRecord` Added `memoryId`；存量Store v14→v15 Automatic补齐 `memory_id=id`，UpdateInheritStable ID，`nodes.id` 保留为 revision id。Added `getMemory(memoryId)` 解析Current revision，`updateMemory` 可按 revision id 或Stable ID定位；修订链按 `memory_id` 聚合，任一 revision 可看完整历史。归因与注入快照使用Stable ID，Export/Import保留 `memoryId`。同一Stable ID至More一个 active：restore 旧 revision 会先Archived其余 active 兄弟并重建 supersedes 方向。升版快照改用 `VACUUM INTO` 生成一致性Document，不再直接拷贝可能缺 WAL 尾巴的主Document。
- **v13 全保真迁移演练**：Added真实 v13 形态 SQLite fixture（none `memory_id`、none `injection_snapshots`），覆盖All业务域与Status键；验证升级后行数、Stable ID、修订链、FTS、Timeline、Image版本、事实链、Beliefs证据和 v14 快照written to，并演练 `.pre-migration` 原Store回退。
- **本地评测基线**：Added `src/evaluation/recall.ts` 与Confirm性 DSH 工作流 fixture，覆盖 Recall@k / Hit@k / MRR、知识UpdateBackCurrent revision、episode Timeline过滤与时间顺序、noneOff查询拒答和Stable ID provenance；注入Replay校验 API packet 与实际 packet 逐字一致。v15 keyworddemote基线已RecordsIn `docs/evaluation-baseline-v15.md`。
- **Injection Snapshot（schema v14）**：Added `injection_snapshots`，持久化实际交给宿主的最终注入包、SHA-256、Selected memories ID、curated 标Memory、compaction 计数与时间；`GET /api/engram/injections` 只Back head，`GET /api/engram/injection` 按 id Back完整Body，为后续Recall台和逐字Replay提供事实源。
- **上下文ReplayWorkfaces**：PanelAdded「上下文Replay」页，按 scope 与 sessionId 过滤注入历史；列表只取 head（时间、session、Packet size、短哈希、入选数、压缩数），Details按 id 按需读取完整最终包、完整 SHA-256 与稳定Memory ID。UI 不重建相似文本，直接展示 `packet` 事实源；zh/en/ru 文案齐备。
- **Recall台 provenance 与Contradiction governance**：Recall测试台命中行AddedStable ID、revision ID 和“viewPlacard”入口。AddedGovernance队列 `GET/POST /api/engram/governance` 与 store 层 `listContradictions/resolveContradiction`：只列双侧 active 的 contradicts 边，支持「Reclassify as related」（保留两条并写 governance 审计）与「闭馆指定侧」（软删并触发证据死亡传播）。走廊页AddedGovernance卡片，处置前完整展示两条Memory与稳定 ID；zh/en/ru 齐备。
- **Durable Jobs**：Added独立 `jobs.db` 与租约式作业队列（幂等入队、attempts、runAfter、checkpoint、dead-letter、过期租约Restore）。`reflect: 'auto'` 从 fire-and-forget 改为持久入队和串行 worker：120 秒RunBudget、180 秒租约，启动时Automatic接管上一进程遗留/过期 running 作业，Uninstall时Cancel在途Run并保留可RestoreStatus；`suggest` 行为不变。作业 ID 带 dispose 事件水位，RestoreSession的新content不会因旧 done/dead 占用 ID 而丢失；损坏队首隔离为 dead；done/dead 与 injection snapshots 均按 366 天保留WindowClear。

### Changed

- **统一MemoryUpdate命令**：Tools `engram_update` 与Panel `POST /update` 共用 application command，集中Runprotocol剥离、密钥脱敏、kind/importance merge和重嵌。`UpdateInput.embedding` Added `null` semantic：content变化但 embedder 不可用时Refute旧向量，不再让旧BodysemanticContinue命中。
- **Search exposures与有效反馈分离**：`search()` 不再Edited `confidence`、`accessCount` 或 `lastAccessedAt`，More查询不再形成自我强化环。每writesRetrieval只写一行 `search-exposure` 审计（去重命中 ID、scope、limit、degraded 与不可逆查询摘要）；`reportOutcome` / `reinforce` Continue作为独立真实反馈通道。
- **注入Status按 session 隔离**：Image去重、compaction 计数、证据提醒和Session路由不再Shared进程级单槽；Session销毁时Clear对应Status，历史任务仅保留独立的最近路由视图。

### Fixed

- `injectProfile: false` 在Automatic摄取Turn on时不再意外注入Image；Image与摄取均Close时，证据提醒仍可独立注册。选择理由和review到期提示Now进入实际注入包，不再只参与 hash。
- 末轮摄取的empty答案、不可解析Output和全候选异常Failed统一落 pending；Restore后写 done，DuplicateRun保持幂等。低活动、nonecontent等Confirm性Skipped不误入Try again队列。

### Security

- Jev endpoint override 仅允许与 provider 已配置 `baseURL` 同源的 http(s) 地址；跨 origin 明确拒绝，不再把宿主 Bearer 凭据发往PanelCalls方指定的任意服务。同源Custom路径保持可用，userinfo、异protocol与异端口均在拒绝Scope内。

## [0.11.6] - 2026-11-06

### Added

- **#49（Panel访问面OnOff）**：Added配置 `panelAccess: 'loopback' | 'lan'`（默认 `loopback`,行为不变）。`lan` 档在宿主 webServer 绑定 `0.0.0.0` 时accept私网访问Panel API:peer accept RFC1918(10/8、172.16/12、192.168/16)、IPv6 ULA(fc/fd)与链路本地(fe80-febf)及 mapped 地址;Host/Origin 放宽到字面 IP、localhost、单Tag name、`*.local`(MoreTags域名仍拒,防 DNS rebinding);公网 IP 任何档位都拒——公网暴露走反代到回环上游。附修:带方括号的 IPv6 Host(`[::1]:5655`)此前必被 403,现正确accept。API none鉴权,LAN 档等于把memory base读写在信任的局域网内公On,请勿在不可信networkTurn on。
- **#48（Beliefs experts工更正入口）**：Added `POST /api/engram/observation-status`(active=Confirm维持 / refuted=no定 / stale=标Stale,404/400 纯英文错误与邻居路由同款)；PanelBeliefs卡片按Status给操作——active 可「Refute」(两writes点击Confirm,同Daily志Clear模式)、stale 可「Refute/Restore」、refuted 可「Restore」,Done局部Refresh + Toast,中/英/俄三语齐备。

### Fixed

- **#48（Beliefs层减法：证据死亡传播）**：源Memory被遗忘/取代/蒸馏Archived/衰减Archived时,以它为证据的Beliefs此前永远保持 active、proof_count 不减——ConflictsBeliefs长期并存且注入每轮上下文(fgql 实测:错的名字Beliefs proofCount 2 > 正确的 1)。Now四条Archived路径统一反查 `sources_json`:摘除死源并扣减 proof_count,证据耗尽置 refuted(刷 updated_at),尚有余证置 stale Stale(不刷 updated_at,复核队列顺序semantic不变);restore 有意不联动(节点复活,Beliefs保持既有Status等下writes复核Confirm)。
- **message 字典门禁红灯（v0.11.4 遗留,0.11.5 带红发版）**：#45 批writesAdded的 `tool.reflectDropped` 缺俄语Translate、`core.warnReflectBatchDropped` 有词条但从未被代码接线——`check-messages` 门禁自 0.11.4 起In main 与 v0.11.5 tag 上都yes红的。Now补齐 ru 词条、把丢弃计数接到Session结束后台巩固的告警处（此前 `dropped > 0` 时 console 完全沉默）,门禁Restore绿。

## [0.11.5] - 2026-11-02

### Fixed

- **自审批 P1（后台巩固限时失配）**：Session结束的后台Beliefs巩固沿用末轮摄取的 5 秒限时——分片改造后一writes巩固yes 3+ calls串行辅助Calls挤同一Window，真实Model下几乎必然中途夭折，且只 console.warn 不可见（#45 报告者攒 110 条 pending 的帮凶之一）。改为独立的 120 秒Budget（fire-and-forget 观察器仍有上界，进程退出照旧可能先打断）。
- **自审批 P1（启发式去重死支）**：none嵌入器部署的同文去重从未active过——判准 `record.importance > existing.importance` In topActive 的 importance 降序里永假，嵌入不可用时 #42 同款Duplicate刻写在该群体none守卫。Now同文Duplicate不论重要度一律merge（双方入 supersedes 链、每对净减一GroupDuplicate），幸存节点取Group内最大 importance/confidence，重跑幂等。
- **自审批 P1（CI 抖动）**：`store.spec` 的 countOpsByDay 用例存在毫秒级Date边界竞态（查询Window上界在written to前采样，行时间戳可落在窗外），晚间时段随机挂 CI——上界改written to后采样并加松弛。

## [0.11.4] - 2026-11-02

### Fixed

- **#44（Backfill估算的内存雪崩）**：`engram_ingest_history` 估算模式（dryRun）为统计轮writes把Time window内**每个候选Session的完整转写**载入内存并全程持有——内存随Session数线性增长（报告者实测约 5.5MB/Session、峰值 5910MB），桌面宿主 4GB V8 堆顶直接 SIGABRT。Now转写延迟加载：估算数完轮writes即弃（峰值 O(并发 4)），Run路径处理到该Session时再读（峰值 O(1)）；Run期读取Failed（选中后被删/损坏）Skipped该SessionContinue（`session-unreadable`），不再中断整批。
- **#45（Beliefs巩固整批丢弃）**：`engram_reflect` 把最More 30 条候选**全文**塞进一writesCalls，OutputBudget却Pin 900——Output必然截断、整批丢弃、 beliefs永远empty着且none任何报错（报告者一晚攒了 110 条 pending）。Now候选原文截断（200 字，与 stale 证据同纪律）、按 10 条分片Calls、OutputBudget按动作数缩放（底价 + 单动作配额，封顶 8000）、超Budget对半递归Try again；解析Failed或单片仍超限时该片计入新的 `dropped` 计数，Tools体明说可重跑补做——Failed可见。
- **#46（Jev/OpenCode Go protocol不符）**：opencode.ai 系端点的 System One 通道要求稳定的 `x-opencode-session` 头，插件此前不发，Go 订阅下所有Jev请求必然 400 MissingSessionID。Now opencode 端点Automatic带进程级稳定Session头；非 2xx 错误带上游响应体摘录（此前只有裸 `HTTP 400`），MissingSessionID / Model is unavailable / Insufficient funds 可直接分辨。订阅侧Model可用性（jev-1.13 不In Go 的 /models 列表）属上游问题，插件none法修复。

## [0.11.3] - 2026-11-01

### Fixed

- **#42（Consolidatedmerge不收敛）**：merge相似条目时只ArchivedDuplicate项、种子条目保持 active，留下「种子 + 新节点」这对Duplicate active——每writes插件重载触发Consolidated都重新匹配、重新刻写一份副本（issue 报告：同一条 fact 3 天累积 54 份、53 份Archived）。Now两条merge路径（嵌入可用 / 不可用）都把双方送进 supersedes 链，每轮merge净减一GroupDuplicate、一轮收敛；幸存节点Inherit种子向量（不再yes向量Retrieval盲区），importance 取Group内最大。
- **#42 附带（摄取Output超Budget永久卡轮）**：`INGEST_MAX_TOKENS` 硬编码 4000，长转写/一writes贴进数百行Daily志的轮writes提取Output必然超限，且同样Input重跑永远Failed——Backfill轮writes被永久卡死（184 轮Backfill中 2 轮因此Failed）。Now超Budget（max-tokens）Automatic对半切块Try again：More段文本按段对半分、单段文本按换行边界对半切，切到最小粒度仍超限的块放弃（贡献 0 候选），整轮照常收口为 done 而非永久 pending；每块真实Calls均先落SessionDaily志（model-visible ⟺ logged），empty答案Try again漏MemoryDaily志的破口一并补上。

## [0.11.2] - 2026-10-30

### Added

- Panel「Today」侧栏Image下方Added**版本卡**（包名 + 版本 tag + Author/Repository/npm/License，版本与握手横幅同源取自包 package.json）与**MoreArtifacts**广告位（usage-billing / rainbowspeak 两条外链卡，虹语行走真彩虹渐变描边；与 usage-billing Panel的同名广告位互为镜像）

### Changed

- 侧栏入口Icon重绘：国会式宫殿线稿（顶针、顶亭、双层穹顶、中央拱门、鼓座环带、大檐口、山墙、主柱廊、侧翼双排窗、栏杆与台阶全Element保留），`icon.svg` / `icon-256.svg` 同步重绘；描边按渲染尺寸补偿（28 ≈ 0.8px@16 / 0.9px@18），密集细节由抗锯齿呈现雕版质感

## [0.11.1] - 2026-10-28

> 2026-10-27 全面审计（`docs/audit-2026-10-27.md`）的三个修复批writes + 两个用户报告的升级事故
> （#39 / #40），以对抗式审查收口每一批（审查自身抓出 9问题并回修，含 2由修复引入的）。

### Added

- 向量道进程内打包矩阵：Retrieval与written to处置不再逐行反序列化+逐行余弦（5 万行封顶，超出fallback流式扫描；维度取More数派）
- 嵌入器句柄可Try again：Failed to load不Cache（下writesCallsAutomaticTry again）、`await` 带 15 秒宽限期（downloads挂起不再让Save/Retrievalnone限等待）
- op_log 保留Window（366 天，Status型操作行永久保留）挂In 24 小时衰减班车上；遥测聚合改 SQL `GROUP BY`（不再once物化数十万行阻塞进程），Daily折算对夏令时 stable
- Panel `useApi` hook 与 `rpc.ts` / `translate.ts` 抽取：15 places复制的Data获取模式On始收敛
- Biome lint 接入 CI（error 级清零；typescript-eslint Unsupported TS 7，故选自带解析器的 biome）+ vitest coverage 棘轮（服务端基线 stmts 77.9 / lines 80.9）
- 客户端测试从 0 起步：俄语复数选形、Dictionary插值、api() 的 ok-first 解析 / project 注入 / 404 自愈

### Fixed

- **#39（升级事故）**：Windows 上system language读不到（`LC_ALL`/`LC_MESSAGES`/`LANG` In Windows 默认不设），ChineseStore被绑定到 384 维MorelanguageModel，升级后written to整体被维度守卫拒绝。两层修复：① locale 读取补 ICU 兜底（`Intl` 默认 locale，Windows 上即 `GetUserDefaultLocaleName` 的结果）；② language设置为 auto 时，**存量Store自己的 `model_label` 优先于System locale**——TagsRecords了「这些向量yes谁写的」，locale 读错也不再把现成Store推到异维Model上。StartStorenoneTags，走原判定；显式language设置与 `embedder.localModel` 覆盖的优先级不变。
- **#40**：Backfill的「Pending turns」永远降不到零——被节流/noneBody/none路由Skipped的轮writes不写任何幂等键，每writesBackfillRe-scan重新Skipped。NowConfirm性Skipped会落 `ingest-skip` 标Memory（target_id 携带原因），估算把「有 done 或有 skip」都算已处理，且已标Memory轮writes直接Skipped不再重判。
- Panel运维卡（宫殿language/Plugin language）改用 section 级 toast——私有实例的 viewport 从未Mount，修底座/换language/Dictionary操作的提示一直在往虚empty里发
- api() 先判 `response.ok` 再解析 JSON——代理错误页不再伪装成 «Unexpected token <»；project 注入分支不再丢 content-type（注入型 POST 曾被服务端 415 挡下）
- importance/confidence 在入Store单点收口 [0,1]（含 import 路径）——`engram_save` 直收Model给的数，越界值曾能永久霸榜 topActive 进Session注入
- engram_search 的 limit merge parseCountParam（1–50，超界报可Try again错误）
- restore 改为先Off后换：checkpoint 探测拒绝在外部 DSH 进程脚下换StoreDocument，WAL 尾巴先刷进主Document再挪走
- Open/迁移中途Failed的Store句柄就地Close——此前迁移Failed泄漏连接，Windows 上砖死后续 restore
- attachEmbeddings 补维度闸（import/backfill 曾yes混维入Store旁路）；四个向量池过滤损坏 BLOB——单坏行不再炸整个Search/整场 reflect；NaN/∞ 向量拒入Store
- 摄取候选逐条隔离：单条Failed不再弃掉整批后半；全败回合不写 done 键（保留 pending 重放，嵌入端点抖动不再永久丢一轮Memory）
- consolidation：Archived统一走 decay semantic（此前 forget 混进遗忘墓碑桶）、批式嵌入、500 条工作集封顶；修掉种子可与自身merge的存量 bug
- JevConfigCard Failed to load的 toast 死Recurring（toast→重渲染→新对象→再触发）断根
- corridor 端点截顶（默认 400 节点）+ 大Room布局降迭代——整StoreBack曾让走廊页签数秒frozen
- Deleted review-due 死复制品路由；CHANGELOG 补 [0.11.0] 链接References；Tools数文案统一为 23

## [0.11.0] - 2026-10-25

> **0.11 yes新的一批功能**：0.10.x 整批yes「Morelanguage与Data驱动」，0.11.x 整批yes「Panel重排与可靠性修复」。
> Panel的三个视图重排成与首页同一套骨架（同一个底图、同一套尺寸、同一块右侧索引），同时修掉两个
> 真会让 experts返工的缺陷：宫殿languageIn macOS 上被误判成英文（于yes整套向量要重算），以及往事页的
> 「Nearby」按钮一点就崩掉整个视图。

### ⚠️ 升级说明：宫殿language改按操作System的真实language判定

- **以前读的yes进程 locale，Now读的yes操作system language**（macOS 读 `AppleLocale`；`C`/`POSIX` 这类"language没设"不再被当成英文）。ChineseSystem的 macOS 之前被读成 `en`——宫殿language、Goal嵌入Model、乃至整套向量都跟着偏了。
- **对Chinese用户**：修好之后宫殿languageyes `zh`、Modelyes `bge-small-zh`（512 维）——正好和yourStore里已有的向量一致，**不需要重算**，插件会把Store补上Model标Memory。
- **对Another language的用户**：如果your的system language与之前判定出来的不同，宫殿language会变、GoalModel随之变，存量Store的vector dims就对不上了——到「运维 → Memory Palace」里选好language按一writes「Run」重算一遍即可（Panel会先提示"Vectors need recalculating"）。
- 也可以In “运维 → Memory Palace」里Manual把宫殿language钉死，之后不再随system language变化。

### 视觉：Graph改造成「Floorplan + Off系边」

- **Graph不再yes一张满屏乱飞的力导向图**：此前它有自己的 560×320 画布、860px 限宽、5 条竖向色带做背景，节点在色带之间任意漂移，常驻文字Tags互相叠字、还被画布边缘切掉——与Floorplan相比尺寸更大、视觉language完全不同（用户原话：「尺寸也太大了点吧，好丑，和Floorplan差别好大」）。
- **两种视图Now共用同一张建筑底图**：Room排布（`roomBoxes()`）、外墙、中央主通道、门牌样式All抽成共用件，Floorplan与Graph画在同一张 720×380 的图上，**RoomLocation在两个Tags页之间保持不变**（Location当索引）。Graph不再yes「另一张图」，而yesFloorplan上More画了Off系边。
- **Graph的节点锚在Room里**：一个Memory点归入它所属 kind 的Room，房内做一writesConfirm性的排斥松弛（none随机数、none rAF 抖动、不再每帧重渲染），房与房之间才画Off系边——边因此读起来就yes「走廊里的往来」。
- **节点不再常驻文字Tags**（Room小、点More，Tags必糊），改为 hover 出提示卡：Room名 + 完整Placard（按可用宽度截断，不再溢出边框）；hover 节点时高亮它、一跳邻居与相Off边，其余淡出。点圆点仍触发原有的Memory选中，节点也失去了拖拽功能——节点的Location由Room决定，拖走反而破坏「Location即索引」。
- **两个Tags页的骨架一致**：Graph此前独占整行（没有右侧索引条），Now与Floorplan一样yes「左地图 + 右侧Room索引条」，边Type图例移进右侧索引条（只列出当前Store里真实存在的边Type）。
- **自环边不再冒充Off系**：存量Store里有历史脏行（`edges.from_id === edges.to_id`，早期written to路径未挡住自己），这类边画不出线却会被计入「3 条走廊」。客户端Now剔除自环边，图例与「还没有Off系边」提示因此说的yes实话。
- **主通道门牌加底衬**：旋转的「Corridor · main hall」此前被虚线与连线切碎，Now压在边之上、带底衬，两个模式同步受益。

### 视觉：Entities页从「九张大卡」变成「一张卡里九行」

- **Entities页从「九张大卡」变成「一张卡里九行」**：此前每个Entities独占一张满宽卡片，行内大片留白，右端还挂一个Duplicate九遍的「view」按钮。NowEntities列表yes一张卡里的紧凑行（色点 + Name + alias + 类别 + links数 + 箭头），**整行可点**（行本身yes按钮，键盘可达），不再有Duplicate按钮；行内给出类别Tags，色点不再需要靠猜。
- **类别 chips 带上自己的颜色**：过滤 chip 里的色点就yes该类别在图上的颜色，色码自解释；计数从漂在右缘的一行灰字改成一枚药丸（「9Entities」）。
- **运维页yes长页，给它一枚块导航**：Backfill / Plugin language / Memory Palacelanguage / Jev四块Tools从上到下排On，TopAdded**吸顶块导航**（点一下就滚到对应Tools，滚动时不盖住块Title——`scroll-margin-top` 按吸顶条高度留了余量）。块节点用 ref 登Memory，不靠 `document.getElementById`，宿主换Mount点也不失效。
- **解释性长文收进「What is this?」折叠**：Backfill与Jev两块On头的三行说明改为按需展On——Tools页第一屏应该先给控件，想读的 experts点一下就在。
- **「Plugin language」块拆成两Group并排放**：「Now的Dictionary / StartDictionary」原本挤在同一条长行里（一个 `margin-left:auto` 把右边那Group推走），Now分成两Group带各自Title的字段，窄栏时各自成列。
- **Dictionary统计从散文行改成表**：原先yes「Messages — 共 501 行 · 已填: 501 · 读取自: — · 问题: none」这样的文字行，Nowyes `Dictionary / 行数 / 已填 / Document / 问题` 五列表，按content宽度排布，不再拉成满屏empty列。
- **长Sheets自带滚动**：Model表与Store表包进滚动容器（Store表另有 320px 高度上限），几十行DataStore清单不再把整页撑长。
- **Dictionary列头Added三条Dictionary键**（`dictColSet` / `dictColRows` / `dictColFilled` / `dictColFiles` 与「What is this?」`opsMoreInfo`），中英俄三份Dictionary同步补齐。

### 视觉：运维页按首页骨架重排

- **运维页以前yes「一列长表单」，Now和首页一个骨架**：页面头（Display + 副Title + 细分隔线）→ 吸顶块导航 → 两列Grid。左列（宽）放需要宽度的「Memory palace: language and bases」和「Backfill」，右列（窄、吸顶）放「Plugin language」和「Jev」——与首页「Today速览 → 活动热力 + Room directory」同一种版式。
- **Tools名从卡片外面搬进卡片里**（首页「Last 26 weeks activity」的写法）：每件Tools一张卡，Title在卡内，右侧放一句说明或动作（`Panel test` 按钮也回到它所属的卡里）。
- **「Memory Palace」卡不再在Statusin order时变成一张empty卡**：Model表和memory base表原先只In “出问题时」才出现（All in order时整张卡只剩一行Status）。Now两张表**常驻**——这张卡yesStatus卡，不yes故障卡；只有「动作」区（锁定 + Run）仍在需要时才出现。
- **Backfill的估算改用首页的大数字**（10 / 24 / 45 三个 32px 指标），不再yes小方块；动作区与Progress区之间加一道细分隔线。
- **Dictionary统计从Sheets改成行**（点 + Name + Document + 已填/总数 + 问题），和首页「Room directory」同一行式language。
- **注册表拆成两张卡**：`RegistryCard`（一个Component塞了两块）拆成 `PalaceLanguageCard` 与 `PluginLanguageCard`，快照由新的 `OpsTab` 统一持有并下传——表读一writes，不再各读各的；两张卡也终于能放进宽窄不同的两列。
- **表头不再折行、Sheets不再被挤成竖排**：表头 `white-space: nowrap`，Sheets有最小宽度，窄处改为横向滚动而不yes把「Model标识」拆成一列一个字。

### 修复：宫殿languageIn macOS 上被误判成英文

- **Panel、设置、电脑language全yesChinese，宫殿却报 English**：`systemLocaleBase()` читался через `Intl.DateTimeFormat().resolvedOptions().locale` — то есть локаль **процесса**. Приложение, запущенное из Dock, наследует `LANG=C.UTF-8` (это «язык не задан»), а Node/ICU отдают для такой локали `en-US` — плагин садился в английский на любой неанглийской macOS. Теперь: переменные окружения читаются как есть, но `C`/`POSIX` — это **«язык не задан», а не «английский»**; если в них настоящего языка нет, на macOS читается `AppleLocale` (настоящий язык ОС), на прочих платформах — ничего (`tests/system-locale.spec.ts`).
- **Следствие для данных**: вектора в базах оказались посчитаны китайским `bge-small-zh` (512), а целью была английская привязка → «нужна конвертация» и пересчёт ~284 записей. Теперь цель — `zh-bge-small-zh`, базы опознаны как `норма` (совпадают по модели), и остаётся только дешёвое действие «дописать метку» вместо пересчёта всех векторов.
- **Служебные тексты** поедут за языком дворца (`setMessageLanguage`): у китайского пользователя они теперь на китайском, а не на английском.

### 修复：往事页「Nearby」按钮一按就崩视图

- **形状不匹配（真凶）**：服务端 `GET /api/engram/episode-timeline?around=<id>` 回的yes `{ around: { anchor, neighbors } }`（同一条路由在分Group模式下回 `{ groups }`），**客户端却把整个响应体当成 `{ anchor, neighbors }` 读**——`around.anchor` yes `undefined`，渲染锚点行时读 `.id` 就把整个往事视图带崩：`TypeError: Cannot read properties of undefined (reading 'id')`。客户端Now取出内层 `around` 再渲染。
- **实证**：把客户端临时改回旧行为复现——锚点行直接消失（没有我的兜底就yes崩溃）；改回修复后锚点行in order渲染。
- **形状改由两端共用**（`src/types.ts` 的 `EpisodeTimelineAroundResponse` / `EpisodeTimelineGroupsResponse`）：这类"服务端与客户端各写一份形状"的漂移从此yes**编译错误**，不会等到用户点按钮才炸。服务端响应也用同一Type标注，锚点缺失时回 404 而不yes硬解References。
- **兜底**：即使响应形状再变，锚点缺失时只Display一行提示（`episodeAroundMissing`），不再带崩整个视图。

### 修复：视图崩溃的反馈信息补上"哪儿崩了"

- **崩溃卡只有一句 "Cannot read properties of undefined (reading 'id')"**：错误Messages说「哪里错了」，Component栈才说「哪个视图错了」。视图级错误边界Now捕获 React 的Component栈，打印到控制台并Display在兜底卡里（最More 12 行）——反馈时带上它，一writes就能定位到具体Component，不必再复现一遍。
- **响应字段一律兜底**（Repository契约「Added响应字段仍必须可选 + 兜底」）：Entities列表的 `item.entity`、运维快照的 `bases` / `models` / `languages`、Dictionary统计的 `files.read` / `rows` All改为缺字段不崩（缺 `entity` 的行照常Display为"—"，不渲染成 `undefined`）。

### 修复：运维页「Memory Palace」块的俄文串与术语堆

- **Chinese界面里蹦出俄文**：服务器在普查memory base时把版本不符写成了硬编码的俄文短句（`база v11, объявление под v13`），Panel原样Display——用户在ChinesePanel里看到两行红字里夹着俄文。Now服务器只给**nonelanguage的事实**（`v11→v13`、表名 `observations`），句子由Panel按自己的language写：`缺LessDataTable observations；DataStore版本 v11，当前插件需要 v13`。
- **「标Memory / Model标识 / 维度 / 池化·权重」「none向量 / 长度不符 / 向量长度」这些列头看不懂**：改成 `alias / Model / vector dims / 池化·量化` 与 `memory base / memories / no vector / wrong dims / vector dims`；Store的逐条Status也从「未检查 / no label / 向量混杂」改成「could not check / 没RecordsModel / mixed vector dims」。
- **「知识宫殿的language」与「Memory Palacelanguage」两个名字指同一个东西**（界面前后mixed）：统一为「宫殿language」，块Title改为「Memory palace: language and bases」。
- **三行只给名词不给解释**：宫殿language / memory base里的向量 / RetrievalMemory用的Model / your和代理说话的language——Now每行都带一句白话说明；红色Status旁补一句「到底差在哪、按下Run会发生什么」（`baseStateConvertHint` / `baseStateRepairHint`）；两张表各加一句表头说明（`registryModelsHint` / `registryBasesHint`）。

### 修复：ClearDaily志后容量反而变大

- **「ClearDaily志」In WAL 模式下不缩Document、反而把Panel上的容量数字推高**：PanelDisplay的容量yes `db + -wal + -shm` 三个Document的大小之和（`routes.ts` 的 `dbFileBytes`），而Clear动作yes `DELETE` + `VACUUM`。**WAL 模式下 `VACUUM` 把重建后的整Store写进 `-wal`，主StoreDocument一个字节都不截断**——于yes每writesClear，总字节数不降反升。实测真实Store（user.db）：Clear后 6.82 MB → **7.17 MB**。
- **`clearLog` Now补做 TRUNCATE-checkpoint**：`VACUUM` 之后Run `PRAGMA wal_checkpoint(TRUNCATE)`，把释放出的页写回主StoreDocument并把 `-wal` 归零。同一个真实Store：6.82 MB → **0.38 MB**（原来只有等宿主Close最后一个连接、SQLite 收尾截断时才会回收——而宿主一直On着这些Store，所以用户永远看不到回收）。
- **只在真的删到行时才 `VACUUM`**：原来即使一条都删不掉也照跑，纯粹yes在把 `-wal` 越撑越大。响应Added `checkpointed` 字段（逐Store与汇总各一份），Panel据此区分「压过了」与「压不动（Store被别的进程占着）」。
- **Panel容量数字不再失真**：容量口径仍yes三个Document的真实字节数，但Now它会随Clear**变小**；`tests/log-clear.spec.ts` 补了回归用例（Clear后 `db+wal+shm` 不得变大）。
- **Daily志页四个计数瓦片在Clear后会Automatic重取**：`Writes / Excavated / Retrieval / Consolidated` 取自Today遥测（182 天Window的 `op_log` 聚合），此前ClearDaily志**不触发**Today重载，于yes瓦片一直DisplayClear前的旧值（看起来像"Daily志没被清掉"）。NowClearDone后会重取Today，计数与真实剩余行数一致。
- **说明**：`ingest-*` / `reflect-pending` 这类键仍在ClearScope之外（它们yesDuplicate摄取的幂等Status，不yesDaily志）；若某Store只剩这些键，Clear会Display「已Deleted 0 条」——这yes正确行为，不yes故障。

### 修复：备份改走一致性快照，tar 解包拒绝路径穿越

- **备份不再直接拷贝活的 .db Document**：先在临时目录里做**一致性快照**（SQLite 快照 API），再打包。WAL 模式下直接拷贝会把还没落盘的 WAL 混进包里，Restore出来的Store可能自相Conflicts。
- **解包端加了三道闸**：条目路径穿越（`../`、绝对路径）直接拒绝；条目名超长拒绝；单条不yes可Restore的 Engram SQLite Store就整体拒绝（不再"Restore一半"留在Store里）。
- 三个新错误文案进服务端Dictionary（`store.backupNoDatabases` / `store.backupInvalidDatabase` / `store.tarUnsafeEntry`），中英俄三份齐。

### 修复：向量重算的完整性闸与远程嵌入器的GoalModel

- **Model标Memory只在完整且none错的重算之后才写进Store**（`vectorConversionComplete`）。此前只要"转换了 >0 条"就写标Memory，部分Failed也照样把Store标成新Model——往后Retrieval出来的yes半新半旧的向量empty间。
- **远程嵌入器的GoalModel改由"真正写向量的那一个"决定**（`resolveBaseTarget`）：不再从本地绑定借维度与标Memory，远程的维度与标Memory本来就none从得知。
- **重算的written to路径带上期望维度**：一批里出现一条别的维度的向量就整批停下（`rewriteVectors` 本就绕过了普通 guard，More一层检查才拦得住）。
- **猜测Model时，长度不在目录里就Back"不知道"**（undefined），而不yesfallback到GoalModel——把"不知道"当成"就yes它"会盖掉真实的wrong dims。

## [0.10.0] - 2026-10-24

> **0.10 yes新的一批功能**：0.9.x 整批yes「稳定性与可审计」，0.10.x 整批yes「Morelanguage与Data驱动」。
> 本版首writes把「默认值」从代码Property改为DataProperty——Model、language绑定、服务端Dictionary都不再写死在代码里。

### Morelanguage层、Data注册表与Store维护（merge #36）

- **默认嵌入Model改为注册表Data，不再yes代码常量**：Model目录 `registry/models.json`（`bge-small-zh` 512 维 / `minilm-l12` 384 维）、language绑定 `registry/bindings.json`（zh → bge-small-zh，`*` → minilm-l12）、languageTable `registry/languages.json` 与结构说明 `registry/schema.json` All作为DataDocument落盘；`src/embedder/local.ts` 不再硬编码Model标识，`src/embedder/select.ts` 按language解析Model，`embedder.localModel` 保留为显式覆盖。
- **服务端Dictionary外置**：`i18n/ru.txt`（498 条）与 `i18n/panel.ru.txt`（48 条）以 TAB 分隔的左原文/右译文格式落盘，服务端Messages与Panel文案从Document读取，不再内嵌在代码里。
- **Added `engram_language_guide` Tools**（Tools总数 22 → **23**）：Output `<包根>/docs/agent-language-guide.ru.md`，兑现首启Window对未Built-inlanguage用户的「agent 会Output一份说明」承诺。
- **PanelAdded「维护」视图**：StoreStatus、ModelTags、向量重算（`runVectorConvert`）、宫殿language切换、Dictionaryview与维护。
- **PanelDictionary接口**：`/api/engram/locales` 提供「非Built-inlanguage + PanelDictionary」，客户端 `src/client/index.ts` 拉取后 `addLanguage` + `register(NS, code, entries)`；路由不可用时fallback到Built-inlanguage。
- **language切换的联锁移到服务端**（`src/registry/palace-guard.ts`）：Window的StatusNow活在服务端——它自己Memory住第一个事件（由 `palace-change/open` OpenWindow）与那一刻Store所在的language，并按自己的Status计算Window。客户端不再发送 `from` 与 `startedAt`：此前校验的yes"客户端送来的值"而非实际Status，任何客户端都能靠伪造当前时间让条件AutomaticHeld。DuplicateOn窗不会延长Window；另一条written to路径（`palace-language`）同样受联锁约束（一个联锁挂在两条路径上，等于没有联锁）。此条Close `docs/tech-debt.md` 的 3.1。
- **其余已知边界**Memory于 `docs/tech-debt.md`（三条，均不阻塞使用）：远程嵌入器不参与GoalModel选择、Unknown维度的Store可能被盖上GoalTags、向量转换只校验条数不校验每条长度。
- **打包修复**：`i18n/`、`registry/`、`docs/agent-language-guide.ru.md` 三条路径补进 `package.json` 的 `files`；`scripts/check-hook-order.mjs` Added静态钩子顺序检查（React 只在Runtime报 #310，构建与 `tsc` 不会）。
- **Docs**：Added `docs/author/`（中英俄三语的Author向DesignDocs与插图，留在Repository不进包）、`docs/tech-debt.md`；Tools数 22 → 23 同步到 README（中英）与落地页。

### ⚠️ 升级说明：存量Store需要重建向量

**升级后默认嵌入Model从 512 维的 `bge-small-zh` 变为 384 维的 `minilm-l12`（Morelanguage）。** 宫殿language为Chinese的Store不受影响（`registry/bindings.json` 里 zh 仍绑定 bge-small-zh 512 维）；其余language的Store——包括language未设、fallback默认（`ru`）的Store——会落到 384 维的MorelanguageModel。

**两套向量不可比，维度也mixed。** 仍在Legacy上的Storewritten to时会被拒绝（`store.embedderDimensionMismatch`：比较与written to都拒绝，不做静默截断）——**如果不重建，升级看起来就像「坏了」**。处理办法二选一：

- 用新Model重建：`pnpm backfill --force`（重建All向量；不带 `--force` 时脚本在written to前就发现wrong dims并报错退出，绝不会写出混合维度的Store）；
- 或把Model改回去：在配置里写 `embedder.localModel: 'Xenova/bge-small-zh-v1.5'`（此时与旧Store同维，可Continue用）。

想按language分别用不同Model，改 `registry/bindings.json` 即可，none需动代码。

## [0.9.9] - 2026-10-23

### Docs与市场曝光

- README 首屏重写（中英双语同步）：Added一句话英文定位句「Local-first long-term memory for your coding agent.」、可折叠目录、「为什么yes它」三条主流路线对照表、Memory流动 ASCII 流程图、**给 agent 自己装的Install prompt**；Added CI 徽章。
- Added `docs/store/icon-180.png`（商店卡片位图Icon，含可复现的 `icon-180.html` 源）与 `docs/store/social-preview.png`（GitHub 社交Preview图，含 `social-preview.html` 源）。
- Added本 CHANGELOG。
- 修正Docs中的Tools数量：实际注册 **22** `engram_*` Tools（原Docs写作 21，英文版另一处写作 20）。
- `package.json` 补 `homepage` / `bugs` / 更全的 `keywords`。


### 商店与社区

- 商店条目DescriptionUpdate到 v0.9.8 现状并补 `icon` 字段（category 保持 `agi` 不变）。
- RepositoryDescription、主页与 topics 补齐；Enable GitHub Pages 落地页 `https://kenz1117.github.io/dsh-engram/`。

## [0.9.8] - 2026-10-22

- `pnpm backfill` Added `--force`（重建All向量）与 `--model`（指定Model），扫描 `<dbDir>` 下All分Store（`user.db` / `shared.db` / `project-<hash>.db`）；维度与Store中已有向量不符时在written to前报错退出，绝不写出混合维度的Store。
- 嵌入器远程端点的离线测试补齐；维度不匹配demote为显式报错而非静默Failed。
- 补上 `vite`（vitest 5 把 vite 降成了 peer 依赖）。

## [0.9.7] - 2026-10-22

- 路由层错误Category收敛：超限 413、坏 JSON 400、不yes对象 400，三类Failed各说各的话；DeletedDuplicate的 review-answer places理器。
- i18n：Import failed的措辞去掉「校验Failed」叠词；大 body 的报错不再套「Document校验Failed」。

## [0.9.6] - 2026-10-21

- 路由级断言补齐三类Failed场景；修正「内存封顶」的措辞；修正 `net.connect` 重载（补 `host` Parameters）。

## [0.9.5] - 2026-10-21

- **Import不再被 64 KiB body 上限静默丢弃**（#34）：Import接口单独放On到 16 MiB；上限改按**字节**计（原按字符数算，Chinese一字 3 bytes, 等于把字节防线悄悄放宽了三倍）；请求体改整块 UTF-8 解码（原逐块解码，More字节字符跨块边界会被切成乱码）；三类Failed各给各的提示（413 / 400 非法 JSON / 400 不yes对象）；Panel加前置体量检查。
- 补「请求中途断掉的Import不会带崩服务」and 10回归用例。

## [0.9.4] - 2026-10-21

- **嵌入vector dims成为硬约束**：Store中向量与当前Model维度mixed时，written to与相似度比较都直接拒绝并报 `EMBEDDER_DIMENSION_MISMATCH`（说清两边各yesMoreLess维），而不yes逐维截断后给出一个像样的相似度。
- 嵌入器可配置：本地Model与池化可选，支持 OpenAI 兼容远程端点（`apiKeyEnv` 从环境变量读，插件不落密钥）。
- Jev Jev改为**Runtime解析**：provider 与Model取自 DSH，插件不Save密钥与地址；旧格式字段一writes性告警。

## [0.9.3] - 2026-10-20

- **Added `engram_import` MemoryImport**（#33）：JSON 回路 + Preview勾选 + content去重 + 就地补嵌入；Import独立成前置Icon按钮，格式支持 JSON 与Export的 Markdown。
- PanelExport菜单项补 button 样式Reset（此前Mirror tree与Import菜单项渲染成大盒子）。

## [0.9.2] - 2026-10-19

- 镜像Export改 fetch + toast，修复点击崩溃；Import改同款下拉（显式 JSON / Markdown 两项）。
- Session压缩后重新注入常驻Image（#32）。
- hero 图References指向 `hero.jpg`（此前References了已Deleted的 png）；Remove被Replace的 `hero.png`，Repository瘦身 2.1 MB。

## [0.9.1] - 2026-10-18

- Refurb suggestions满宽铺On + 弹窗不透明兜底（#19 问题一/二）。
- `stats` 分部补上 `shared`。

## [0.9.0] - 2026-10-18

**稳定性与可审计批writes（据 [审计报告](../docs/audit-2026-10-18.md)）**

- 视图级 ErrorBoundary（按视图demote + Try again按钮）——把整类白屏从「不可见事故」变成「可见Warning」。
- Failed不再伪装成empty态；修Search框 trim 过早。
- Version handshake + `VERSION` 单一Source化；客户端与服务端版本偏斜时Top出现重启提醒横幅。
- 摄取重放Recurring逐键隔离 + 陈旧放弃 + 已付费的 LLM 产物不静默丢弃。
- Data安全三连：restore 后Close并失效分StoreCache、SQLite On WAL / busy_timeout、迁移前Automatic快照。
- i18n：翻新理由FollowPanellanguage、demote墓志铭走Dictionary、俄语复数。

## [0.8.16] - 2026-10-18

- dependabot 接入（npm Weekly / actions Monthly）+ `packageManager` 钉 `pnpm@10.15.0`；Added CONTRIBUTING 与 SECURITY。

## [0.8.15] - 2026-10-18

- #19 修复：Refurb suggestions卡片内联原文与merge候选；侧栏Icon回归宿主单色。
- 打包修复：host peers All optional，tarball 能装进干净 profile（0.8.0–0.8.14 十五个版本带病发布的事故收口）。
- CI 门禁：在托管 runner 上真实Run两道不依赖 harness 树的自检。

## [0.8.14] - 2026-10-17

## [0.8.13] - 2026-10-17

- 吸顶 Header/Tab 栏；修复 tab 栏 1px 滚动条；调整 scope 顺序。

## [0.8.12] - 2026-10-15

- 历史Backfill「卡住」修复：逐轮Progress + 轮询Failed显形 + 重启哨兵。

## [0.8.0] - 2026-10-13

- **Panel入口升级为顶层侧栏**（`sidebar.panellist` + `main` 双槽位），主列满宽渲染，不再受设置弹窗宽度限制。
- Added第九视图「Beliefs」与 UI 布局重构。
- Graph模式补完；侧栏Icon改品牌渐变 tile；活动热力Daily历；走廊真·建筑Floorplan。

## [0.7.16] - 2026-10-13

## [0.7.15] - 2026-10-03

- Panel第九视图「Beliefs」落地；UI 砍 Header 品牌区、加暖色渐变水印与线性Icon。

## [0.7.14] - 2026-10-03

- **AutomaticBeliefs巩固**（`reflect: suggest|auto` + observations 表，schema v12）：把「尚未成为任何Beliefs证据」的原始Memory交给辅助 LLM 巩固为带证据数的一句话Beliefs；同主题经向量近邻Automatic归并并持续细化；新证据与既有Beliefs近邻时Beliefs先转 `stale`，下writes巩固四选一（form / refine / still / refute）。原始MemoryAll保留，Beliefsyes叠加的抽象层。

## [0.7.13] - 2026-09-25

- 采用 v4 producer-owned plugin message sources。

## [0.7.12] - 2026-09-22

- **Jev System一Jev**：DEFER band的三路判定（merge / accept / defer）与Conflicts边Confirm可交外部 Jev Model判决；Jev 不可用时静默fallback纯规则四态，不阻断written to。每writesJev旁路留观测（判定结果、yesno参与、耗时、fallback原因），Panel「Jev」视图可排查。

## [0.7.11] - 2026-09-21

- **EntitiesDictionary与时序事实链**（阶段三）：事实级三元Group携带 `valid_at` / `invalid_at` Time window，Conflict事实软失效不Deleted；`engram_facts` 读写事实链；`engram_search` 加 `asOf` 时点回看；PanelEntities页事实链Details。

## [0.7.10] - 2026-09-21

- EntitiesDictionary：摄取与Save的辅助 LLM Output同时抽取Entities提及（ experts物 / Projects / Tools / Concepts，可带alias），按归一化名与既有Entities精确匹配消解；Memory与EntitiesMore对Morelinks。

## [0.7.9] - 2026-09-21

- **阶段二Memory分层首批**：ImageEditor（`engram_profile_edit`，版本链rollback + Panel diff 审计）、episode 情景Memory独立Timeline（`engram_episode_timeline`，按Session分Group、Time proximity expansion）、Backfill与实时Session摘要Group头。

## [0.7.8] - 2026-09-21

- **阶段一飞轮闭环补强四项All落地**：written to四态回报（ACCEPT / MERGE / DROP / DEFER）、分级递减注入Budget、PII 脱敏扩充（Chinese密码 / 身份证 / 手机号）、证据门收尾提醒。

## [0.7.7] - 2026-09-21

- Today待回忆demote为速览计数；陈展加「due today」筛选。
- 遗留Store迁移Governance：written to JSON 墓碑 sidecar Records归属，`legacyMigration` 支持 `eager` / `conservative` 两种策略。

## [0.7.6] - 2026-09-17

- 摄取逐条判宫殿（跨Projects通用的进私 expertsStore，只跟当前Projects有Off的进ProjectsStore）；Project palace随 GUI Workspace切换。
- 适配新版 dsh 的 `sessionPersistence` Read-only句柄。
- none git 时的分Store命名从「cwd 前 12 字符」改为 cwd 全量 sha256（修掉同前缀目录撞Store）。

## [0.7.5] - 2026-09-12

- CJK token 估算；**证据门 `engram_assess`**（命中 ≠ 足以回答，代码强制三条件齐备）；管家Daily志补Dictionary。

## [0.7.4] - 2026-09-11

- Panel细节打磨：Today视图重排、Tools栏 chips、Daily志Tools条与类别色点、Backfill分段线。

## [0.7.3] - 2026-09-11

- **历史SessionBackfill**：把 dsh 已持久化的历史Session逐轮提炼进宫殿（默认按每个Session自己的 cwd 分Store），幂等键支持中断续跑；Import规则可配（Time window / 轮数上限 / 辅助Model / yesno含子代理）。Panel五视图重构。

## [0.7.2] - 2026-09-11

- **Memory Palace信息架构落地**：Location当索引（written to按 kind 分房并钉「Room#桩位」坐标，Room容量 9，满员room opened）、Pin路线定顺序（`tour_routes` append-only）、骨架长期复用、标Memory独一none二（门牌 0-1 评分）；修复迁移与 dispose 崩溃。

## [0.7.1] - 2026-09-11

- UI 打磨与修复；上架 dsh-market「AGI 架构探索」类目。

## [0.6.1] - 2026-09-01

- 统计卡片补脱敏指标；`engram_export` Redacted view。

## [0.6.0] - 2026-09-01

- **注入防护三件套**（protocolTags + 入Store脱敏 + Recall占位防回声室）；More查询 RRF 融合；批量Save；脱敏筛选视图。

## [0.5.0] - 2026-09-04

- 竞品对标四项；管理 UI 全面翻新。

## [0.4.7] - 2026-09-02

- 发布时通过 `prepare` 链接 peer 包。

## [0.4.4] - 2026-09-01

- zh/en i18n 走宿主 locale 服务，Panel文案All本地化。

## [0.4.0] - 2026-09-01

- Settings page「memory base」tab 取代独立页面。

## [0.3.0] - 2026-09-01

- Web 管理Panel（`/engram`）+ `/api/engram/*` 回环 API；嵌入Backfill脚本（为demote期written to的Memory补算向量）。

## [0.2.0] - 2026-09-01

- Automatic摄取、contradiction candidates、蒸馏飞轮、审计Tools；schema v2 MemorySourceSession/轮writes/seq、review/stats/export/decay。

## [0.0.1] - 2026-09-01

- 首个可用版本：`engram_*` Tools集、插件入口、Image注入、SQLite + FTS5 + Off系边 + op log 的本地Storage。

[Unreleased]: https://github.com/kenz1117/dsh-engram/compare/v0.12.0...HEAD
[0.12.0]: https://github.com/kenz1117/dsh-engram/compare/v0.11.6...v0.12.0
[0.11.6]: https://github.com/kenz1117/dsh-engram/compare/v0.11.5...v0.11.6
[0.11.5]: https://github.com/kenz1117/dsh-engram/compare/v0.11.4...v0.11.5
[0.11.4]: https://github.com/kenz1117/dsh-engram/compare/v0.11.3...v0.11.4
[0.11.3]: https://github.com/kenz1117/dsh-engram/compare/v0.11.2...v0.11.3
[0.11.2]: https://github.com/kenz1117/dsh-engram/compare/v0.11.1...v0.11.2
[0.11.1]: https://github.com/kenz1117/dsh-engram/compare/v0.11.0...v0.11.1
[0.11.0]: https://github.com/kenz1117/dsh-engram/compare/v0.10.0...v0.11.0
[0.10.0]: https://github.com/kenz1117/dsh-engram/compare/v0.9.9...v0.10.0
[0.9.9]: https://github.com/kenz1117/dsh-engram/compare/v0.9.8...v0.9.9
[0.9.8]: https://github.com/kenz1117/dsh-engram/compare/v0.9.7...v0.9.8
[0.9.7]: https://github.com/kenz1117/dsh-engram/compare/v0.9.6...v0.9.7
[0.9.6]: https://github.com/kenz1117/dsh-engram/compare/v0.9.5...v0.9.6
[0.9.5]: https://github.com/kenz1117/dsh-engram/compare/v0.9.4...v0.9.5
[0.9.4]: https://github.com/kenz1117/dsh-engram/compare/v0.9.3...v0.9.4
[0.9.3]: https://github.com/kenz1117/dsh-engram/compare/v0.9.2...v0.9.3
[0.9.2]: https://github.com/kenz1117/dsh-engram/compare/v0.9.1...v0.9.2
[0.9.1]: https://github.com/kenz1117/dsh-engram/compare/v0.9.0...v0.9.1
[0.9.0]: https://github.com/kenz1117/dsh-engram/compare/v0.8.16...v0.9.0
[0.8.16]: https://github.com/kenz1117/dsh-engram/compare/v0.8.15...v0.8.16
[0.8.15]: https://github.com/kenz1117/dsh-engram/compare/v0.8.14...v0.8.15
[0.8.14]: https://github.com/kenz1117/dsh-engram/compare/v0.8.13...v0.8.14
[0.8.13]: https://github.com/kenz1117/dsh-engram/compare/v0.8.12...v0.8.13
[0.8.12]: https://github.com/kenz1117/dsh-engram/compare/v0.7.16...v0.8.12
[0.8.0]: https://github.com/kenz1117/dsh-engram/compare/v0.7.16...v0.8.0
[0.7.16]: https://github.com/kenz1117/dsh-engram/compare/v0.7.15...v0.7.16
[0.7.15]: https://github.com/kenz1117/dsh-engram/compare/v0.7.14...v0.7.15
[0.7.14]: https://github.com/kenz1117/dsh-engram/compare/v0.7.13...v0.7.14
[0.7.13]: https://github.com/kenz1117/dsh-engram/compare/v0.7.12...v0.7.13
[0.7.12]: https://github.com/kenz1117/dsh-engram/compare/v0.7.11...v0.7.12
[0.7.11]: https://github.com/kenz1117/dsh-engram/compare/v0.7.10...v0.7.11
[0.7.10]: https://github.com/kenz1117/dsh-engram/compare/v0.7.9...v0.7.10
[0.7.9]: https://github.com/kenz1117/dsh-engram/compare/v0.7.8...v0.7.9
[0.7.8]: https://github.com/kenz1117/dsh-engram/compare/v0.7.7...v0.7.8
[0.7.7]: https://github.com/kenz1117/dsh-engram/compare/v0.7.6...v0.7.7
[0.7.6]: https://github.com/kenz1117/dsh-engram/compare/v0.7.5...v0.7.6
[0.7.5]: https://github.com/kenz1117/dsh-engram/compare/v0.7.4...v0.7.5
[0.7.4]: https://github.com/kenz1117/dsh-engram/compare/v0.7.3...v0.7.4
[0.7.3]: https://github.com/kenz1117/dsh-engram/compare/v0.7.2...v0.7.3
[0.7.2]: https://github.com/kenz1117/dsh-engram/compare/v0.7.1...v0.7.2
[0.7.1]: https://github.com/kenz1117/dsh-engram/compare/v0.6.1...v0.7.1
[0.6.1]: https://github.com/kenz1117/dsh-engram/compare/v0.6.0...v0.6.1
[0.6.0]: https://github.com/kenz1117/dsh-engram/compare/v0.5.0...v0.6.0
[0.5.0]: https://github.com/kenz1117/dsh-engram/compare/v0.4.7...v0.5.0
[0.4.7]: https://github.com/kenz1117/dsh-engram/compare/v0.4.6...v0.4.7
[0.4.4]: https://github.com/kenz1117/dsh-engram/compare/v0.4.3...v0.4.4
[0.4.0]: https://github.com/kenz1117/dsh-engram/compare/v0.3.0...v0.4.0
[0.3.0]: https://github.com/kenz1117/dsh-engram/compare/v0.2.0...v0.3.0
[0.2.0]: https://github.com/kenz1117/dsh-engram/compare/v0.0.1...v0.2.0
[0.0.1]: https://github.com/kenz1117/dsh-engram/releases/tag/v0.0.1
