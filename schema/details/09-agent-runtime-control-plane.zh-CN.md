# Agent Runtime Control Plane / 代理运行控制平面
**中文** | [English](./09-agent-runtime-control-plane.md)

## 为什么需要这页

LLM-Wiki 的很多问题，会先出现在运行过程里，随后才反映到页面上。

一个 source 可能触发多轮 `grep`、`read`、`write`、`lint`、`handoff`；一次 repair 可能同时影响多个双链页面；一个 subagent 也可能只能先返回部分发现，无法一次写完。链路一长，光有 tool call 能力并不够，关键是运行过程有没有边界、状态和交接。

这页只回答四个问题：

1. 当前任务谁负责；
2. 当前运行带着什么状态；
3. 到什么条件必须停下；
4. 下一阶段为什么能接着做。

## 这页负责什么

这页是 runtime 的设计文档，重点是三件事：

1. 定义运行角色；
2. 定义运行状态；
3. 定义调度、停止、交接和观测的最小结构。

[08-harmless-engineering.zh-CN.md](./08-harmless-engineering.zh-CN.md) 解释为什么长程 agent 必须低破坏、可观察、可停止；这页进一步回答：这样的 runtime 应该怎么组织。

## Control Plane 与 Data Plane

data-plane 承载知识内容本身：

1. `raw/` 原始来源；
2. `wiki/` 被维护的页面；
3. `index.md` 和 `log.md` 这样的运行文件。

control-plane 负责调度这些内容如何被处理：

1. 谁拥有任务；
2. 任务如何拆分；
3. 哪些状态需要保留；
4. 哪些证据必须跟着走；
5. 什么情况下继续；
6. 什么情况下停止或升级。

没有 control-plane，长程 agent 最终就会退化成“会调工具，但状态不可见、结果不可审查”。

## Runtime Roles

角色要显式定义。哪怕某个阶段里是同一个人、同一个 agent 暂时扮演多个角色，职责边界也要清楚。

### Controller

Controller 负责整次运行的总控。

职责包括：

1. 决定 scope；
2. 分配 ownership；
3. 合并有边界的输出；
4. 判断是否继续下一轮；
5. 在超出边界时升级。

Controller 的重点在于保证调度正确，核心职责是调度与收口。

### Worker

Worker 负责一个有边界的执行单元。

典型单元包括：

1. 一个 source；
2. 一个 wiki 页面；
3. 一批 repair 目标；
4. 一轮 verification。

Worker 不一定要完成整个任务，但必须把自己负责那一段的状态交代清楚。

### Verifier

Verifier 负责判断结果是否已经安全到可以被后续依赖。

尤其在这些场景里要单独看待：

1. `draft` 准备进入 `stable`；
2. 某条结论会影响多个双链页面；
3. 一次 repair batch 改了多个 entity 或 concept；
4. 前一轮运行已经暴露出漂移、遗漏或幻觉风险。

风险越高，Verifier 越不应和 Writer 是同一个执行者。

### Human In The Loop

human-in-the-loop 负责最终决策：

1. 设定目标；
2. 划定 source 范围；
3. 批准例外；
4. 裁决冲突；
5. 决定 blocked 结果是否仍可发布。

好的 control-plane 会把 human-in-the-loop 放在关键节点，并降低接手成本。

## Runtime State

长程工作不能只靠聊天上下文，必须把状态分层。

### Run State

Run state 描述当前这次执行本身。

典型字段：

1. `run_id`
2. `stage`
3. `owned_files`
4. `risk_level`
5. `budget_remaining`
6. `stop_reason`

它描述的是“这次运行”本身，对应运行态，独立于知识层。

### Working Memory

Working memory 是当前几步执行里临时保留的信息。

例如：

1. 当前 source 切片；
2. 正在审查的页面候选；
3. 尚未解决的冲突；
4. 下一次 tool call 要用的局部笔记。

它可以压缩、可以不完整，也可以被丢弃；它不属于长期真值。

### Handoff State

Handoff state 是交给下一阶段、下一个 subagent 或 human-in-the-loop 的最小交接包。

至少要说明：

1. 改了什么；
2. 用了哪些证据；
3. 还剩什么没解决；
4. 下一步该谁接手；
5. 这次为什么停下。

如果 handoff 说不清这五件事，主 agent 很难继续正确调度。

## Execution Boundaries

长程 agent 容易失控，核心原因通常是边界不清。

### Scope

Scope 回答：这次运行到底允许触碰什么。

一个可控的 scope，通常应该缩到下面这类粒度：

1. 一个 source；
2. 一个目标文件；
3. 一个有边界的 batch；
4. 一个明确的 verification 问题。

Scope 不清，ownership 就一定会漂。

### Budget

Budget 回答：这次运行最多允许消耗多少执行资源，然后必须重新判断。

常见预算包括：

1. tool-call 次数；
2. candidate 数量；
3. write 次数；
4. 时间或 turn 上限；
5. 无进展情况下的重试上限。

很多长程失败，先是 overrun failure，后面才演变成内容错误。

### Termination

Termination 必须显式。

一轮运行应在以下条件之一满足时停下：

1. 有边界的任务已完成；
2. 预算耗尽；
3. 歧义仍然过高；
4. 重复 `grep/read/edit` 已经不再减少不确定性；
5. handoff 已经足够清楚，可以交给下一位 owner。

stop condition 的价值在于让停下来的原因可判断、可复盘。

### Escalation

Escalation 回答：什么时候当前 runtime 不该继续硬跑。

常见升级条件：

1. source 冲突持续存在；
2. 证据缺失；
3. 同一文件反复读取却没有进展；
4. 同一目标页反复编辑却没有收敛；
5. 当前 worker 已经不适合继续处理这个风险等级。

Escalation 属于 control-plane 的常规机制。

## Observability

Observability 决定了这个 runtime 能不能被审查、接手和复盘。

### Trace

一条有用的 trace，至少应能回答：

1. 读了什么；
2. 改了什么；
3. 调了哪些工具；
4. 发生了哪些 handoff；
5. 写后做了哪些验证。

### Stop Reason

每一轮运行都应该显式暴露 stop reason，例如：

1. `completed`
2. `budget_reached`
3. `awaiting_verification`
4. `awaiting_human`
5. `blocked_by_conflict`

没有 stop reason，下一位 owner 就只能猜。

### Coverage

Coverage 回答：这次运行到底真正检查了什么。

例如：

1. 哪些文件读完整了；
2. 哪些文件只读了一部分；
3. 哪些文件因为拆分被跳过；
4. 哪些页面被更新了；
5. 哪些页面虽然进入候选，但最终没被触碰。

`processed` 不等于 `fully read`。对长文件场景来说，coverage 比“处理过”更有价值。

### Health Signals

Control-plane 应尽早暴露不健康迹象。

例如：

1. 同一文件上的重复 `grep/read`；
2. 同一页面上的重复编辑；
3. tool-call 数上涨，但 open question 没下降；
4. 写后没有对应 lint；
5. handoff 状态很弱，甚至缺失。

这些信号出现得越早，后面的修复成本越低。

## Handoff Contract

具体 enforcement 放在 runbook 更合适，但 control-plane 里至少要把 handoff contract 讲清楚。

一个示意结构如下：

```yaml
run_id: "run-..."
stage: "ingest|query|lint|verify"
owned_files:
  - "wiki/entities/..."
files_changed:
  - "wiki/entities/..."
evidence_refs:
  - "src-..."
open_questions:
  - "..."
risk_level: "R1|R2|R3"
stop_reason: "completed|budget_reached|awaiting_verification|awaiting_human|blocked"
next_owner: "controller|worker|verifier|human"
coverage:
  fully_read:
    - "raw/..."
  partially_read:
    - "wiki/index.md"
```

重点不在字段名一模一样，而在于 handoff 必须满足两件事：

1. machine-readable；
2. 下一阶段真的能据此继续执行。

## 它如何接入 Ingest / Query / Lint

Control-plane 不替代 Karpathy 的闭环，它让这条闭环可控。

对 `ingest`：

1. controller 决定 source 范围；
2. worker 负责读取与抽取；
3. verifier 在 promotion 前检查证据是否足够。

对 `query`：

1. controller 决定本轮 query 可以依赖哪些知识；
2. worker 基于已维护页面做综合；
3. verifier 在高影响结论被回写或复用前做核查。

对 `lint`：

1. controller 定义 lint 目标集合；
2. worker 在有边界的范围内做结构或内容检查；
3. verifier 确认修复没有静默改变原意。

## 常见失效模式

LLM-Wiki 里常见的 runtime 失效模式，基本集中在下面几类：

1. worker 一直在读，但不返回结构化状态；
2. controller 合并了 prose，却没有带够 evidence metadata；
3. working memory 越来越多，但 ownership 越来越不清楚；
4. chunk 文件或中间切片被错当成稳定 source；
5. stop reason 和 open questions 还没明确，就先写回 wiki；
6. 后续 stage 无法判断上一轮究竟是读完整了 source，还是只摸到了一部分。

这些问题先是 control-plane failure，后面才会变成内容 failure。

## 推荐阅读

下面这组阅读材料，分别对应三条线：

1. 文档中心系统；
2. agent runtime；
3. state、handoff 与 observability。

| 阅读材料 | 为什么值得读 | 链接 |
|---|---|---|
| Technology Template Project - OHS Framework | 文档中心系统、细粒度寻址、动态链接的一手来源。 | [dougengelbart.org/content/view/110/](https://dougengelbart.org/content/view/110/) |
| About OHS | 用更短的方式解释 OHS 为什么是基础设施。 | [dougengelbart.org/about/ohs.html](https://dougengelbart.org/about/ohs.html) |
| Toward High-Performance Organizations: A Strategic Role for Groupware | 把 OHS、CODIAK、bootstrapping 接起来。 | [dougengelbart.org/content/view/116/](https://www.dougengelbart.org/content/view/116/) |
| ReAct: Synergizing Reasoning and Acting in Language Models | reasoning + action 回路的经典论文。 | [arxiv.org/abs/2210.03629](https://arxiv.org/abs/2210.03629) |
| Model Context Protocol Specification | 适合看 tools、resources、progress、cancellation、logging 与 trust boundary。 | [modelcontextprotocol.io/specification/2025-06-18](https://modelcontextprotocol.io/specification/2025-06-18) |
| Effective context engineering for AI agents | 适合建立“context 是有限 runtime 资源”的心智模型。 | [anthropic.com/engineering/effective-context-engineering-for-ai-agents](https://www.anthropic.com/engineering/effective-context-engineering-for-ai-agents) |
| Writing effective tools for AI agents | 适合看工具边界、工具描述与 eval-driven 改进。 | [anthropic.com/engineering/writing-tools-for-agents](https://www.anthropic.com/engineering/writing-tools-for-agents) |
| Handoffs - OpenAI Agents SDK | 适合理解 specialist delegation 与执行权转移。 | [openai.github.io/openai-agents-python/handoffs/](https://openai.github.io/openai-agents-python/handoffs/) |
| Context management - OpenAI Agents SDK | 适合理解本地 runtime state 和模型可见上下文的区别。 | [openai.github.io/openai-agents-python/context/](https://openai.github.io/openai-agents-python/context/) |
| Tracing - OpenAI Agents SDK | 适合理解 tool calls、handoffs、guardrails 的 trace 级观测。 | [openai.github.io/openai-agents-python/tracing/](https://openai.github.io/openai-agents-python/tracing/) |
| Chain of Agents | 适合理解 manager-worker 结构在长上下文任务中的价值。 | [research.google/blog/chain-of-agents-large-language-models-collaborating-on-long-context-tasks/](https://research.google/blog/chain-of-agents-large-language-models-collaborating-on-long-context-tasks/) |
| Choose your agentic AI architecture components | 适合理解 runtime、memory、observability 都是系统设计选择。 | [docs.cloud.google.com/architecture/choose-agentic-ai-architecture-components](https://docs.cloud.google.com/architecture/choose-agentic-ai-architecture-components) |

## 它和 08 以及 CLAUDE 的关系

这三篇文档的分工可以明确成：

1. [08-harmless-engineering.zh-CN.md](./08-harmless-engineering.zh-CN.md)：解释为什么 runtime 必须低破坏、有边界、可恢复；
2. 这篇文档：解释 runtime control-plane 应该怎样建模；
3. [robust-llm-wiki-CLAUDE.zh-CN.md](../robust-llm-wiki-CLAUDE.zh-CN.md)：解释日常运行里具体怎么执行。
