# Quickstart

**中文** | [English](./README.md)

这份 quickstart 专门围绕两种最真实的入口场景设计：

1. 你已经有自己的 LLM-Wiki，想把这个仓库里的经验接过去；
2. 你还没有 LLM-Wiki，想先让 agent 根据这套框架帮你生成一份可用的操作文件。

## 路径 A：你已经有自己的 LLM-Wiki

如果你已经在维护自己的 wiki，目标不是把这个仓库整套照搬过去，而是让 agent 先吃透 [schema/robust-llm-wiki-CLAUDE.md](../schema/robust-llm-wiki-CLAUDE.md) 里的运行规则，再按你现有实现做选择性吸收。

更推荐的流程是：

1. clone 这个仓库，或者直接把仓库链接交给 agent，
2. 让 agent 先读 [schema/robust-llm-wiki-CLAUDE.md](../schema/robust-llm-wiki-CLAUDE.md)，
3. 再让它对照你当前的 wiki 结构做比对，
4. 最后只提出适合你当前系统的调优建议，而不是强行重写。

可以直接使用这样的提示词：

```text
Read robust-llm-wiki's schema/robust-llm-wiki-CLAUDE.md.
Compare it against my current LLM-Wiki implementation.
Do not force a rewrite.
Instead:
1. identify which rules already fit my system,
2. identify which rules need adaptation,
3. propose a minimal migration plan,
4. draft an agent operating file for my repo that preserves my current architecture where possible.
```

在这条路径下，最好的结果通常不是“照抄他们的文件”，而是“把 robust 的运行经验翻译成我自己仓库的语言、目录和约束”。

## 路径 B：你还没有 LLM-Wiki

如果你现在还没有自己的 LLM-Wiki，其实入口可以更简单：直接把这个仓库当成 policy source，让你的 coding agent 为它自己的生态生成对应的操作文件。

核心思路是：

1. 这个仓库提供运行模式，
2. 你的 agent 负责把它翻译成自己最理解的控制文件。

可以直接用一句话启动：

```text
Read robust-llm-wiki's schema/robust-llm-wiki-CLAUDE.md.
Based on it, generate the agent operating file for my environment.
If I use Claude Code, generate CLAUDE.md.
If I use Codex or a generic coding agent workflow, generate AGENTS.md.
If I use Gemini, generate GEMINI.md.
Keep the core operating ideas, but adapt names, commands, and conventions to the target agent.
```

这通常已经足够得到第一版可用结果。

## 下一步阅读

1. [schema/README.zh-CN.md](../schema/README.zh-CN.md)
2. [schema/SPEC.zh-CN.md](../schema/SPEC.zh-CN.md)
3. [schema/robust-llm-wiki-CLAUDE.zh-CN.md](../schema/robust-llm-wiki-CLAUDE.zh-CN.md)
