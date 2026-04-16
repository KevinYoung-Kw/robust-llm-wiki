# Engineering Notes From Two Real LLM-Wikis (Anonymized)

[中文](./2026-04-17-two-wikis-engineering-notes.zh-CN.md) | **English**

Updated: 2026-04-17

## 1. Sample Scope

Two long-running LLM-Wikis (anonymized as A/B):

1. Wiki A: life / relationships / long-term matters
2. Wiki B: AI / technical learning / research / workflows

## 2. Key Data (Reproducible)

### 2.1 Full-Repository Scale

1. Wiki A: `1748` Markdown files, with `165` over 10,000 characters (`9.4%`)
2. Wiki B: `1345` Markdown files, with `126` over 10,000 characters (`9.4%`)

### 2.2 Type Distribution Under `wiki/`

1. A (`wiki/`): entities `491`, concepts `309`, sources `74`, synthesis `6`
2. B (`wiki/`): entities `109`, concepts `143`, sources `106`, synthesis `5`

### 2.3 Raw vs Wiki Structure Ratio (Approx.)

1. A: raw `73` vs wiki `886` (about `1:12.1`)
2. B: raw `116` vs wiki `365` (about `1:3.1`)

### 2.4 Structural Health Signals

1. Frontmatter coverage under `wiki/`: A `886/886`, B `365/365`
2. Pages with at least one wikilink under `wiki/`:
- A: `857/886` (about `96.7%`)
- B: `365/365` (`100%`)
3. A has about `29` pages without wikilinks, mainly short concept/entity pages

### 2.5 Daily Entity/Concept Growth Trend (Creation-Time Based, Approx.)

Notes:

1. Uses file birth time as the metric to estimate daily additions.
2. Scope includes only `wiki/entities/*.md` and `wiki/concepts/*.md`.

| Date | A_entity | A_concept | B_entity | B_concept | Daily New Total | Cumulative Total |
|---|---:|---:|---:|---:|---:|---:|
| 2026-04-11 | 0 | 0 | 0 | 1 | 1 | 1 |
| 2026-04-13 | 0 | 0 | 25 | 2 | 27 | 28 |
| 2026-04-14 | 433 | 213 | 20 | 41 | 707 | 735 |
| 2026-04-15 | 51 | 41 | 3 | 1 | 96 | 831 |
| 2026-04-16 | 7 | 55 | 61 | 95 | 218 | 1049 |
| 2026-04-17 | 0 | 0 | 0 | 3 | 3 | 1052 |

Trend interpretation (engineering view):

1. Peak day (`2026-04-14`) added `707` pages, showing that batch ingest can instantly amplify read/write and validation load.
2. Even when daily additions drop later, cumulative volume keeps rising (to `1052`), so query and repair cost will not naturally go down.
3. This directly explains why token usage is very high: cost is not only on new pages, but also on linking to existing pages and conflict re-checks.

### 2.6 Token Usage Snapshot Across Two Wiki Projects (Local Traceable, Merged Scope)

Notes:

1. Project mapping follows the current agreement:
- `karpathy-llm-wiki` is merged into Wiki A (anonymized domain).
- `robust-llm-wiki` is merged into Wiki B (anonymized domain).
2. This is a traceable conservative number, not a full billing number.
3. Snapshot date: 2026-04-17.

| Project (Anonymized) | Codex Local Total | Claude Local Total | Combined |
|---|---:|---:|---:|
| Wiki A (life / relationships / long-term matters) | 119,130,370 | 283,792,320 | 402,922,690 |
| Wiki B (AI / technical learning / research / workflows) | 52,420,609 | 17,644,374 | 70,064,983 |
| Total | 171,550,979 | 301,436,694 | 472,987,673 |

Why we say "real token usage is likely higher (and this is still low)":

1. Some maintenance sessions strongly related to both Wikis may have happened in other working directories and are not included in this strict two-project scope.
2. Usage fields are not fully aligned across toolchains, so some historical sessions may not provide directly summable usage numbers.
3. Ongoing `grep/read`, repeated re-check loops, and concurrent subagents significantly increase context cost, and these costs are not always replayable under one single accounting method.

## 3. Engineering Conclusions Extracted From Real Repositories

1. Long files are normal, not exceptions: about `9.4%` of files exceed 10,000 characters, which directly amplifies context truncation risk.
2. `log.md` and `index.md` are high-risk hotspots: the longest files in both repositories are concentrated in logs and index pages.
3. "File was processed" does not mean "content was fully read": concurrency plus read limits can miss trailing sections.
4. Single-page quality issues spread through wikilinks: when new entity/concept relations are added, old errors can be brought into new pages via `read/grep`.
5. Engineering noise can leak into the knowledge area: samples include suspected unexpanded variable paths (for example, `$(...)` fragments).

## 4. Direct Implications For Robust LLM-Wiki

1. The File2Agent proposal is necessary: long files should default to single-file deep-read ownership.
2. Hook usage should cover the full flow: pre-read, pre-write, post-write, post-batch, and post-merge should all have lightweight checks.
3. Schema should come before implementation: clarify boundaries first, then decide workflow realization.
4. Keep wording simple: explain "why this matters" before "how to apply it."

## 5. Mapping To This Repository's Specification

1. Low-hallucination governance: [`schema/details/04-hallucination-control.md`](../schema/details/04-hallucination-control.md)
2. File2Agent and context engineering: [`schema/details/05-file2agent-context-engineering.md`](../schema/details/05-file2agent-context-engineering.md)
3. Hook full-flow proposal: [`schema/details/06-claude-hook-guidelines.md`](../schema/details/06-claude-hook-guidelines.md)
4. Turbo-model maintenance task split: [`schema/details/07-turbo-model-value.md`](../schema/details/07-turbo-model-value.md)
