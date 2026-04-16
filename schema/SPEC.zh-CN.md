# Robust LLM-Wiki 总纲（SPEC）
**中文** | [English](./SPEC.md)

## 0. 这份总纲怎么用

1. 这份文档只写共识和边界，不写项目脚本实现。
2. 细节写在 `schema/details/`，证据写在 `research/`。
3. 当前阶段聚焦规范边界与执行口径，具体实现保持开放。

## 1. 为什么会有这个项目

1. 我们已经长期维护两个真实运行的 LLM-Wiki（脱敏为 A/B）。
2. 在真实维护中，我们看到同一个问题会反复出现：结构不统一、链接断裂、内容幻觉。
3. 其中最关键的是幻觉问题：一次修了 7 个 entity，仍要多轮 grep/read 才能稳定。
4. 这说明 LLM-Wiki 必须坚持弱幻觉优先。
5. 如果旧内容有幻觉，新一轮 ingest/query 可能继续引用它，错误会被继承并扩散。

## 2. 两个实践样本的精准统计（脱敏 A/B）

统计时间：2026-04-17

### 2.1 全仓 Markdown 统计

| 样本 | 定位（脱敏） | Markdown 文件数 | Markdown 行数 | `[[...]]` 双链数 | Markdown 字节数 |
|---|---|---:|---:|---:|---:|
| Wiki A | 生活/关系/长期事项 | 1,748 | 238,293 | 13,090 | 14,470,735 |
| Wiki B | AI/技术学习/研究/工作流 | 1,345 | 541,083 | 4,690 | 33,034,280 |
| 合计 | - | 3,093 | 779,376 | 17,780 | 47,505,015 |

### 2.2 `wiki/` 目录页面结构统计

| 样本 | wiki 页面总数 | entities | concepts | sources | synthesis | topics |
|---|---:|---:|---:|---:|---:|---:|
| Wiki A | 886 | 491 | 309 | 74 | 6 | 0 |
| Wiki B | 365 | 109 | 143 | 106 | 5 | 0 |
| 合计 | 1,251 | 600 | 452 | 180 | 11 | 0 |

统计口径：`find` 统计 `*.md` 文件与字节；`cat|wc -l` 统计 Markdown 行数；`rg -o '\[\[[^]]+\]\]'` 统计双链。

### 2.3 来自真实样本的工程约束（已纳入 schema）

1. 长文件是常态，不是例外：A/B 两个样本中，超过 10000 字符的 Markdown 都约为 `9.4%`。
2. `log.md` 与 `index.md` 是高风险热点：两仓库最长文件都集中在日志与索引页。
3. “文件处理过”不等于“内容读完整”：并发 + 读取上限容易造成后段遗漏。
4. 单页质量问题会沿双链扩散：新增 entity/concept 关联时，旧误差会被 `read/grep` 带入新页。
5. 工程噪声会渗入知识区：疑似变量未展开目录（如 `$(...)`）需要纳入 lint 观察项。

### 2.4 entity/concept 日增趋势（为什么后期更难维护）

1. 两个真实样本在同一统计窗口内，出现过单日新增 `707` 页（entity+concept）。
2. 即使后续单日新增回落，累计规模仍持续增长（样本窗口末尾累计 `1052` 页）。
3. 这说明维护成本不仅来自“新建”，还来自“查询既有 + 补链回查 + 冲突修复”。
4. 简化表达：`维护成本 ≈ 新增成本 + 存量成本 + grep/read 回查成本`。
5. 详细趋势表见 [`research/2026-04-17-two-wikis-engineering-notes.zh-CN.md`](../research/2026-04-17-two-wikis-engineering-notes.zh-CN.md)。

## 3. Karpathy 内核红线（不可破）

1. 一定要是 Wiki（知识沉淀为可维护页面，不只停留在聊天记录）。
2. 一定要有双链（可跳转、可追踪、可复用）。
3. 一定要有维护闭环（`ingest -> query -> lint`）。
4. 后续扩展不能破坏以上三条。

## 4. 规范分工（先细化，再实现）

1. [`details/01-attribute-proposal.zh-CN.md`](./details/01-attribute-proposal.zh-CN.md)：先定义最小属性契约，减少写入混乱。
2. [`details/02-ingest-field-rules.zh-CN.md`](./details/02-ingest-field-rules.zh-CN.md)：新增字段前先定义入库规则，避免后期返工（含 `draft/stable` 写入闸门倡议）。
3. [`details/03-lint-playbook.zh-CN.md`](./details/03-lint-playbook.zh-CN.md)：按顺序做 lint，减少重复检查（含路径与 shell 安全检查）。
4. [`details/04-hallucination-control.zh-CN.md`](./details/04-hallucination-control.zh-CN.md)：基于实战场景做弱幻觉治理。
5. [`details/05-file2agent-context-engineering.zh-CN.md`](./details/05-file2agent-context-engineering.zh-CN.md)：长文件 ingest 的分工思路与上下文工程倡议。
6. [`details/06-claude-hook-guidelines.zh-CN.md`](./details/06-claude-hook-guidelines.zh-CN.md)：Claude Hook 的全流程轻量守门倡议（含写后快速 lint）。
7. [`details/07-turbo-model-value.zh-CN.md`](./details/07-turbo-model-value.zh-CN.md)：Turbo/低参数模型在双链与格式维护中的任务分工倡议。
8. 具体实现按项目实际选择，不在本仓库固定。

## 5. 扩展边界（可以改，但要守边界）

1. 可以在 `entity`、`concept` 外扩展 `topic` 等分类。
2. 可以按项目需要扩展字段（如 `status`、`topic`），但要先定义 ingest 规则。
3. 模板可用于约束属性，不建议强行约束正文大纲。

## 6. 弱幻觉优先（总纲结论）

1. 模型选型优先看同口径幻觉率，不看“新旧”印象。
2. 同口径下更新版本不一定更低幻觉率。
3. 修复后的页面要继续做 read/grep/lint，直到冲突和断链下降到可控。
4. 具体数据、公式和执行建议见 [`details/04-hallucination-control.zh-CN.md`](./details/04-hallucination-control.zh-CN.md)。
5. 维护阶段可优先使用 Turbo/低参数模型处理双链和格式一致性（见 [`details/07-turbo-model-value.zh-CN.md`](./details/07-turbo-model-value.zh-CN.md)）。

## 7. 文件分层

1. `schema/SPEC.md`：英文主总纲。
2. `schema/SPEC.zh-CN.md`：中文镜像（本文件）。
3. `schema/details/*.md`：英文详细规则和执行说明。
4. `schema/details/*.zh-CN.md`：中文镜像详细规则。
5. `research/RESEARCH.md`：英文研究主文档。
6. `research/RESEARCH.zh-CN.md`：中文镜像研究文档。
