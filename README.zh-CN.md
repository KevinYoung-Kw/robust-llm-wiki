# Robust LLM-Wiki

**中文** | [English](./README.md)

[![Latest Release](https://img.shields.io/github/v/release/KevinYoung-Kw/robust-llm-wiki?label=release)](https://github.com/KevinYoung-Kw/robust-llm-wiki/releases)
[![GitHub Stars](https://img.shields.io/github/stars/KevinYoung-Kw/robust-llm-wiki?style=flat)](https://github.com/KevinYoung-Kw/robust-llm-wiki/stargazers)
[![License](https://img.shields.io/github/license/KevinYoung-Kw/robust-llm-wiki)](./LICENSE)
[![Docs](https://img.shields.io/badge/docs-bilingual-2f6feb)](./schema/README.zh-CN.md)

![Robust LLM-Wiki Banner](./assets/github-banner-en.webp)

Robust LLM-Wiki 是一个面向长期 AI 维护场景的、以文档为中心的 Wiki control-plane 框架仓库。

这个仓库不公开私有 Wiki 内容。它公开的是：当一个 Wiki 变大以后，怎样保持 Wiki 形态、保持双链、控制 agent runtime 漂移，并让维护过程长期可审计。

这个项目来自两个长期运行的真实 LLM-Wiki。目标不是一次性生成内容，而是把维护本身做成可重复、可治理的系统。

> [!IMPORTANT]
> **先读这里（Read This First）**
> 如果你想用最顺的路径进入 schema 文档，请先读：
> **[`schema/README.zh-CN.md`](./schema/README.zh-CN.md)**
>
> 它会先给你阅读顺序，再把你带到：
> - `SPEC`：理解架构与边界
> - `robust-llm-wiki-CLAUDE.zh-CN`：理解 operator policy 与 agent 协作规则
> - `details/`：按主题深入具体规则页

## 这个仓库要解决什么问题

当一个 LLM-Wiki 规模上来之后，核心问题就不再是“模型能不能写一页”，而变成：

1. wiki 能不能继续保持可导航；
2. 新来源能不能在不污染旧页面的前提下被编译进去；
3. query 产物能不能可靠到足以参与后续维护；
4. agent runtime 在长工具链下会不会开始漂移。

这个仓库存在的意义，就是把这些问题整理成可复用规则，而不是一次性的 prompt 技巧。

## 项目速览

| 项目 | 数值 |
|---|---|
| 统计日期 | `2026-04-17` |
| 实践样本 | 两个脱敏的真实生产 Wiki（A/B） |
| Markdown 文件数 | `3,093` |
| Markdown 行数 | `779,376` |
| 双链数（`[[...]]`） | `17,780` |

在这个规模下，核心挑战是把维护质量长期稳定住，同时把一次性写入扩展为可持续维护流程。

## 项目缘起

项目缘起很直接：两个 LLM-Wiki 的长期运行反复暴露出同类型问题，所以我们把修复路径沉淀成可复用的方法。这里公开的是方法、规则和验证框架，不包含原始 Wiki 的私有内容：

1. 人读和 AI 读目标混在一起，页面要么难读、要么难处理。
2. 过度依赖固定 outline，逐步偏离 Wiki 形态。
3. 来源常被放到末尾，正文双链不足，页面容易孤岛化。
4. 规则与检查没有分层，规模上来后维护成本快速上升。

## 文档中心的方向

这个仓库的方向是 document-centered，而不是 tool-centered。

落到实操里，含义很具体：

1. 长期存在的对象是页面，而不是聊天记录；
2. 地址、链接、日志与变更轨迹，和生成文本同样重要；
3. 系统不仅要增加内容，还要不断改善自己的维护过程。

这也补上了它的历史脉络：它更接近 Engelbart 提出的 document-centered OHS/CODIAK 方向，而不是只围绕 prompt 展开的 workflow。真正长期存在的对象，是可寻址的页面和它们之间的链接，而不是某一次临时工具会话。

这也是为什么这个仓库更像一个 control-plane，而不是一个内容仓库。

## Control Plane 与 Data Plane

这个仓库有意识地只公开 control-plane：

1. `research/`：证据、样本规模、共性失效模式；
2. `schema/`：架构边界、运行规则、治理要求；
3. runbook：agent 应该如何 ingest、query、lint、handoff 和 promotion。

真正的 data-plane 则应由下游 Wiki 自己提供：

1. `raw/` 原始来源；
2. `wiki/` 维护中的知识页；
3. `index.md` 与 `log.md` 这样的运行文件。

这个拆分是刻意的。它让框架仓库保持可复用，同时不暴露真实知识库内容。

## 核心思路

Robust 的重点是“维护机制更稳”。

1. 保持 Karpathy 内核不变。
2. 用 `Schema` 定义底线、红线和可扩展边界。
3. 把 runtime 安全当作架构问题，而不是补丁问题。
4. 让维护方法本身随着知识库规模增长而一起改进。
5. 把具体实现与本框架仓库解耦，便于下游按需落地。

## 一个最小维护闭环

这个仓库想表达的闭环其实很简单：

1. ingest 一个新 source，先形成 draft 知识；
2. 更新它关联到的 entity、concept、synthesis 页面；
3. 更新 `index.md`，并在 `log.md` 追加记录；
4. 分层执行 lint；
5. 只有在证据和结构都通过后，才允许 promotion。

本仓库不绑定某一个固定实现，但它明确要求下游实现满足这个闭环契约。

## 为什么它不只是 Schema

这个仓库的重点不是“文件多”，而是把长期维护过程讲清楚。

当前公开内容主要从三个方面支撑这一点：

1. 两个脱敏真实生产 Wiki 的规模和运行信号；
2. `research/` 中的证据、工程观察和共性问题；
3. `schema/` 中把维护、runtime、promotion 讲清楚的规则层。

这里公开的是 control-plane；真正的 live corpus 由下游 Wiki 自己持有。

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
