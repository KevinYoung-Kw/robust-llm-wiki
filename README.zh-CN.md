# Robust LLM-Wiki

**中文** | [English](./README.md)

[![Latest Release](https://img.shields.io/github/v/release/KevinYoung-Kw/robust-llm-wiki?label=release)](https://github.com/KevinYoung-Kw/robust-llm-wiki/releases)
[![GitHub Stars](https://img.shields.io/github/stars/KevinYoung-Kw/robust-llm-wiki?style=flat)](https://github.com/KevinYoung-Kw/robust-llm-wiki/stargazers)
[![License](https://img.shields.io/github/license/KevinYoung-Kw/robust-llm-wiki)](./LICENSE)
[![Docs](https://img.shields.io/badge/docs-bilingual-2f6feb)](./schema/README.zh-CN.md)

![Robust LLM-Wiki Banner](./assets/github-banner-en.webp)

Robust LLM-Wiki 是一个面向 AI 长期维护场景的开源 Wiki 框架实践项目。

它不是新的内容型 Wiki 仓库，而是把真实维护经验沉淀为可复用的方法：

1. `Research`（证据与共性问题）
2. `Schema`（不可妥协红线与执行规则）

> [!IMPORTANT]
> **先读这里（Read This First）**
> 如果你想用最顺的路径进入 schema 文档，请先读：
> **[`schema/README.zh-CN.md`](./schema/README.zh-CN.md)**
>
> 它会先给你阅读顺序，再把你带到：
> - `SPEC`：理解架构与边界
> - `robust-llm-wiki-CLAUDE.zh-CN`：理解 operator policy 与 agent 协作规则
> - `details/`：按主题深入具体规则页

## 项目速览

| 项目 | 数值 |
|---|---|
| 统计日期 | `2026-04-17` |
| 实践样本 | 两个脱敏的真实生产 Wiki（A/B） |
| Markdown 文件数 | `3,093` |
| Markdown 行数 | `779,376` |
| 双链数（`[[...]]`） | `17,780` |

在这个规模下，真正困难的不是“写出内容”，而是“长期稳定维护”。

## 项目缘起

我们在长期维护两个真实 LLM-Wiki 时，反复遇到同一类问题：

1. 人读和 AI 读目标混在一起，页面要么难读、要么难处理。
2. 过度依赖固定 outline，逐步偏离 Wiki 形态。
3. 来源常被放到末尾，正文双链不足，页面容易孤岛化。
4. 规则与检查没有分层，规模上来后维护成本快速上升。

## 核心思路

Robust 不是“功能更多”，而是“维护机制更稳”。

1. 保持 Karpathy 内核不变。
2. 用 `Schema` 定义底线、红线和可扩展边界。
3. 把具体实现与本框架仓库解耦，便于下游按需落地。

## 仓库结构

![Robust LLM-Wiki Outline](./assets/outline.webp)

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

定位：沉淀证据、样本规模、生态观察和问题归纳。

- 主文档（EN）：[research/RESEARCH.md](./research/RESEARCH.md)
- 主文档（ZH）：[research/RESEARCH.zh-CN.md](./research/RESEARCH.zh-CN.md)
- 调研快照：`research/snapshots/`
- 工程观察（A/B 脱敏）：[research/2026-04-17-two-wikis-engineering-notes.zh-CN.md](./research/2026-04-17-two-wikis-engineering-notes.zh-CN.md)
- 生态调研：[research/2026-04-17-llm-wiki-ecosystem-survey.zh-CN.md](./research/2026-04-17-llm-wiki-ecosystem-survey.zh-CN.md)

### 2) Schema

定位：承载红线、规则、扩展边界与治理要求。

- 规范主文档（EN）：[schema/SPEC.md](./schema/SPEC.md)
- 规范主文档（ZH）：[schema/SPEC.zh-CN.md](./schema/SPEC.zh-CN.md)
- Schema 入口页（EN）：[schema/README.md](./schema/README.md)
- Schema 入口页（ZH）：[schema/README.zh-CN.md](./schema/README.zh-CN.md)
- Claude 顶层操作文档（EN）：[schema/robust-llm-wiki-CLAUDE.md](./schema/robust-llm-wiki-CLAUDE.md)
- Claude 顶层操作文档（ZH）：[schema/robust-llm-wiki-CLAUDE.zh-CN.md](./schema/robust-llm-wiki-CLAUDE.zh-CN.md)
- 详细规则目录：[schema/details/](./schema/details)
- 开源治理（EN）：[schema/OPEN_SOURCE_POLICY.md](./schema/OPEN_SOURCE_POLICY.md)
- 开源治理（ZH）：[schema/OPEN_SOURCE_POLICY.zh-CN.md](./schema/OPEN_SOURCE_POLICY.zh-CN.md)

## 快速开始

1. 先读 [quickstart/README.zh-CN.md](./quickstart/README.zh-CN.md)。
2. 根据自己的情况选路径：把这套框架接入你现有的 LLM-Wiki，或者直接基于它生成新的 `CLAUDE.md` / `AGENTS.md` / `GEMINI.md`。
3. 然后再进入 [schema/README.zh-CN.md](./schema/README.zh-CN.md)、[schema/SPEC.zh-CN.md](./schema/SPEC.zh-CN.md) 和 [schema/robust-llm-wiki-CLAUDE.zh-CN.md](./schema/robust-llm-wiki-CLAUDE.zh-CN.md)。

## Karpathy 内核（不偏离）

1. 一定是 Wiki。
2. 一定有双链网络。
3. 一定有 `ingest -> query -> lint` 闭环。
4. 一定可追溯、可审计、可回滚。

## 开源框架协议（建议）

1. 代码与脚本：Apache-2.0
2. 研究与规范文档：CC BY 4.0
3. 贡献流程：DCO 签署 + 第三方许可证声明
4. 当前仓库许可证文件：Apache-2.0（`LICENSE`）

详细规则见：[schema/OPEN_SOURCE_POLICY.zh-CN.md](./schema/OPEN_SOURCE_POLICY.zh-CN.md)

## 协作文档

1. 贡献指南（EN）：[CONTRIBUTING.md](./CONTRIBUTING.md)
2. 贡献指南（ZH）：[CONTRIBUTING.zh-CN.md](./CONTRIBUTING.zh-CN.md)
3. 多语言规则（EN）：[I18N.md](./I18N.md)
4. 多语言规则（ZH）：[I18N.zh-CN.md](./I18N.zh-CN.md)

## 基线来源

- Karpathy 原始 gist：[https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f](https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f)
