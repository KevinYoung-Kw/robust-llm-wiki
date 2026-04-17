# Robust LLM-Wiki Research
**中文** | [English](./RESEARCH.md)

## 研究目标

本研究用于支撑一个“更优实践”方向：在不偏离 Karpathy LLM-Wiki 内核的前提下，让大规模 LLM-Wiki 在长期维护中更稳健。

## 基线来源

1. Karpathy 原始 gist: https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f
2. GitHub / Gist 生态快照（见 `research/snapshots/`）
3. 两个长期运行的真实 LLM-Wiki 实践样本（已脱敏为 A/B）

## 实践样本规模（A/B 脱敏）

截至 2026-04-17：

1. Wiki A（生活/关系/长期事项）
- 1,748 个 Markdown 文件
- 238,293 行 Markdown 文本
- 13,090 个双链
- 14,470,735 字节 Markdown 文本

2. Wiki B（AI/技术学习/研究/工作流）
- 1,345 个 Markdown 文件
- 541,083 行 Markdown 文本
- 4,690 个双链
- 33,034,280 字节 Markdown 文本

3. 合计
- 3,093 个 Markdown 文件
- 779,376 行 Markdown 文本
- 17,780 个双链
- 47,505,015 字节 Markdown 文本

统计口径：`find` 统计 `*.md` 文件数与字节数；`find -print0 | xargs -0 cat | wc -l` 统计行数；`rg -uu -o '\[\[[^]]+\]\]'` 统计双链数量。

## 关键问题（来自实战）

1. 人读与 AI 读目标混在同一层，导致页面要么难读，要么难处理。
2. 过度依赖正文模板（outline），页面风格僵化且逐渐脱离 Wiki 形态。
3. 来源常被追加在尾部，正文双链不足，页面变成“可读但不可跳转”的孤岛。
4. lint 依赖大量 read/grep 手工检查，消耗上下文与时间。

## 核心研究结论

1. 维持 Karpathy 内核是前提：Wiki 形态、双链网络、ingest/query/lint 闭环。
2. Template 最佳用途是“约束属性”，而不是“规定正文 outline”。
3. 页面应做“人读层 + AI读层”分层写作，而不是二选一。
4. lint 应分层执行：Property lint -> Link lint -> Content lint（脚本实现由下游项目自行完成）。

## 模型幻觉率（同口径横比）

主口径：Vectara Hallucination Leaderboard（最后更新 2026-03-20；截至 2026-04-17 暂无更近更新）。

本仓库不在研究页内复述具体厂商、具体模型、具体分数，也不对模型能力做公开评价。

如需查看同口径的公开数据，请直接参考原始链接：

1. Vectara Hallucination Leaderboard: https://github.com/vectara/hallucination-leaderboard
2. AA-Omniscience 实时页: https://artificialanalysis.ai/evaluations/omniscience
3. SimpleQA: https://arxiv.org/abs/2411.04368
4. AA-Omniscience: https://arxiv.org/abs/2511.13029

补充说明：
1. 不同榜单定义不同，不能直接做绝对值横比。
2. 同口径下的更新版本也不一定更低幻觉。
3. 如需比较，请以原始发布页面和原始方法说明为准。

## 口径限制（必须同时阅读）

1. 不同榜单指标定义不同（SimpleQA、AA-Omniscience、Vectara 不能直接绝对值横比）。
2. 同一榜单内可做相对选型。
3. 任务形态差异（摘要/问答/是否可用工具）会显著影响幻觉指标。
4. 评分器差异与模型热更新会引入漂移。

## 幻觉传播简化模型（含多次 read 工具调用）

\[
H_{t+1}=\alpha H_t + \lambda k_t h_m - \beta u_t
\]

1. \(H_t\)：第 t 轮幻觉量（污染实体规模）
2. \(k_t\)：该轮 Agent 的 read/grep 工具调用次数
3. \(h_m\)：该路径所用模型幻觉率
4. \(\lambda\)：每次 read 转写为新声明的系数
5. \(u_t\)：该轮修正实体数
6. \(\alpha\)、\(\beta\)：传播与修正有效率

若
\[
\alpha H_t + \lambda k_t h_m \ge \beta u_t
\]
则净幻觉不会下降。

## 实战修正案例（用户提供）

本轮集中修正的实体页（摘要）：

1. 实体-A：媒体来源纠正，补充模型信息。
2. 实体-B：关键日期冲突修正。
3. 实体-C：别名与身份信息补充。
4. 实体-D：实体拆分与错误链接修正。
5. 实体-E：竞赛与作品信息补充。
6. 实体-F：参与者数量事实修正与子页同步。

结论：即便集中修正多个实体，也常需要多轮 read/grep 与 lint 才能稳定下来。

## 选型建议（面向 LLM-Wiki）

1. ingest 编译路径优先低幻觉模型。
2. query 高风险结论做二次核验。
3. 维持分层 lint + 回滚策略。
4. 可牺牲表达丰富性，不可容忍高幻觉污染。

## 可追溯资料

1. Karpathy gist: https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f
2. Vectara 榜单: https://github.com/vectara/hallucination-leaderboard
3. AA-Omniscience 实时页: https://artificialanalysis.ai/evaluations/omniscience
4. SimpleQA: https://arxiv.org/abs/2411.04368
5. AA-Omniscience: https://arxiv.org/abs/2511.13029
6. 生态快照与原始数据：`research/snapshots/`
7. 两个真实仓库工程观察：[`research/2026-04-17-two-wikis-engineering-notes.zh-CN.md`](./2026-04-17-two-wikis-engineering-notes.zh-CN.md)

说明：
1. 本仓库在引用这些资料时，只提供研究入口与方法来源。
2. 相关页面不代表本仓库对任何模型厂商、模型家族或产品能力作公开判断。
