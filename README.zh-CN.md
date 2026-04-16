# Robust LLM-Wiki

**中文** | [English](./README.md)

Robust LLM-Wiki 是一个开源框架实践项目。

它不是新的内容型 Wiki 仓库，而是把一线维护经验沉淀成可复用的方法：

1. `Research`（证据与问题）
2. `Schema`（红线与执行规则）

## 项目缘起

我们之所以提出这个项目，是因为已经长期维护了两个真实运行的 LLM-Wiki，并在持续使用中发现了共性问题。

这两个 Wiki（已脱敏为 A/B）分别聚焦：

1. 生活/关系信息与长期事项
2. AI/技术学习、研究与工作流沉淀

截至 2026-04-17，合计规模达到：

1. `3,093` 个 Markdown 文件
2. `779,376` 行 Markdown 文本
3. `17,780` 个双链（`[[...]]`）

在这个规模下，真正困难的不是“能不能写内容”，而是“能不能长期稳定维护”。

## 我们观察到的问题

1. 人读和 AI 读目标混在一起，页面要么难读，要么难处理。
2. 过度依赖 outline 模板，导致页面风格僵化并偏离 Wiki 形态。
3. 来源常被追加在尾部，正文双链不足，页面逐渐成为孤岛。
4. 规范与检查没有分层，维护成本随着规模快速上升。

## Robust LLM-Wiki 的核心概念

Robust 的意思不是“更多功能”，而是“更稳的维护机制”。

1. 保持 Karpathy 内核不变：Wiki 形态、双链网络、ingest/query/lint 闭环。
2. 用 Schema 承载底线与红线，明确可扩展边界。
3. 当前阶段将具体工作流实现与本仓库解耦。

## 仓库结构

```text
.
├── .github/
├── CONTRIBUTING.md
├── CONTRIBUTING.zh-CN.md
├── I18N.md
├── I18N.zh-CN.md
├── README.md
├── README.zh-CN.md
├── research/
└── schema/
```

### 1) Research

定位：沉淀证据、样本规模、生态观察、问题归纳。

- 主文档（EN）：[research/RESEARCH.md](./research/RESEARCH.md)
- 主文档（ZH）：[research/RESEARCH.zh-CN.md](./research/RESEARCH.zh-CN.md)
- 调研快照：`research/snapshots/`
- 工程观察（A/B 脱敏）：[research/2026-04-17-two-wikis-engineering-notes.zh-CN.md](./research/2026-04-17-two-wikis-engineering-notes.zh-CN.md)
- 生态调研：[research/2026-04-17-llm-wiki-ecosystem-survey.zh-CN.md](./research/2026-04-17-llm-wiki-ecosystem-survey.zh-CN.md)

### 2) Schema

定位：承载红线、规则、扩展边界、开源治理要求。

- 规范主文档（EN）：[schema/SPEC.md](./schema/SPEC.md)
- 规范主文档（ZH）：[schema/SPEC.zh-CN.md](./schema/SPEC.zh-CN.md)
- 详细规则目录（EN）：[schema/details/](./schema/details)
- 开源协议与治理（EN）：[schema/OPEN_SOURCE_POLICY.md](./schema/OPEN_SOURCE_POLICY.md)
- 开源协议与治理（ZH）：[schema/OPEN_SOURCE_POLICY.zh-CN.md](./schema/OPEN_SOURCE_POLICY.zh-CN.md)

## Karpathy 内核（不偏离）

1. 一定是 Wiki（知识沉淀为可维护页面，而不是聊天记录）
2. 一定有双链（可导航、可发现、可复用）
3. 一定有 ingest/query/lint 闭环
4. 一定可追溯、可审计、可回滚

## 开源框架协议（建议）

1. 代码与脚本：Apache-2.0
2. 研究与规范文档：CC BY 4.0
3. 贡献流程：DCO 签署 + 第三方许可证声明
4. 当前仓库许可证文件：Apache-2.0（`LICENSE`）

详细规则见（EN）：[schema/OPEN_SOURCE_POLICY.md](./schema/OPEN_SOURCE_POLICY.md)

## 开源协作文件

1. 贡献指南（EN）：[CONTRIBUTING.md](./CONTRIBUTING.md)
2. 贡献指南（ZH）：[CONTRIBUTING.zh-CN.md](./CONTRIBUTING.zh-CN.md)
3. 多语言规则（EN）：[I18N.md](./I18N.md)
4. 多语言规则（ZH）：[I18N.zh-CN.md](./I18N.zh-CN.md)

## 基线来源

- Karpathy 原始 gist: https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f
