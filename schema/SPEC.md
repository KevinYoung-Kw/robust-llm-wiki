# Robust LLM-Wiki Specification (SPEC)
[中文](./SPEC.zh-CN.md) | **English**

> Status: Stable architecture/reference
> Version: `v0.1.1`
> Scope: This file defines what Robust LLM-Wiki is and what it must preserve. It does not define operator workflow details or tool-specific runbooks.

## 1. Why Robust LLM-Wiki Exists

A long-running LLM knowledge base does not usually fail in one dramatic moment. It fails slowly.

Structure drifts. Links decay. Old hallucinations become new citations. A page that looked "good enough" last week quietly becomes a weak dependency for the next ingest, the next query, and the next synthesis pass. Over time, the problem is no longer one wrong answer. The problem is a knowledge system that cannot reliably maintain itself.

Robust LLM-Wiki exists to address that system problem. It treats knowledge maintenance as an architectural problem first: how to keep a wiki navigable, source-grounded, and repairable over repeated AI-assisted update cycles.

## 2. The System Model

The model is intentionally simple. It has three layers and one maintenance loop.

### 2.1 Layers

- `raw/` is the evidence layer. It stores source material and remains immutable.
- `wiki/` is the maintained knowledge layer. It turns source material into linked, reusable pages.
- `schema/` is the governance layer. It defines contracts, boundaries, and quality expectations for the wiki.

These layers should stay distinct. Raw evidence is not rewritten as knowledge. Wiki pages are not treated as ungoverned scratchpads. Schema is not a dumping ground for scripts or temporary implementation choices.

### 2.2 Maintenance Loop

- `ingest` reads source material and updates knowledge pages.
- `query` uses the wiki as working memory for retrieval and synthesis.
- `lint` checks whether the structure remains healthy over time.

This loop is part of the system definition, not an implementation detail. Specific scripts, hooks, or models may change. The loop itself should remain explicit.

## 3. Karpathy Kernel

Robust LLM-Wiki preserves three non-negotiable properties:

1. It must remain a wiki: knowledge accumulates as maintainable pages, not only as chat transcripts or logs.
2. It must preserve a wikilink network: key entities and concepts should remain navigable, referenceable, and reusable through `[[wikilinks]]`.
3. It must preserve the maintenance loop: `ingest -> query -> lint`.

Any extension is valid only if these three properties remain intact.

## 4. Minimum Structural Contract

The wiki layer may evolve, but production knowledge still needs a stable minimum contract.

A stable page should have YAML frontmatter with at least:

- `id`
- `title`
- `type`
- `source_ids`
- `last_reviewed`
- `status`

The purpose of this contract is not aesthetic consistency. It is to make pages traceable, queryable, and reviewable at scale.

The repository also distinguishes between `draft` and `stable` knowledge:

- `draft` is for incomplete, provisional, or unresolved material.
- `stable` is for material that is source-grounded and ready to be depended on by future maintenance.

This document defines that architectural distinction. Exact promotion gates and verification workflow belong in [robust-llm-wiki-CLAUDE.md](./robust-llm-wiki-CLAUDE.md) and `schema/details/`.

## 5. Low-Hallucination Priority

Low hallucination is not a nice-to-have optimization. It is a first-order design goal.

Once hallucinated content enters a long-running wiki, it can be read back by later ingest and query steps, then reappear as if it were grounded knowledge. That is why Robust LLM-Wiki values traceability and repairability over raw generation speed or novelty.

At the architectural level, this means:

- source grounding matters more than fluent prose
- maintainability matters more than one-pass completeness
- model choice should be judged by comparable evidence, not by "newer must be better"

## 6. Extension Boundaries

Robust LLM-Wiki is intentionally extensible, but not unconstrained.

- Projects may add page types beyond `entity` and `concept`.
- Projects may add fields when those fields have clear ingest-time meaning.
- Templates may guide metadata and structure, but should not rigidly over-specify page bodies.

Extensions remain inside the spec only when all of the following stay true:

1. the Karpathy kernel is preserved
2. the `raw -> wiki -> schema` layering remains clear
3. new fields or types have defined contracts before they are treated as stable structure

In other words: flexibility is welcome, but schema growth should reduce future ambiguity, not create it.

## 7. Distilled Signals From Practice

This spec is grounded in real maintenance work on long-running LLM wikis, but the top-level lesson is more important than the raw tables.

Across anonymized practice samples, the same signals keep appearing:

- long files are normal, not exceptional
- `index.md` and `log.md` tend to become operational hotspots
- "processed" does not necessarily mean "fully read"
- one low-quality page can spread errors through wikilinks and repeated retrieval
- maintenance cost grows with existing volume, not only with new page creation

Detailed measurements and case evidence belong in `research/`, especially `research/2026-04-17-two-wikis-engineering-notes.md`.

## 8. What This SPEC Does Not Standardize

This file is deliberately not a runbook.

It does not standardize:

- specific scripts, CLIs, or CI wiring
- exact lint thresholds or hook mechanics
- model vendors or version choices
- team-specific operational ownership beyond the architectural boundary

Those choices remain project-dependent as long as they preserve the kernel, the layering, and the low-hallucination priority described here.

## 9. Reading Map

Use the repository docs in this order:

1. `schema/SPEC.md`: architecture, boundaries, and shared reference truths
2. `schema/robust-llm-wiki-CLAUDE.md`: operator policy and execution safety rules
3. `schema/details/*.md`: focused rules and implementation-level guidance
4. `research/*`: evidence, benchmarks, and case notes

`SPEC` should explain the system. `CLAUDE` should explain how to run it safely.
