# Robust LLM-Wiki Specification (SPEC)
[中文](./SPEC.zh-CN.md) | **English**

## 0. How To Use This SPEC

1. This document contains only shared consensus and boundaries, not project script implementations.
2. Detailed rules are in `schema/details/`, and evidence is in `research/`.
3. At the current stage, we focus on clear rules and boundaries, and keep implementation open.

## 1. Why This Project Exists

1. We have been maintaining two real, long-running LLM-Wikis (anonymized as A/B).
2. In real operations, we repeatedly see the same issues: inconsistent structure, broken links, and hallucinations.
3. The most critical issue is hallucination: even after fixing 7 entities once, we still needed multiple rounds of grep/read to stabilize.
4. This shows that LLM-Wiki must prioritize low-hallucination operation.
5. If older content contains hallucinations, a new ingest/query round may keep citing it, and errors will be inherited and spread.

## 2. Precise Stats From Two Practice Samples (Anonymized A/B)

Statistics date: 2026-04-17

### 2.1 Full-Repository Markdown Stats

| Sample | Scope (Anonymized) | Markdown Files | Markdown Lines | `[[...]]` Wikilinks | Markdown Bytes |
|---|---|---:|---:|---:|---:|
| Wiki A | Life / relationships / long-term matters | 1,748 | 238,293 | 13,090 | 14,470,735 |
| Wiki B | AI / technical learning / research / workflow | 1,345 | 541,083 | 4,690 | 33,034,280 |
| Total | - | 3,093 | 779,376 | 17,780 | 47,505,015 |

### 2.2 `wiki/` Directory Page-Structure Stats

| Sample | Total Wiki Pages | entities | concepts | sources | synthesis | topics |
|---|---:|---:|---:|---:|---:|---:|
| Wiki A | 886 | 491 | 309 | 74 | 6 | 0 |
| Wiki B | 365 | 109 | 143 | 106 | 5 | 0 |
| Total | 1,251 | 600 | 452 | 180 | 11 | 0 |

Measurement method: `find` for `*.md` files and bytes; `cat|wc -l` for Markdown lines; `rg -o '\[\[[^]]+\]\]'` for wikilink count.

### 2.3 Engineering Constraints From Real Samples (Now Incorporated Into Schema)

1. Long files are normal, not exceptions: in both A/B samples, Markdown files over 10,000 characters are about `9.4%`.
2. `log.md` and `index.md` are high-risk hotspots: the longest files in both repositories are concentrated in logs and index pages.
3. “File processed” does not equal “content fully read”: concurrency + read limits can easily miss later sections.
4. Single-page quality issues can spread through wikilinks: when new entity/concept relations are added, old errors can be brought into new pages via `read/grep`.
5. Engineering noise can leak into the knowledge area: suspected unexpanded variable paths (for example `$(...)`) should be included in lint observation.

### 2.4 Daily entity/concept Growth Trend (Why Maintenance Gets Harder Later)

1. Within the same observation window across two real samples, there was a peak day with `707` new pages (entity+concept).
2. Even when daily additions later decline, cumulative volume continues to grow (window-end cumulative count: `1052` pages).
3. This means maintenance cost does not come only from “new pages”; it also comes from “querying existing pages + backlinking re-check + conflict repair”.
4. Simplified expression: `Maintenance Cost ≈ New-Creation Cost + Existing-Volume Cost + grep/read Re-check Cost`.
5. For the detailed trend table, see `research/2026-04-17-two-wikis-engineering-notes.md`.

## 3. Karpathy Core Red Lines (Non-Negotiable)

1. It must be a Wiki (knowledge is accumulated as maintainable pages, not just chat logs).
2. It must have wikilinks/double links (navigable, traceable, reusable).
3. It must have a maintenance loop (`ingest -> query -> lint`).
4. Future extensions must not break the three rules above.

## 4. Division Of Spec Work (Refine First, Implement Later)

1. `details/01-attribute-proposal.md`: define the minimum attribute contract first, to reduce write-time disorder.
2. `details/02-ingest-field-rules.md`: define ingestion rules before adding new fields, to avoid later rework (including the `draft/stable` write-gate recommendation).
3. `details/03-lint-playbook.md`: run lint in sequence, to reduce repeated checking (including path and shell safety checks).
4. `details/04-hallucination-control.md`: low-hallucination governance based on real maintenance scenarios.
5. `details/05-file2agent-context-engineering.md`: division-of-labor ideas for long-file ingest and context-engineering recommendations.
6. `details/06-claude-hook-guidelines.md`: lightweight full-process guardrails with Claude Hook (including fast post-write lint).
7. `details/07-turbo-model-value.md`: task-division recommendations for Turbo/low-parameter models in wikilink and format maintenance.
8. implementation choices remain project-dependent and are not fixed in this repository.

## 5. Extension Boundaries (Flexible, But Bounded)

1. You may extend beyond `entity` and `concept` with categories such as `topic`.
2. You may extend fields by project needs (for example `status`, `topic`), but define ingest rules first.
3. Templates may constrain attributes, but should not rigidly constrain body outlines.

## 6. Low-Hallucination Priority (SPEC Conclusion)

1. Model selection should prioritize same-benchmark hallucination rates, not “newer/older” impressions.
2. On the same benchmark, a newer version is not guaranteed to have a lower hallucination rate.
3. After a fix, continue read/grep/lint until conflicts and broken links are reduced to a controllable level.
4. For detailed data, formulas, and execution suggestions, see `details/04-hallucination-control.md`.
5. In the maintenance phase, you can prioritize Turbo/low-parameter models for wikilink and format consistency tasks (see `details/07-turbo-model-value.md`).

## 7. File Layering

1. `schema/SPEC.md`: top-level specification (this file in English).
2. `schema/details/*.md`: detailed rules and execution guidance (English primary).
3. `research/RESEARCH.md`: evidence, samples, evaluations, and cases (English primary).
4. `*.zh-CN.md`: Chinese mirrors for multilingual access.
