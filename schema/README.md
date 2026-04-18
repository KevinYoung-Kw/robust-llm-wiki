# Schema Docs: Start Here

[中文](./README.zh-CN.md) | **English**

> [!IMPORTANT]
> Start with [SPEC.md](./SPEC.md), then read [robust-llm-wiki-CLAUDE.md](./robust-llm-wiki-CLAUDE.md).
> `SPEC.md` explains the architecture and boundaries. `robust-llm-wiki-CLAUDE.md` explains how agents should operate inside those boundaries.

> [!TIP]
> Current released schema baseline: `v0.1.3`

The `schema/` folder answers a practical question: how should a long-running LLM-Wiki be structured, maintained, and governed so that it stays useful over time?

Read these documents in layers rather than all at once. Start with the architecture, then the operating contract, then the focused rule pages that match your task.

## Recommended Reading Order

1. [SPEC.md](./SPEC.md) for the core system model, invariants, and extension boundaries.
2. [robust-llm-wiki-CLAUDE.md](./robust-llm-wiki-CLAUDE.md) for agent operating rules, ownership, promotion gates, and risk controls.
3. [OPEN_SOURCE_POLICY.md](./OPEN_SOURCE_POLICY.md) for licensing, provenance, and disclosure requirements.
4. [`details/`](./details/) for focused guidance on fields, ingest, lint, hallucination control, hooks, long-file workflows, and model allocation.
5. `*.zh-CN.md` mirror files when you want the Chinese version of the same material.

## Reader Paths

### Fastest Overview

If you only want the shortest correct path:

1. [SPEC.md](./SPEC.md)
2. [robust-llm-wiki-CLAUDE.md](./robust-llm-wiki-CLAUDE.md)

### I Need To Run Agents Safely

1. [robust-llm-wiki-CLAUDE.md](./robust-llm-wiki-CLAUDE.md)
2. [details/03-lint-playbook.md](./details/03-lint-playbook.md)
3. [details/06-claude-hook-guidelines.md](./details/06-claude-hook-guidelines.md)
4. [details/08-harmless-engineering.md](./details/08-harmless-engineering.md)
5. [details/09-agent-runtime-control-plane.md](./details/09-agent-runtime-control-plane.md)

### I Need To Define Or Extend Fields

1. [SPEC.md](./SPEC.md)
2. [details/01-attribute-proposal.md](./details/01-attribute-proposal.md)
3. [details/02-ingest-field-rules.md](./details/02-ingest-field-rules.md)

### I Need Draft-To-Stable And Evidence Rules

1. [robust-llm-wiki-CLAUDE.md](./robust-llm-wiki-CLAUDE.md)
2. [details/02-ingest-field-rules.md](./details/02-ingest-field-rules.md)
3. [details/03-lint-playbook.md](./details/03-lint-playbook.md)

### I Need Long-File Or Agent-Team Guidance

1. [robust-llm-wiki-CLAUDE.md](./robust-llm-wiki-CLAUDE.md)
2. [details/05-file2agent-context-engineering.md](./details/05-file2agent-context-engineering.md)
3. [details/08-harmless-engineering.md](./details/08-harmless-engineering.md)
4. [details/09-agent-runtime-control-plane.md](./details/09-agent-runtime-control-plane.md)

### I Need Hallucination And Model Guidance

1. [details/04-hallucination-control.md](./details/04-hallucination-control.md)
2. [details/07-turbo-model-value.md](./details/07-turbo-model-value.md)
3. [details/08-harmless-engineering.md](./details/08-harmless-engineering.md)

### I Need Legal Or Open-Source Policy

1. [OPEN_SOURCE_POLICY.md](./OPEN_SOURCE_POLICY.md)

## What Each Top-Level Schema Doc Is For

| Document | Purpose | Read This When |
|---|---|---|
| [SPEC.md](./SPEC.md) | Architectural reference for the framework: invariants, boundaries, lifecycle, and extension limits. | You need to understand what the system is before deciding how to operate it. |
| [robust-llm-wiki-CLAUDE.md](./robust-llm-wiki-CLAUDE.md) | Operating contract for agents: stage ownership, handoffs, promotion gates, agent-team rules, lint cadence, and escalation. | You are about to run or design an agent workflow. |
| [OPEN_SOURCE_POLICY.md](./OPEN_SOURCE_POLICY.md) | License, provenance, compliance, and private security disclosure policy. | You are contributing, publishing, or reviewing third-party material. |
| [`details/`](./details/) | Focused rule pages and execution guidance for individual topics. | You already know the top-level model and need a specific rule or playbook. |

## Detail Pages At A Glance

| File | Topic |
|---|---|
| [details/01-attribute-proposal.md](./details/01-attribute-proposal.md) | Minimum page fields and extensible metadata direction |
| [details/02-ingest-field-rules.md](./details/02-ingest-field-rules.md) | Ingest contracts, normalization, defaults, updates, and conflict handling |
| [details/03-lint-playbook.md](./details/03-lint-playbook.md) | Lint order and maintenance checking rhythm |
| [details/04-hallucination-control.md](./details/04-hallucination-control.md) | Low-hallucination operating rationale and repair guidance |
| [details/05-file2agent-context-engineering.md](./details/05-file2agent-context-engineering.md) | Long-file reading, context limits, and `1 subagent -> 1 file` style workflows |
| [details/06-claude-hook-guidelines.md](./details/06-claude-hook-guidelines.md) | Hook trigger recommendations and lightweight guardrails |
| [details/07-turbo-model-value.md](./details/07-turbo-model-value.md) | Where faster or lower-cost models help, and where they should not lead |
| [details/08-harmless-engineering.md](./details/08-harmless-engineering.md) | Low-damage long-horizon agent behavior, observability, and model/runtime choice |
| [details/09-agent-runtime-control-plane.md](./details/09-agent-runtime-control-plane.md) | Runtime design: roles, state, boundaries, handoffs, and observability |

## Reading Principle

When in doubt, read from general to specific:

1. Architecture in `SPEC`
2. Operating rules in `robust-llm-wiki-CLAUDE`
3. Focused implementation guidance in `details/`
4. Chinese mirrors only when you need the translated version of the same document
