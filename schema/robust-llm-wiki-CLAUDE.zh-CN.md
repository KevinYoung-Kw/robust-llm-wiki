# robust-llm-wiki-CLAUDE

**中文** | [English](./robust-llm-wiki-CLAUDE.md)

> 状态：`v0.1.1` 当前生效的 operator policy
> 范围：这份文档是本仓库内 agent 的运行手册。
> 边界：`SPEC.md` 负责解释系统是什么；这份文档负责解释怎样安全地运行它。

## 1. 这份文件负责什么

可以把 `SPEC.md` 理解成架构边界，把这份文档理解成 operator handbook。

`SPEC.md` 规定了哪些事实必须始终成立：它仍然是一个 wiki，仍然依赖双链网络，仍然运行 `ingest -> query -> lint` 这个闭环。

这份文档则再往下一层，专门回答这些运行中的问题：

1. agent 什么时候可以写
2. 什么内容必须留在 `draft`
3. Agent Team 应该怎样分工
4. 在把内容提升到 `stable` 之前要检查什么
5. 要留下哪些记录，才能让下一个 operator 信任这次改动

## 2. 拿不准时，按这个顺序做判断

只要优先级顺序清楚，大多数日常决策都会简单很多。

1. 先保护事实正确性和来源可追溯性
2. 再保护安全、法律和合规要求
3. 再保护结构完整性：字段、链接、命名和页面状态
4. 再保护长期可维护性与未来返工成本
5. 只有前四项都安全时，才去打磨文风和表述

## 3. 运行姿态

在展开具体 stage 之前，最好先把一个简单心智模型放在脑子里。

1. 原样保留 Karpathy 内核：wiki 形态、双链网络、`ingest -> query -> lint` 闭环。
2. 把原始来源当作只读真值层。agent 可以读、可以引用、可以综合，但不要重写。
3. 把 wiki 当作被维护的知识层。这里允许受控的 AI 更新。
4. 把 schema 当作治理层。`SPEC.md` 定义架构边界，这份文档定义 operator 行为，`schema/details/` 存放单主题规则和 playbook，`research/` 存放证据和案例。
5. 让 human / AI 分工始终清晰：human 决定目标、来源范围和例外处理；agent 负责维护、综合和结构化检查。
6. 遇到不确定时，宁可显式保留不确定，也不要靠自信措辞把问题抹平。
7. 让 provenance 和 compliance 可见。不要接收来源不明或许可不清的第三方材料；需要 contributor rights attestation 与第三方 source/license metadata；需要维护 dependency 的 license/version inventory；安全问题优先走私密披露通道。

## 4. 有意识地运行核心闭环

只有把 `ingest`、`query` 和 `lint` 当作三件不同的工作，而不是一团混合动作时，仓库才容易长期健康。

### 4.1 Ingest

Ingest 负责把源材料转成 draft 知识。

1. Ingest 负责 source assimilation 和 draft-page updates。
2. Ingest 中写下的每一条事实，都应该还能追到证据。
3. 没有 `source_ids` 的新事实，只能停留在 `draft`，不能直接进入 `stable`。
4. 碰到冲突时，要把它显式留下来，写成 conflict、open question 或 blocked promotion；不要靠更肯定的语气把冲突伪装成结论。

### 4.2 Query

Query 把 wiki 当作工作记忆层使用，但它不能绕过证据规则。

1. Query 可以产出 answer artifact 或 synthesis note。
2. 如果要直接改 canonical wiki content，仍然必须走 ingest 级别的 evidence 和 promotion gate。
3. Query 得出的高影响结论，应该先视为 provisional，直到它完成与其他 wiki 改动同等强度的验证路径。

### 4.3 Lint

Lint 是维护中的 guardrail。它保证系统形状不被悄悄带坏。

1. Lint 按 `property -> link -> content` 的顺序执行。
2. Lint 可以自动修结构性问题，比如 metadata 形状或明显的格式断裂。
3. Lint 不应静默替你做语义判断。任何会改变含义的改动，都应回到 content-owner approval 或新的 ingest-style review。

### 4.4 Stage Handoff

每一个 stage 都应给下一个 stage 留下足够清楚的交接信息。

1. 每个 stage 都输出 machine-parseable handoff：`run_id`、`files_changed`、`evidence_refs`、`open_questions`、`risk_level`、`next_owner`。
2. 下一个 stage 在开始前要先校验这些字段。
3. handoff 缺字段时，应视为 stop condition，而不是温和提醒。

## 5. 让 `draft` 和 `stable` 真正有区别

最容易把 wiki 带坏的方式之一，就是让未完成的工作看起来像已经坐实的知识。`draft -> stable` 这条边界，就是为了防止这种事发生。

1. 生产页面应至少满足最小契约：`id`、`title`、`type`、`source_ids`、`last_reviewed`、`status`。
2. `type` 和 `status` 必须来自批准过的枚举，未知值应在 ingest 或 lint 时失败。
3. `draft` 用来容纳新的、未完成的、或仍有争议的知识。
4. `stable` 只留给那些已经可追溯、已交叉检查、结构也干净的知识。
5. 提升到 `stable` 至少要满足：有 `source_ids`、至少完成一轮 cross-check、通过最低限度 lint。
6. 写这段内容的 agent 不能自己批准它从 `draft` 升到 `stable`。
7. `stable` promotion 需要独立 verification。对 `R2` 和 `R3` 工作，要使用独立 verifier agent、另一模型或 human reviewer。
8. 超过项目 staleness window 的页面，要根据 `last_reviewed` 被标记，并阻止自动 promotion。

## 6. 谨慎扩展 Schema

Schema 应该能长，但增长需要先有契约，再有字段。

1. 新增字段或页面类型前，先定义它的 ingest contract：source、normalization、default behavior、update strategy、conflict handling。
2. 如果这几项不完整，或写得过于模糊以至于无法 review，就不应合并 schema 扩展。
3. 新规则只要可能，就应做到 lint-checkable。
4. 规则变更和 `stable` 内容变更，都应记录 effective date 与 impact scope。

## 7. 作为 Agent Team 工作，而不是一群 Agent 挤在一起

Agent Team 真正有价值的前提，是它能减少歧义，而不是扩大歧义。

1. 对长文件或高影响文件，默认采用 `1 subagent -> 1 file` 的端到端 ownership。
2. 每个 subagent 在编辑前声明自己的 `owned_files`。
3. 在同一个 batch 里，每个目标 wiki 文件默认只有一个 writer；其他 agent 可以读、可以 review、可以准备上下文，但对该文件保持只读。
4. 如果编辑重叠不可避免，就必须串行化，并使用可解释的优先级规则；解决过程要写进 `log.md`。
5. Main agent 负责 orchestrate、aggregate 和跨文件问题的收口；subagent 负责深读和 file-scoped execution。
6. 如果文件很短，或者已有可证明 full-coverage 的流程，可以例外，但例外必须显式说明。

长文件需要单独强调，因为“处理过”和“读完整”不是一回事。

1. 默认把长文件视为常态，而不是特例。
2. 把 `index.md` 和 `log.md` 一类热点文件视为高频检查对象。
3. 先 locate，再 deep-read。先用 index、search 或 chunking 定位相关区域，再分配深度 ownership。
4. chunk 或 slice 文件只是中间产物，不能当作独立 `src` 去 ingest。
5. 所有切片工作都要合并回原始 source 视图，也就是 `1 src -> 1 raw`。

## 8. 按节奏验证，而不是凭感觉

可靠维护来自重复的检查节奏。重点不是“随时都 lint”，而是在坏状态最可能进入系统的节点上做检查。

1. 读之前：先做轻量提醒，确认文件大小、风险级别，以及是否需要拆分。
2. 写之前：先检查必需字段、来源链路和 wikilink 预期。
3. 每次 AI 写完后：跑一轮快速最小 lint。
4. 每次 ingest 或 repair batch 之后：先跑快速 `property + link` lint。
5. 从 `draft` 升到 `stable` 之前：跑 content lint。
6. 大批量 backfill 或 batch work 之后：再跑更广覆盖的 lint 和 consistency spot-check。
7. 对重大改动保留 pre/post lint snapshot，方便 diff 和 rollback。
8. 如果事实修复或链接修复暴露出更多漂移，就继续 `read/grep/lint`，直到冲突和断链降到可控范围。
9. 如果 path-risk 检查发现未展开变量、不安全 quoting 或可疑 path concatenation，就暂停 batch ingest/query，先修脚本安全。

## 9. 尽早分级风险，清晰升级

不是所有工作都值得走同一套流程。风险分级的作用，是让团队把额外谨慎用在真正重要的地方。

1. `R1`：常规维护、格式修复、直接的 metadata 修复、或低影响更新
2. `R2`：高风险页面更新、重要 synthesis 变化、或可能沿大量链接扩散的改动
3. `R3`：关键结论、事实裁决、或会实质改变后续理解的工作

风险等级应该直接改变流程强度。

1. `R2` 和 `R3` 需要额外的 `read/grep` 验证与更严格的 promotion 检查。
2. 当高影响结论缺乏可靠来源、冲突反复无法收敛、关键 schema/lint 检查持续失败、或 handoff 缺字段时，应升级给 human owner。
3. 升级期间，把相关文件冻结为只读，仅保留 assigned resolver 可写。
4. 在 `log.md` 中记录 owner、deadline 和 unblock criteria。
5. 只有 unblock checks 通过后，才恢复正常流程。

## 10. 按失败模式分配模型

模型选择最重要的，不是“谁更强”，而是它改变了系统最可能犯哪种错。

1. Ingest 和高影响 synthesis 优先走弱幻觉路径。
2. 格式清理、双链修复、结构一致性之类重复维护工作，可以交给更快或更便宜的模型。
3. 高不确定事实裁决和高质量新页面生成，不要走 Turbo-only 路径。
4. 比较模型时，要基于可比证据；用同 benchmark 或同任务范围来比较版本。
5. 看 hallucination rate 时，也要一起看 answer rate；只看一个指标会误导判断。
6. 不要假设新版本天然更安全。
7. 高风险输出在被当作可依赖知识前，应再经过一次更强模型或 human reviewer 的 regression pass。

## 11. 让仓库更可读的一些约定

这些不是系统的架构核心，但它们能让仓库更容易操作和审计。

1. 把 `index.md` 保留为内容导航入口，并在 ingest 或重大结构变化后更新它。
2. 让 `log.md` 保持 append-only 和 parseable。它是操作历史，不是草稿本。
3. 把 `[[wikilink]]` 保留为关键 entity 和 concept 的结构化交叉引用机制。
4. 临时工具产物不要混进知识区。
5. 需要脱敏的例子和案例，继续保持匿名化。

## 12. 留下别人能接手的审计轨迹

每一次有意义的改动，都应该让下一次 review 更容易，而不是更难。

1. 记录这次改动为什么要做。
2. 记录 `files_changed`；如果用了 Agent Team，还要记录 `owned_files`。
3. 如果工作尚未完成，记录 `evidence_refs`、未解决冲突、open questions 和 `next_owner`。
4. 记录风险等级，以及当前是否处于升级状态。
5. 记录下一步还应该检查什么。
6. 对 ingest 或 batch work 报告基础 coverage signals：读完的文件数、candidate outputs、未解决冲突数、broken-link delta。
7. 对规则变更和 `stable` 内容变更，附上 effective date 和 impact scope。

## 13. 哪些部分仍然保持灵活

这份 policy 对正确性和可追溯性要求很严格，但它仍然为本地工程决策留了空间。

1. 具体脚本、命令和 CI wiring 可以因项目而异。
2. lint threshold 和 alert level 可以随仓库规模演化。
3. 模型供应商和版本可以灵活选择，只要不违背上面的 operating policy。
4. 核心系统边界之外的 folder-level mechanics 可以调整，而不必重写整套 policy。
