# 幻觉治理（详细）
**中文** | [English](./04-hallucination-control.md)

## 0. 背景（为什么要做这件事）

1. 一开始我们使用了幻觉更高的模型做编译和维护，结果在 Wiki 里出现了事实偏差。
2. 后续只能切换到幻觉更低的模型，配合多轮 lint、grep、read 去做修复。
3. 一次修复里改了 7 个 entity，但仍然需要多轮 grep/read 才能稳定。
4. 这说明 LLM-Wiki 必须坚持“弱幻觉优先”。
5. 原因很直接：如果旧内容里有幻觉，新一轮 ingest/query 可能继续引用它，错误会被继承并扩散。

所以，这个文档不是为了讨论“哪个模型更强”，而是为了回答：  
在持续维护 Wiki 时，怎样把幻觉风险压到更低。

## 1. 实战样例（脱敏）

| Wiki 页面 | 状态 | 关键发现（脱敏） |
|---|---|---|
| 实体 A | 已修正 | 媒体来源名称纠正；补充方法模型说明 |
| 实体 B | 已修正 | 关键日期冲突已修正 |
| 实体 C | 已补充 | 别名与身份信息已补充 |
| 实体 D | 已修正 | 同名混淆已拆分；错误跳转链接已修正 |
| 实体 E | 已补充 | 竞赛结果与作品清单已补充 |
| 实体 F | 已修正 | 参与人数表述已纠正；子页面信息已同步 |

## 2. 同口径模型对比（主口径：Vectara）

主榜单页面更新时间：2026-03-20（截至 2026-04-17 暂无更近公开更新）。

| 厂商/模型条目 | Hallucination Rate | 说明 |
|---|---:|---|
| deepseek-ai/DeepSeek-V3.2-Exp | 5.3% | 当前同口径较低 |
| openai/gpt-5.4-nano-2026-03-17 | 3.1% | 当前同口径更低条目之一 |
| openai/gpt-5.4-mini-2026-03-17 | 5.5% | 当前同口径较低 |
| MiniMaxAI/minimax-m2p5 | 9.1% | 同榜单优于 `minimax-m2p1(11.8%)` |
| zai-org/GLM-4.7-flash | 9.3% | 较 `GLM-4.5-AIR-FP8` 更新，数值持平 |
| zai-org/glm-5 | 10.1% | 更近版本，但该口径下更高 |
| anthropic/claude-haiku-4-5-20251001 | 9.8% | 当前可见 Haiku 条目 |
| anthropic/claude-sonnet-4-6 | 10.6% | 较 `claude-sonnet-4(10.3%)` 更新，但更高 |
| moonshotai/Kimi-K2.5 | 14.2% | 当前可见 Kimi 主条目 |
| moonshotai/Kimi-K2-Instruct-0905 | 17.9% | 同族对照条目 |

补充：
1. Codex 在部分评测页有排名条目，但静态页没有统一可引用的幻觉率数字。
2. 新版本不一定更低幻觉率，选型要看同口径实测值。

## 3. 怎么理解这些分数

1. 只有同一榜单内的结果可以直接横向比较。
2. 不同榜单（SimpleQA、AA-Omniscience、Vectara）定义不同，不能直接比绝对值。
3. 这里主要是摘要任务口径，不等于你在生产里的全部任务。
4. 看 hallucination rate 时，要一起看 answer rate，避免单指标误判。

## 4. 一个简单判断：初次编译不干净，后面就会很难降

先用一个简单公式看趋势：

\[
\Delta H \approx p \times (n_{entity}+n_{concept}) + c \times n_{read/grep}
\]

其中：

1. \(p\)：当前模型幻觉率
2. \(n_{entity}\)：本轮新生成的 entity 数量
3. \(n_{concept}\)：本轮新生成的 concept 数量
4. \(n_{read/grep}\)：本轮为了建关联而进行的 read/grep 次数
5. \(c\)：每次 read/grep 把旧误差带入新页面的平均影响系数
6. \(\Delta H\)：这一轮新增的幻觉量

这表示：  
一次编译如果产出很多 entity/concept，且模型幻觉率偏高，新增幻觉会明显变多。  
后续新增关联时，Agent 会反复 read/grep 旧页面，如果旧页面里已有幻觉，这些错误会继续被带到新页面。  
后面即使开始修复（例如这次已经修了 7 个 entity），也常常需要多轮 grep/read/lint 才能把整体幻觉率往下压。

## 5. 执行建议（弱幻觉优先）

1. Ingest 编译优先选同口径下低幻觉模型。
2. Query 的关键结论做二次核验，重点查来源和链接。
3. 高风险页面改动后，增加一轮 read/grep 校验再入稳定区。
4. 每轮修复后都跑 lint，并保留快照做对比。

## 6. 数据来源（可复核）

1. 主榜单（GitHub）：https://github.com/vectara/hallucination-leaderboard
2. 榜单原文（README）：https://raw.githubusercontent.com/vectara/hallucination-leaderboard/main/README.md
3. 方法说明（README#Methodology）：https://github.com/vectara/hallucination-leaderboard#methodology
4. Vectara 方法博文：https://www.vectara.com/blog/introducing-the-next-generation-of-vectaras-hallucination-leaderboard