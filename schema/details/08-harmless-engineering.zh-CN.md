# Harmless 工程
**中文** | [English](./08-harmless-engineering.md)

## 一句话版本

Harmless 工程是长期 LLM-Wiki 维护里的 runtime control-plane。

这页不是做厂商评价，也不是做模型排行。它讨论的是：当 agent 长时间运行时，怎样让执行过程保持有边界、可观察、可恢复。

## 为什么需要这页

在真实维护里，很多问题不是从一句错误回答开始的，而是从一次失控的运行开始的。

常见现象包括：

1. 同一个文件被反复 `read`，但没有实质进展；
2. 同一个页面被反复编辑，但没有真正收敛；
3. subagent 一直在跑，却迟迟不 handoff；
4. 低质量中间状态被写进 wiki，后面又被继续复用。

这说明问题不只是写作问题，更是 runtime 问题。

## 运行模型

对这个仓库来说，一个更健康的 runtime 模型至少要有五部分：

1. `controller`：持有 run state，负责路由和最终合并；
2. `worker`：负责有边界的局部任务，通常是单文件或单批次；
3. `handoff`：把结构化状态向上返回；
4. `observability`：让运行过程可检查；
5. `termination`：让任务在明确边界停下。

少掉其中任意一层，系统在规模变大后都会更难信任。

## Controller 与 Worker

主 agent 和 subagent 不应该只是“多个 writer”。

更健康的方式是：

1. controller 分配范围；
2. worker 在该范围内完成读取与抽取；
3. worker 回传结构化结果；
4. controller 决定是否值得继续下一轮。

这也是为什么 `1 subagent -> 1 file` 有价值。它给 runtime 提供了更清楚的 ownership 边界。

## State 与 Memory

长期 wiki 维护需要显式状态，不能只靠聊天上下文。

至少应当把这些东西做成可见状态：

1. `run_id`
2. `files_changed`
3. `evidence_refs`
4. `open_questions`
5. `risk_level`
6. `next_owner`

稳定知识应该留在 wiki 中。运行时状态则应通过 handoff、日志和可审查产物被显式暴露，而不是藏在某一段对话窗口里。

## Observability

一个健康的 runtime，至少要让人容易回答这些问题：

1. 读了什么文件；
2. 改了什么内容；
3. 还有什么未解决问题；
4. 停在了哪里；
5. 当前这次运行是否仍然健康。

在实操里，比较有价值的信号通常包括：

1. 同一文件上的重复 `grep/read`；
2. 同一目标页上的重复编辑；
3. tool call 数一直上涨，但 open questions 没减少；
4. handoff 输出很弱，或者缺失；
5. 写入之后没有对应的 lint 或验证。

如果系统暴露不出这些信号，漂移往往会先发生，团队后发现。

## Termination 很重要

一个长程 agent 不能只在“感觉做完了”时停下。

更稳妥的运行方式，需要明确 stop condition，例如：

1. 候选数量达到上限；
2. tool-call 预算到上限；
3. 歧义达到阈值；
4. handoff 记录已完成；
5. promotion gate 尚未满足。

这就是 bounded execution 的意义。它不是为了让系统变小，而是为了让进展可检查。

## 为什么这会让 Wiki 更好

Harmless 工程会从三个方面保护 wiki：

1. 降低一次坏运行扩散到多页的概率；
2. 在出问题时让修复成本更低；
3. 让长程能力一般的 runtime stack 也能在更硬的边界里被安全使用。

如果没有这一层，wiki 即使局部看起来没问题，底层维护闭环也可能已经开始退化。

## 它和其他规则的关系

这页和下面几篇是直接相连的：

1. [04-hallucination-control.zh-CN.md](./04-hallucination-control.zh-CN.md)：保护事实质量；
2. [05-file2agent-context-engineering.zh-CN.md](./05-file2agent-context-engineering.zh-CN.md)：保护所有权与覆盖率；
3. [06-claude-hook-guidelines.zh-CN.md](./06-claude-hook-guidelines.zh-CN.md)：保护写入附近的守门；
4. [03-lint-playbook.zh-CN.md](./03-lint-playbook.zh-CN.md)：保护写后结构健康；
5. [robust-llm-wiki-CLAUDE.zh-CN.md](../robust-llm-wiki-CLAUDE.zh-CN.md)：定义 operator runbook。

可以直接理解为：

- wiki 是知识层，
- lint 是结构守门，
- File2Agent 是 ownership 规则，
- Harmless 工程是 runtime control-plane。

