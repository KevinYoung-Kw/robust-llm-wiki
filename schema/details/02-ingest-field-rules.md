# Ingest Field Operational Rules (Detailed)
[中文](./02-ingest-field-rules.zh-CN.md) | **English**

## Background

1. A field is useful not because of its name, but because `raw -> wiki` operational rules are explicit.
2. In real maintenance, we observed that “field first, rule later” creates large rework.
3. This document shifts field rules left to reduce later repair cost.

## Why This Must Be Done First

1. Without ingest rules, fields with the same name can mean different things on different pages.
2. When rules are unclear, AI writing becomes inconsistent, and later debugging needs multiple grep/read rounds.
3. At larger page scale, field conflicts keep reappearing in ingest/query.

## Must Be Defined Before Adding Any New Field

1. Source: what type of raw information this field is extracted from.
2. Normalization: what format this value must be unified to.
3. Default: what to fill (or not fill) when evidence is missing.
4. Update strategy: when to update and when to keep old values.
5. Conflict handling: what rule decides among inconsistent multi-source values.

## Why These Five Items Must Be Defined

1. Source definition: makes fields traceable.
2. Normalization definition: makes same-type values comparable and searchable.
3. Default definition: prevents nulls from being treated as true values.
4. Update strategy definition: prevents new noise from overwriting confirmed information.
5. Conflict-handling definition: avoids long-term self-contradictions for one entity.

## Minimum Operational Requirements

1. Rules must be documented and reviewable.
2. Rules must be checkable by lint.
3. Rule updates must record effective date and impact scope.

## `draft/stable` Write Gate (Minimum Recommendation)

1. Any new factual claim without `source_ids` can only be written to `draft`, never directly to `stable`.
2. Promotion from `draft` to `stable` should meet all three:
   - traceable `source_ids`
   - one `read/grep` cross-check round completed
   - current `property + link` minimum lint passed
3. `query` outputs should default to write-back into `draft`, not directly overwrite `stable`.
4. If a `stable` page must be changed, source trace and change trace should be kept (for example, change summary or log entry).

## Source Basis (External Gist/Repo + Project Inference)

Direct external sources:

1. Karpathy original gist: <https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f>
2. Karpathy gist raw: <https://gist.githubusercontent.com/karpathy/442a6bf555914893e9891c11519de94f/raw/ac46de1ad27f92b28ac95459c782c07f6b8c964a/llm-wiki.md>
3. Astro-Han/karpathy-llm-wiki (repo): <https://github.com/Astro-Han/karpathy-llm-wiki>
4. xoai/sage-wiki (repo): <https://github.com/xoai/sage-wiki>

Inference note:

1. The `draft/stable` gate in this document is a governance extension for this project. It is derived from the source-grounded workflow in the gist/repo pattern and our survey findings, not a verbatim sentence from a single upstream source.
