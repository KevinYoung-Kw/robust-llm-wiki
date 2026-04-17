# Robust LLM-Wiki

[中文](./README.zh-CN.md) | **English**

[![Latest Release](https://img.shields.io/github/v/release/KevinYoung-Kw/robust-llm-wiki?label=release)](https://github.com/KevinYoung-Kw/robust-llm-wiki/releases)
[![GitHub Stars](https://img.shields.io/github/stars/KevinYoung-Kw/robust-llm-wiki?style=flat)](https://github.com/KevinYoung-Kw/robust-llm-wiki/stargazers)
[![License](https://img.shields.io/github/license/KevinYoung-Kw/robust-llm-wiki)](./LICENSE)
[![Docs](https://img.shields.io/badge/docs-bilingual-2f6feb)](./schema/README.md)

![Robust LLM-Wiki Banner](./assets/github-banner-en.webp)

Robust LLM-Wiki is a document-centered control-plane for long-term Wiki maintenance with AI.

This repository does not publish a private wiki. It publishes the framework for keeping one stable over time: how to preserve Wiki form, keep wikilinks healthy, control agent runtime drift, and make maintenance auditable as the corpus grows.

The project was extracted from two long-running real LLM-Wikis. The goal is not one-shot generation. The goal is repeatable maintenance.

> [!IMPORTANT]
> **Read This First**
> If you want the best entry into the schema docs, start with:
> **[`schema/README.md`](./schema/README.md)**
>
> It gives you the reading order first, then points you to:
> - `SPEC.md` for architecture and boundaries
> - `robust-llm-wiki-CLAUDE.md` for operator policy and agent-team rules
> - `details/` for focused playbooks and rule pages

## What Problem This Solves

Once an LLM-Wiki becomes large enough, the main problem is no longer \"can the model write a page.\" The problem becomes:

1. can the wiki stay navigable,
2. can new sources be compiled without polluting old pages,
3. can query results be trusted enough to feed later maintenance,
4. can the agent runtime stop drifting as tool chains get longer.

This repository exists to answer those questions with reusable rules rather than one-off prompts.

## At A Glance

| Item | Value |
|---|---|
| Snapshot date | `2026-04-17` |
| Practice samples | Two anonymized production Wikis (A/B) |
| Markdown files | `3,093` |
| Markdown lines | `779,376` |
| Wikilinks (`[[...]]`) | `17,780` |

At this scale, the core challenge is sustaining maintenance quality over time, not just finishing one-time page creation.

## Why This Repository Exists

The motivation is straightforward: long-running operation of two real LLM-Wikis exposed the same classes of failure repeatedly, so the repair patterns were turned into reusable guidance. This repository publishes methods, rules, and validation structure, not the private contents of those Wikis:

1. Human-read and AI-read goals become mixed, so pages are either hard to read or hard to process.
2. Overuse of rigid outlines drifts away from the Wiki form.
3. Sources are appended at the tail while body-level wikilinks stay weak, creating isolated pages.
4. Rules and checks are not layered, so maintenance cost rises quickly with volume.

## Document-Centered Direction

The design direction is document-centered rather than tool-centered.

In practical terms, that means:

1. pages are the long-lived unit, not chat transcripts,
2. addresses, links, and logs matter as much as generated prose,
3. the system should improve its maintenance process, not only add more content.

This is also the historical direction that makes the project legible: closer to Engelbart's document-centered OHS/CODIAK line than to a prompt-only workflow. The durable unit is the addressable page and its links, not the transient tool session.

That is why this repository behaves more like a control-plane than a content repository.

## Control Plane vs Data Plane

This repository intentionally publishes the control-plane:

1. `research/` for evidence, scale, and recurring failure patterns,
2. `schema/` for architectural boundaries and operating rules,
3. runbooks for how agents should ingest, query, lint, hand off, and promote knowledge.

Downstream implementations are expected to provide the data-plane:

1. `raw/` source material,
2. `wiki/` maintained knowledge pages,
3. `index.md` and `log.md` as first-class operating files.

This split is deliberate. It keeps the framework reusable while letting each real Wiki keep its own private corpus.

## Core Idea

Robust focuses on maintaining stability in operations and governance.

1. Keep Karpathy kernel unchanged.
2. Use `Schema` to define hard boundaries and safe extension space.
3. Treat runtime safety as architecture, not as an afterthought.
4. Improve the maintenance method itself as the corpus grows.
5. Keep implementation choices decoupled from this framework repository.

## A Minimal Maintenance Cycle

The intended cycle is simple:

1. ingest one source into draft knowledge,
2. update the linked entity, concept, or synthesis pages it touches,
3. refresh `index.md` and append to `log.md`,
4. run lint in layers,
5. promote only after evidence and structure checks pass.

The repository does not ship one fixed implementation of that loop. It defines the contract that a downstream implementation should satisfy.

## Why This Is More Than Schema

The point of the framework is not \"more files.\" The point is to make long-term Wiki maintenance legible.

The repository already anchors that claim in three ways:

1. real operating scale from two anonymized production Wikis,
2. evidence and engineering notes in `research/`,
3. explicit runtime and promotion rules in `schema/`.

What it publishes is the control logic. What downstream Wikis provide is the live corpus.

## Repository Map

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

Purpose: preserve evidence, dataset scale, ecosystem observations, and problem summaries.

- Main doc (EN): [research/RESEARCH.md](./research/RESEARCH.md)
- Main doc (ZH): [research/RESEARCH.zh-CN.md](./research/RESEARCH.zh-CN.md)
- Survey snapshots: `research/snapshots/`
- Engineering notes (A/B anonymized): [research/2026-04-17-two-wikis-engineering-notes.md](./research/2026-04-17-two-wikis-engineering-notes.md)
- Ecosystem survey: [research/2026-04-17-llm-wiki-ecosystem-survey.md](./research/2026-04-17-llm-wiki-ecosystem-survey.md)

### 2) Schema

Purpose: define red lines, operating rules, extension boundaries, and governance requirements.

- Main spec (EN): [schema/SPEC.md](./schema/SPEC.md)
- Main spec (ZH): [schema/SPEC.zh-CN.md](./schema/SPEC.zh-CN.md)
- Schema start-here guide (EN): [schema/README.md](./schema/README.md)
- Schema start-here guide (ZH): [schema/README.zh-CN.md](./schema/README.zh-CN.md)
- Claude operating guidance: [schema/robust-llm-wiki-CLAUDE.md](./schema/robust-llm-wiki-CLAUDE.md)
- Detailed rules: [schema/details/](./schema/details)
- Open-source policy (EN): [schema/OPEN_SOURCE_POLICY.md](./schema/OPEN_SOURCE_POLICY.md)
- Open-source policy (ZH): [schema/OPEN_SOURCE_POLICY.zh-CN.md](./schema/OPEN_SOURCE_POLICY.zh-CN.md)

## Quick Start

1. Start with [quickstart/README.md](./quickstart/README.md).
2. Choose the path that matches you: adapt this framework into an existing LLM-Wiki, or generate a fresh `CLAUDE.md` / `AGENTS.md` / `GEMINI.md` from this repo's operating guide.
3. Then continue into [schema/README.md](./schema/README.md), [schema/SPEC.md](./schema/SPEC.md), and [schema/robust-llm-wiki-CLAUDE.md](./schema/robust-llm-wiki-CLAUDE.md).

## Karpathy Kernel (Do Not Drift)

1. It must be a Wiki.
2. It must have wikilinks/double-links.
3. It must keep the `ingest -> query -> lint` loop.
4. It must remain traceable, auditable, and rollback-friendly.

## Open-Source Licensing (Recommended)

1. Code and scripts: Apache-2.0
2. Research and standards docs: CC BY 4.0
3. Contribution process: DCO sign-off + third-party license declaration
4. Current repository license file: Apache-2.0 (`LICENSE`)

Detailed policy: [schema/OPEN_SOURCE_POLICY.md](./schema/OPEN_SOURCE_POLICY.md)

## Collaboration Docs

1. Contribution guide (EN): [CONTRIBUTING.md](./CONTRIBUTING.md)
2. Contribution guide (ZH): [CONTRIBUTING.zh-CN.md](./CONTRIBUTING.zh-CN.md)
3. Translation policy (EN): [I18N.md](./I18N.md)
4. Translation policy (ZH): [I18N.zh-CN.md](./I18N.zh-CN.md)

## Baseline Source

- Karpathy original gist: [https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f](https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f)
