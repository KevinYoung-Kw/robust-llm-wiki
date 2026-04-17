# Robust LLM-Wiki

[中文](./README.zh-CN.md) | **English**

[![Latest Release](https://img.shields.io/github/v/release/KevinYoung-Kw/robust-llm-wiki?label=release)](https://github.com/KevinYoung-Kw/robust-llm-wiki/releases)
[![GitHub Stars](https://img.shields.io/github/stars/KevinYoung-Kw/robust-llm-wiki?style=flat)](https://github.com/KevinYoung-Kw/robust-llm-wiki/stargazers)
[![License](https://img.shields.io/github/license/KevinYoung-Kw/robust-llm-wiki)](./LICENSE)
[![Docs](https://img.shields.io/badge/docs-bilingual-2f6feb)](./schema/README.md)

![Robust LLM-Wiki Banner](./assets/github-banner-en.webp)

Robust LLM-Wiki is an open-source framework for long-term, low-drift Wiki maintenance with AI.

It is not a new content Wiki repository. It distills real maintenance experience into reusable methods:

1. `Research` (evidence and recurring problems)
2. `Schema` (non-negotiable boundaries and operating rules)

> [!IMPORTANT]
> **Read This First**
> If you want the best entry into the schema docs, start with:
> **[`schema/README.md`](./schema/README.md)**
>
> It gives you the reading order first, then points you to:
> - `SPEC.md` for architecture and boundaries
> - `robust-llm-wiki-CLAUDE.md` for operator policy and agent-team rules
> - `details/` for focused playbooks and rule pages

## At A Glance

| Item | Value |
|---|---|
| Snapshot date | `2026-04-17` |
| Practice samples | Two anonymized production Wikis (A/B) |
| Markdown files | `3,093` |
| Markdown lines | `779,376` |
| Wikilinks (`[[...]]`) | `17,780` |

At this scale, the hard part is not writing pages. The hard part is keeping quality stable over time.

## Why This Project Exists

We observed recurring failure patterns during long-term maintenance of two real LLM-Wikis:

1. Human-read and AI-read goals become mixed, so pages are either hard to read or hard to process.
2. Overuse of rigid outlines drifts away from the Wiki form.
3. Sources are appended at the tail while body-level wikilinks stay weak, creating isolated pages.
4. Rules and checks are not layered, so maintenance cost rises quickly with volume.

## Core Idea

Robust does not mean “more features.” It means “more stable maintenance mechanisms.”

1. Keep Karpathy kernel unchanged.
2. Use `Schema` to define hard boundaries and safe extension space.
3. Keep implementation choices decoupled from this framework repository.

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
