# Agent Runtime Control Plane
[中文](./09-agent-runtime-control-plane.zh-CN.md) | **English**

## Why This Page Exists

Many LLM-Wiki failures surface in the runtime first and only later show up on the page.

One source may trigger multiple rounds of `grep`, `read`, `write`, `lint`, and `handoff`. One repair may touch several linked pages. One subagent may need to return partial findings instead of forcing a full page in one pass. Once the chain gets long, tool-calling is not enough. What matters is whether the runtime has boundaries, state, and a handoff model.

This page answers four questions:

1. who owns the current task;
2. what state the run is carrying;
3. what makes the run stop;
4. why the next stage can trust the previous one.

## What This Page Covers

This is a runtime design document. It focuses on three things:

1. runtime roles;
2. runtime state;
3. the minimum structure for routing, stopping, handoff, and observability.

[08-harmless-engineering.md](./08-harmless-engineering.md) explains why long-running agents must stay low-damage, observable, and stoppable. This page explains how that runtime should be organized.

## Control Plane vs Data Plane

The split is straightforward.

The data-plane holds the knowledge work itself:

1. `raw/` source material;
2. `wiki/` maintained pages;
3. operating files such as `index.md` and `log.md`.

The control-plane determines how that work is processed:

1. who owns the task;
2. how the task is decomposed;
3. what state must be preserved;
4. what evidence must travel with the run;
5. when the run can continue;
6. when it must stop or escalate.

Without a control-plane, a long-running agent tends to collapse into tool use with hidden state and unreviewable writes.

## Runtime Roles

Roles should be explicit. Even if one human or one program temporarily plays several roles, the responsibilities should stay separate.

### Controller

The controller owns the run as a whole.

Its job is to:

1. choose scope;
2. assign ownership;
3. merge bounded outputs;
4. decide whether another round is justified;
5. escalate when boundaries are exceeded.

The controller's primary job is correct routing.

### Worker

The worker owns one bounded unit of execution.

Typical units include:

1. one source;
2. one wiki page;
3. one repair batch;
4. one verification pass.

The worker does not need to finish the whole job. It needs to return clear state for the part it owned.

### Verifier

The verifier decides whether an output is safe to depend on.

This matters most when:

1. `draft` may become `stable`;
2. one conclusion affects several linked pages;
3. one repair batch changes multiple entities or concepts;
4. the previous run already exposed drift, omission, or hallucination risk.

The higher the risk, the less the verifier should overlap with the writer.

### Human In The Loop

The human-in-the-loop keeps final authority over:

1. goals;
2. source scope;
3. exceptions;
4. unresolved conflicts;
5. whether a blocked result is still acceptable to publish.

A good control-plane keeps a human-in-the-loop at key decision points and lowers the cost of stepping in.

## Runtime State

Long-horizon work cannot rely on chat context alone. State needs layers.

### Run State

Run state describes the execution itself.

Typical fields:

1. `run_id`
2. `stage`
3. `owned_files`
4. `risk_level`
5. `budget_remaining`
6. `stop_reason`

This state belongs to the current run, not the long-term knowledge base.

### Working Memory

Working memory is the temporary information kept for the next few steps.

Examples:

1. the current source slice;
2. candidate pages under review;
3. unresolved conflicts;
4. local notes for the next tool call.

It may be partial, compressed, or discarded. It does not belong in durable truth.

### Handoff State

Handoff state is the minimum package needed by the next stage, the next subagent, or a human-in-the-loop.

At minimum, it should explain:

1. what changed;
2. what evidence was used;
3. what remains unresolved;
4. who should act next;
5. why the run stopped.

If a handoff cannot answer those five questions, the main agent cannot route the next step reliably.

## Execution Boundaries

Long-running agents usually fail when boundaries are weak.

### Scope

Scope answers what the run is allowed to touch.

A controllable scope usually means:

1. one source;
2. one target file;
3. one bounded batch;
4. one clear verification question.

If scope is vague, ownership will drift.

### Budget

Budget answers how much execution is allowed before the system must reassess.

Common budgets include:

1. tool-call count;
2. candidate count;
3. write count;
4. time or turn cap;
5. retry cap after no progress.

Many long-horizon failures begin as overrun failures before they become content failures.

### Termination

Termination should be explicit.

A run should stop when one of the following is true:

1. the bounded task is complete;
2. the budget is exhausted;
3. ambiguity remains too high;
4. repeated `grep/read/edit` no longer reduces uncertainty;
5. the handoff is clear enough for the next owner.

The value of a stop condition is inspectability.

### Escalation

Escalation answers when the runtime should stop pushing forward.

Common escalation triggers:

1. source conflict persists;
2. evidence is missing;
3. the same file is re-read without progress;
4. the same page is re-edited without convergence;
5. the current worker is no longer appropriate for the risk level.

Escalation is part of the design.

## Observability

Observability determines whether a runtime can be reviewed, resumed, and debugged.

### Trace

A useful trace should show:

1. what was read;
2. what was changed;
3. what tools were called;
4. what handoffs occurred;
5. what validation followed the write.

### Stop Reason

Every run should expose a stop reason such as:

1. `completed`
2. `budget_reached`
3. `awaiting_verification`
4. `awaiting_human`
5. `blocked_by_conflict`

Without a stop reason, the next owner has to guess.

### Coverage

Coverage answers what the run actually examined.

Examples:

1. which files were fully read;
2. which files were only partially read;
3. which files were skipped because of decomposition;
4. which pages were updated;
5. which pages entered the candidate set but were left untouched.

`processed` is weaker than `fully read`. For long files, coverage carries more value than a generic completion claim.

### Health Signals

The control-plane should expose unhealthy patterns early.

Examples:

1. repeated `grep/read` on the same file;
2. repeated edits on the same page;
3. tool-call count rises while open questions stay flat;
4. writes happen without post-write lint;
5. handoff state is weak or missing.

The earlier those signals surface, the cheaper the repair.

## Handoff Contract

The exact enforcement belongs in the runbook, but the control-plane should define the shape of a handoff.

An illustrative structure:

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

The point is not exact field names. The point is that handoff should be:

1. machine-readable;
2. strong enough for the next stage to act on.

## How It Fits Ingest / Query / Lint

The control-plane does not replace the Karpathy loop. It makes that loop runnable at scale.

For `ingest`:

1. the controller chooses source scope;
2. the worker reads and extracts;
3. the verifier checks evidence before promotion.

For `query`:

1. the controller decides what knowledge is in play;
2. the worker synthesizes against maintained pages;
3. the verifier checks high-impact conclusions before they are written back or reused.

For `lint`:

1. the controller defines the lint target set;
2. the worker checks structure or content within a bounded scope;
3. the verifier confirms that the fix did not silently change meaning.

## Common Failure Modes

Common LLM-Wiki runtime failures usually fall into a small set:

1. the worker keeps reading but never returns structured state;
2. the controller merges prose without enough evidence metadata;
3. working memory grows while ownership gets less clear;
4. chunk files or intermediate slices are mistaken for stable source;
5. the run writes into the wiki before stop reasons and open questions are explicit;
6. a later stage cannot tell whether the previous run fully read the source or only touched part of it.

These begin as control-plane failures before they become content failures.

## Recommended Reading

The reading list below maps to three lines:

1. document-centered systems;
2. agent runtime;
3. state, handoff, and observability.

| Reading | Why it matters | URL |
|---|---|---|
| Technology Template Project - OHS Framework | Primary source for document-centered systems, fine-grained addressability, and dynamic linking. | [dougengelbart.org/content/view/110/](https://dougengelbart.org/content/view/110/) |
| About OHS | Short explanation of why OHS is infrastructure rather than a writing tool. | [dougengelbart.org/about/ohs.html](https://dougengelbart.org/about/ohs.html) |
| Toward High-Performance Organizations: A Strategic Role for Groupware | Connects OHS, CODIAK, and bootstrapping. | [dougengelbart.org/content/view/116/](https://www.dougengelbart.org/content/view/116/) |
| ReAct: Synergizing Reasoning and Acting in Language Models | Canonical paper for the reasoning-plus-action loop. | [arxiv.org/abs/2210.03629](https://arxiv.org/abs/2210.03629) |
| Model Context Protocol Specification | Useful for tools, resources, progress, cancellation, logging, and trust boundaries. | [modelcontextprotocol.io/specification/2025-06-18](https://modelcontextprotocol.io/specification/2025-06-18) |
| Effective context engineering for AI agents | Useful for treating context as a finite runtime resource. | [anthropic.com/engineering/effective-context-engineering-for-ai-agents](https://www.anthropic.com/engineering/effective-context-engineering-for-ai-agents) |
| Writing effective tools for AI agents | Useful for tool boundaries, tool descriptions, and eval-driven improvement. | [anthropic.com/engineering/writing-tools-for-agents](https://www.anthropic.com/engineering/writing-tools-for-agents) |
| Handoffs - OpenAI Agents SDK | Useful for specialist delegation and control transfer. | [openai.github.io/openai-agents-python/handoffs/](https://openai.github.io/openai-agents-python/handoffs/) |
| Context management - OpenAI Agents SDK | Useful for separating local runtime state from model-visible context. | [openai.github.io/openai-agents-python/context/](https://openai.github.io/openai-agents-python/context/) |
| Tracing - OpenAI Agents SDK | Useful for trace-level observability across tools, handoffs, and guardrails. | [openai.github.io/openai-agents-python/tracing/](https://openai.github.io/openai-agents-python/tracing/) |
| Chain of Agents | Useful for manager-worker structure on long-context tasks. | [research.google/blog/chain-of-agents-large-language-models-collaborating-on-long-context-tasks/](https://research.google/blog/chain-of-agents-large-language-models-collaborating-on-long-context-tasks/) |
| Choose your agentic AI architecture components | Useful for viewing runtime, memory, and observability as system design choices. | [docs.cloud.google.com/architecture/choose-agentic-ai-architecture-components](https://docs.cloud.google.com/architecture/choose-agentic-ai-architecture-components) |

## Relationship To 08 And CLAUDE

The split is simple:

1. [08-harmless-engineering.md](./08-harmless-engineering.md): why the runtime must stay low-damage, bounded, and recoverable;
2. this page: how the runtime control-plane should be modeled;
3. [robust-llm-wiki-CLAUDE.md](../robust-llm-wiki-CLAUDE.md): how that model is executed day to day.
