# 两个真实 LLM-Wiki 的工程化观察（脱敏）
**中文** | [English](./2026-04-17-two-wikis-engineering-notes.md)

更新时间：2026-04-17

## 1. 样本范围

两个长期运行中的 LLM-Wiki（脱敏为 A/B）：

1. Wiki A：生活/关系/长期事项
2. Wiki B：AI/技术学习、研究与工作流

## 2. 关键数据（可复核）

### 2.1 全仓规模

1. Wiki A：`1748` 个 Markdown 文件，其中 `165` 个超过 10000 字符（`9.4%`）
2. Wiki B：`1345` 个 Markdown 文件，其中 `126` 个超过 10000 字符（`9.4%`）

### 2.2 wiki 区类型分布

1. A（`wiki/`）：entities `491`、concepts `309`、sources `74`、synthesis `6`
2. B（`wiki/`）：entities `109`、concepts `143`、sources `106`、synthesis `5`

### 2.3 raw 与 wiki 的结构比例（近似）

1. A：raw `73` vs wiki `886`（约 `1:12.1`）
2. B：raw `116` vs wiki `365`（约 `1:3.1`）

### 2.4 结构健康信号

1. `wiki/` 下 frontmatter 覆盖：A `886/886`，B `365/365`
2. `wiki/` 下包含至少一个双链的页面：
   - A：`857/886`（约 `96.7%`）
   - B：`365/365`（`100%`）
3. A 中无双链页面约 `29` 个，主要集中在 concepts/entities 的短页

### 2.5 entity/concept 每日新增趋势（创建时间口径，近似）

说明：

1. 口径为文件创建时间（birth time），用于估算“每日新增”。
2. 统计范围：仅 `wiki/entities/*.md` 与 `wiki/concepts/*.md`。

| 日期 | A_entity | A_concept | B_entity | B_concept | 当日新增总量 | 累计总量 |
|---|---:|---:|---:|---:|---:|---:|
| 2026-04-11 | 0 | 0 | 0 | 1 | 1 | 1 |
| 2026-04-13 | 0 | 0 | 25 | 2 | 27 | 28 |
| 2026-04-14 | 433 | 213 | 20 | 41 | 707 | 735 |
| 2026-04-15 | 51 | 41 | 3 | 1 | 96 | 831 |
| 2026-04-16 | 7 | 55 | 61 | 95 | 218 | 1049 |
| 2026-04-17 | 0 | 0 | 0 | 3 | 3 | 1052 |

趋势解读（面向工程）：

1. 峰值日（`2026-04-14`）新增 `707` 页，说明“批量 ingest”会瞬间放大读写与校验负载。
2. 后续即使新增回落，累计总量仍持续上升（到 `1052`），查询和修复成本不会自然下降。
3. 这直接解释了“非常烧 token”：不是只烧在新建页，还烧在关联既有页与回查冲突。

### 2.6 两个 Wiki 项目的 token 使用快照（本地可追溯，合并口径）

说明：

1. 项目映射口径按当前约定执行：
   - `karpathy-llm-wiki` 并入 Wiki A（领域已脱敏）。
   - `robust-llm-wiki` 并入 Wiki B（领域已脱敏）。
2. 这是“可追溯的保守值”，不是完整账单值。
3. 统计时间：2026-04-17。

| 项目（脱敏） | Codex 本地累计 | Claude 本地累计 | 合计 |
|---|---:|---:|---:|
| Wiki A（生活/关系/长期事项） | 119,130,370 | 283,792,320 | 402,922,690 |
| Wiki B（AI/技术学习/研究/工作流） | 52,420,609 | 17,644,374 | 70,064,983 |
| 合计 | 171,550,979 | 301,436,694 | 472,987,673 |

为什么说“实际更烧 token（且此值偏低）”：

1. 部分与两仓维护强相关的会话可能发生在其他工作目录，未计入这张“两项目严格口径”表。
2. 不同工具链的记录字段并不完全一致，部分历史会话可能缺少可直接累加的 usage 字段。
3. 维护中的 `grep/read`、多轮回查、子代理并发会显著增加上下文消耗，且不一定都能在同一口径下完整回放。

## 3. 从真实仓库提炼出的工程结论

1. 长文件是常态，不是例外：`~9.4%` 文件超过 10000 字符，直接放大上下文截断风险。
2. `log.md` 与 `index.md` 是高风险热点：两仓库最长文件都集中在日志和索引页。
3. “文件处理过”不等于“内容读完整”：并发 + 读取上限容易造成后段遗漏。
4. 单页质量问题会沿双链扩散：新增 entity/concept 关联时，旧误差会被 read/grep 带入新页。
5. 工程噪声会渗入知识区：样本中出现了疑似变量未展开目录（例如 `$(...)` 片段）。

## 4. 对 Robust LLM-Wiki 的直接启发

1. File2Agent 倡议是必要的：长文件优先单文件深读分工。
2. Hook 需要全流程化：读前、写前、写后、批次后、回填后都应有轻量守门。
3. schema 要先于实现：先把规范边界讲清楚，再决定流程化落地方式。
4. 规范表达要通俗：先解释“为什么要做”，再给“如何参考”。

## 5. 已对应到本仓库的规范映射

1. 弱幻觉治理：[`schema/details/04-hallucination-control.zh-CN.md`](../schema/details/04-hallucination-control.zh-CN.md)
2. File2Agent 与上下文工程：[`schema/details/05-file2agent-context-engineering.zh-CN.md`](../schema/details/05-file2agent-context-engineering.zh-CN.md)
3. Hook 全流程倡议：[`schema/details/06-claude-hook-guidelines.zh-CN.md`](../schema/details/06-claude-hook-guidelines.zh-CN.md)
4. Turbo 模型维护分工：[`schema/details/07-turbo-model-value.zh-CN.md`](../schema/details/07-turbo-model-value.zh-CN.md)
