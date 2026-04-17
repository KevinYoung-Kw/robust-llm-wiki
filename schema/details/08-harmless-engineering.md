# Harmless Engineering
[中文](./08-harmless-engineering.zh-CN.md) | **English**

## The Short Version

Harmless Engineering is the runtime control-plane for long-running LLM-Wiki maintenance.

This page is not about vendor ranking. It is about making agent execution stay bounded, observable, and recoverable while the wiki keeps changing.

## Why This Page Exists

In practice, LLM-Wiki failure often does not start with one bad sentence. It starts with one bad run.

Typical failure patterns look like this:

1. the same file gets read repeatedly without progress,
2. the same page gets edited repeatedly without convergence,
3. a subagent keeps running but never hands control back,
4. low-quality intermediate state gets written into the wiki and reused later.

That is a runtime problem, not only a writing problem.

## The Runtime Model

For this repository, a healthy runtime model has five parts:

1. `controller`: owns run state, routing, and final merge decisions,
2. `worker`: owns a bounded unit of work, usually one file or one batch,
3. `handoff`: returns machine-readable state upward,
4. `observability`: exposes what happened during the run,
5. `termination`: makes the run stop at a known boundary.

If one of those parts is missing, the system becomes harder to trust as the wiki grows.

## Controller And Worker

The main agent and subagents should not behave like interchangeable writers.

The healthier pattern is:

1. the controller assigns scope,
2. the worker reads and extracts inside that scope,
3. the worker returns structured output,
4. the controller decides whether another round is justified.

This is why `1 subagent -> 1 file` is useful. It gives the runtime a clearer ownership boundary.

## State And Memory

Long-running wiki maintenance needs explicit state, not only chat context.

At minimum, the runtime should make these things visible:

1. `run_id`,
2. `files_changed`,
3. `evidence_refs`,
4. `open_questions`,
5. `risk_level`,
6. `next_owner`.

Stable knowledge should live in the wiki. Runtime state should be surfaced through handoff records, logs, and reviewable artifacts rather than hidden inside one conversation window.

## Observability

The runtime should make it easy to answer simple questions:

1. what file was read,
2. what changed,
3. what is still unresolved,
4. where the run stopped,
5. whether the run is still healthy.

In practice, useful signals include:

1. repeated `grep/read` on the same file,
2. repeated edits on the same target,
3. growing tool-call count without shrinking open questions,
4. missing or weak handoff output,
5. missing lint or validation after writes.

If the system cannot expose these signals, it will drift before the team notices.

## Termination Matters

A long-running agent should not stop only when it \"feels done.\"

Good runtime behavior requires explicit stop conditions, for example:

1. candidate cap reached,
2. tool-call budget reached,
3. ambiguity threshold reached,
4. handoff record completed,
5. promotion gate not yet satisfied.

This is the point of bounded execution. It is not about making the system smaller. It is about making progress inspectable.

## Why This Makes The Wiki Better

Harmless Engineering protects the wiki in three ways:

1. it lowers the chance that one bad run spreads across many pages,
2. it makes repair cheaper when something does go wrong,
3. it lets weaker runtime stacks remain usable inside harder boundaries.

Without this layer, the wiki may still look correct locally while the maintenance loop is already degrading underneath.

## Relationship To The Rest Of The Framework

This page connects directly to several other rule pages:

1. [04-hallucination-control.md](./04-hallucination-control.md): protects factual quality,
2. [05-file2agent-context-engineering.md](./05-file2agent-context-engineering.md): protects ownership and coverage,
3. [06-claude-hook-guidelines.md](./06-claude-hook-guidelines.md): protects near-write guardrails,
4. [03-lint-playbook.md](./03-lint-playbook.md): protects post-write structure health,
5. [robust-llm-wiki-CLAUDE.md](../robust-llm-wiki-CLAUDE.md): defines the operator runbook.

In short:

- the wiki is the knowledge layer,
- lint is the structural guardrail,
- File2Agent is the ownership rule,
- Harmless Engineering is the runtime control-plane.

