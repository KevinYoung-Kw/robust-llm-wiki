# Schema 文档：从这里开始

**中文** | [English](./README.md)

> [!IMPORTANT]
> 推荐先读 [SPEC.zh-CN.md](./SPEC.zh-CN.md)，再读 [robust-llm-wiki-CLAUDE.zh-CN.md](./robust-llm-wiki-CLAUDE.zh-CN.md)。
> `SPEC` 负责解释这个框架“是什么、边界在哪里”；`robust-llm-wiki-CLAUDE.zh-CN.md` 负责解释 agent 在这些边界内“应该如何运行”。

> [!TIP]
> 当前 released schema baseline: `v0.1.3`

`schema/` 这组文档要回答的是一个很实际的问题：怎样把一个长期运行的 LLM-Wiki 设计成可维护、可追溯、可治理，而不是越积越乱。

不要一次把所有文档都读完。更好的方式是分层阅读：先理解架构，再理解操作规则，最后按任务去读具体规则页。

## 推荐阅读顺序

1. [SPEC.zh-CN.md](./SPEC.zh-CN.md)：理解系统模型、核心不变量和扩展边界。
2. [robust-llm-wiki-CLAUDE.zh-CN.md](./robust-llm-wiki-CLAUDE.zh-CN.md)：理解 agent 的操作规范、ownership、promotion gate 和风险控制。
3. [OPEN_SOURCE_POLICY.zh-CN.md](./OPEN_SOURCE_POLICY.zh-CN.md)：理解许可证、provenance 和 disclosure 要求。
4. [`details/`](./details/)：按字段、ingest、lint、幻觉控制、hooks、长文件协作、模型分工等主题深入。
5. 英文主文档：当你需要英文原文或最新英文表达时，对照 `*.md` 文件阅读。

## 阅读路径

### 最短总览路径

如果你只想先建立正确心智模型：

1. [SPEC.zh-CN.md](./SPEC.zh-CN.md)
2. [robust-llm-wiki-CLAUDE.zh-CN.md](./robust-llm-wiki-CLAUDE.zh-CN.md)

### 我需要安全地运行 Agents

1. [robust-llm-wiki-CLAUDE.zh-CN.md](./robust-llm-wiki-CLAUDE.zh-CN.md)
2. [details/03-lint-playbook.zh-CN.md](./details/03-lint-playbook.zh-CN.md)
3. [details/06-claude-hook-guidelines.zh-CN.md](./details/06-claude-hook-guidelines.zh-CN.md)
4. [details/08-harmless-engineering.zh-CN.md](./details/08-harmless-engineering.zh-CN.md)

### 我需要定义或扩展字段

1. [SPEC.zh-CN.md](./SPEC.zh-CN.md)
2. [details/01-attribute-proposal.zh-CN.md](./details/01-attribute-proposal.zh-CN.md)
3. [details/02-ingest-field-rules.zh-CN.md](./details/02-ingest-field-rules.zh-CN.md)

### 我需要搞清楚 Draft-To-Stable 与证据门槛

1. [robust-llm-wiki-CLAUDE.zh-CN.md](./robust-llm-wiki-CLAUDE.zh-CN.md)
2. [details/02-ingest-field-rules.zh-CN.md](./details/02-ingest-field-rules.zh-CN.md)
3. [details/03-lint-playbook.zh-CN.md](./details/03-lint-playbook.zh-CN.md)

### 我需要长文件或 Agent Team 协作指南

1. [robust-llm-wiki-CLAUDE.zh-CN.md](./robust-llm-wiki-CLAUDE.zh-CN.md)
2. [details/05-file2agent-context-engineering.zh-CN.md](./details/05-file2agent-context-engineering.zh-CN.md)
3. [details/08-harmless-engineering.zh-CN.md](./details/08-harmless-engineering.zh-CN.md)

### 我需要幻觉控制与模型分工建议

1. [details/04-hallucination-control.zh-CN.md](./details/04-hallucination-control.zh-CN.md)
2. [details/07-turbo-model-value.zh-CN.md](./details/07-turbo-model-value.zh-CN.md)
3. [details/08-harmless-engineering.zh-CN.md](./details/08-harmless-engineering.zh-CN.md)

### 我需要法律或开源治理规则

1. [OPEN_SOURCE_POLICY.zh-CN.md](./OPEN_SOURCE_POLICY.zh-CN.md)

## 顶层文档各自负责什么

| 文档 | 作用 | 什么时候读 |
|---|---|---|
| [SPEC.zh-CN.md](./SPEC.zh-CN.md) | 这个框架的架构参考：不变量、边界、生命周期和扩展限制。 | 你要先判断“这个系统是什么”，而不是马上决定“怎么执行”。 |
| [robust-llm-wiki-CLAUDE.zh-CN.md](./robust-llm-wiki-CLAUDE.zh-CN.md) | Agent 运行规范：stage ownership、handoff、promotion gate、Agent Team、lint cadence 与 escalation。 | 你即将设计或运行 agent workflow。 |
| [OPEN_SOURCE_POLICY.zh-CN.md](./OPEN_SOURCE_POLICY.zh-CN.md) | 许可证、来源证明、合规与私密安全披露规则。 | 你在贡献、发布、引入第三方材料，或做合规审查。 |
| [`details/`](./details/) | 单主题的规则页与执行说明。 | 你已经理解顶层模型，现在只需要某一个具体主题。 |

## `details/` 速览

| 文件 | 主题 |
|---|---|
| [details/01-attribute-proposal.zh-CN.md](./details/01-attribute-proposal.zh-CN.md) | 最小页面字段与可扩展元数据方向 |
| [details/02-ingest-field-rules.zh-CN.md](./details/02-ingest-field-rules.zh-CN.md) | ingest 合同、归一化、默认值、更新策略、冲突处理 |
| [details/03-lint-playbook.zh-CN.md](./details/03-lint-playbook.zh-CN.md) | lint 顺序与维护检查节奏 |
| [details/04-hallucination-control.zh-CN.md](./details/04-hallucination-control.zh-CN.md) | 低幻觉运行原则与修复思路 |
| [details/05-file2agent-context-engineering.zh-CN.md](./details/05-file2agent-context-engineering.zh-CN.md) | 长文件阅读、context 限制与 `1 subagent -> 1 file` 风格协作 |
| [details/06-claude-hook-guidelines.zh-CN.md](./details/06-claude-hook-guidelines.zh-CN.md) | hook 触发点建议与轻量级 guardrail |
| [details/07-turbo-model-value.zh-CN.md](./details/07-turbo-model-value.zh-CN.md) | 更快或更便宜模型适合做什么、不适合做什么 |
| [details/08-harmless-engineering.zh-CN.md](./details/08-harmless-engineering.zh-CN.md) | 长程 agent 调度里的低破坏性、可观测性与模型/运行栈选择 |

## 一个简单原则

不确定从哪篇开始时，就按这个顺序读：

1. 先看 `SPEC`
2. 再看 `robust-llm-wiki-CLAUDE.zh-CN`
3. 再看 `details/`
4. 最后再回到 `research/` 里的证据与案例
