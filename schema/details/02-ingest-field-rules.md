# Ingest Field Rules
[中文](./02-ingest-field-rules.zh-CN.md) | **English**

> Read this page when you need to decide whether a new field is ready to be written into the wiki at scale.

## 1. The Core Rule

A field is not ready just because it has a good name.

A field becomes usable only when the `raw -> wiki` path for that field is explicit. If the system does not know where the value comes from, how it is normalized, and when it is allowed to change, the field will create repair work later.

That is why this document treats field rules as something to define before scale, not after scale.

## 2. What Must Be Defined Before Adding A Field

Before a new field is written broadly, define all five of these:

1. `Source`: what kind of raw evidence this field is extracted from
2. `Normalization`: what shape the value must be converted into
3. `Default`: what to do when evidence is missing or incomplete
4. `Update strategy`: when to overwrite, when to preserve, and when to defer
5. `Conflict handling`: how to resolve inconsistent values across sources

If one of these remains vague, the field is not ready.

## 3. Why These Five Matter

These are not bureaucratic extras. They prevent common failure modes.

1. Source keeps the field traceable.
2. Normalization keeps same-type values comparable.
3. Default behavior prevents empty or guessed values from masquerading as facts.
4. Update strategy prevents fresh noise from overwriting older confirmed information.
5. Conflict handling prevents the same page from drifting into long-term contradiction.

## 4. Minimum Operational Requirements

Once the five items exist, the rule is still not done until it is operational.

At minimum:

1. the rule must be documented
2. the rule must be reviewable
3. the rule should be lint-checkable
4. rule changes should record effective date and impact scope

## 5. `draft -> stable` Gate

The most important write rule is simple: missing evidence means missing permission to stabilize.

1. Any new factual claim without `source_ids` stays in `draft`.
2. It does not go directly to `stable`.
3. Promotion from `draft` to `stable` should require:
   - traceable `source_ids`
   - one completed `read/grep` cross-check round
   - passing minimum `property + link` lint
4. `query` outputs should write back to `draft` by default, not overwrite `stable`.
5. If a `stable` page changes, keep both source trace and change trace.

## 6. How To Read This Rule In Practice

The point of the gate is not to slow the system down for its own sake.

The point is to keep unfinished synthesis from quietly becoming durable knowledge. In a long-running wiki, the real risk is not one weak page. The real risk is that a weak page gets cited back into later ingest and query cycles as if it were already settled.

## 7. Upstream Basis And Project Extension

This page is compatible with the source-grounded workflow shape found in Karpathy-style LLM wiki patterns, but the exact `draft/stable` gate is a project-level governance extension for this repository.

Useful upstream references:

1. Karpathy original gist: <https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f>
2. Karpathy gist raw: <https://gist.githubusercontent.com/karpathy/442a6bf555914893e9891c11519de94f/raw/ac46de1ad27f92b28ac95459c782c07f6b8c964a/llm-wiki.md>
3. Astro-Han/karpathy-llm-wiki: <https://github.com/Astro-Han/karpathy-llm-wiki>
4. xoai/sage-wiki: <https://github.com/xoai/sage-wiki>
