# File2Agent And Context Engineering (Detailed)
[中文](./05-file2agent-context-engineering.zh-CN.md) | **English**

## 0. Background

1. In the early stage, when we directly imported knowledge at large scale, we hit a recurring issue: “it looked processed, but much content was not fully read.”
2. Common triggers were: long files hitting read limits, shallow reads under concurrency, and main-context saturation during backfill.
3. These issues lead to missing entity/concept outputs and significantly higher repair costs later.

### 0.1 Data Anchors From Real Samples

1. Wiki A: `1748` Markdown files, with `165` over 10,000 characters (`9.4%`).
2. Wiki B: `1345` Markdown files, with `126` over 10,000 characters (`9.4%`).
3. Long files are normal, not exceptions: about `9.4%` of files exceed 10,000 characters.
4. `log.md` and `index.md` are high-risk hotspots: in both repositories, the longest files are concentrated in logs and index pages.
5. “File processed” does not mean “content fully read”: concurrency + read limits can easily miss later sections.
6. Single-page quality problems can spread through wikilinks: when adding new entity/concept relations, old errors can be carried into new pages via `read/grep`.
7. Engineering noise can leak into the knowledge area: samples showed suspected unexpanded variable directories (for example `$(...)` fragments).

### 0.2 Why Token Cost Keeps Rising Later (Simple Version)

From daily entity/concept growth in two real Wikis, there are clear bulk-growth days (for example, `707` new pages in one day).  
Even when later daily growth decreases, cumulative total still keeps growing.

Use this simple formula to view maintenance burden:

\[
Cost_t \approx \alpha \cdot \Delta N_t + \beta \cdot N_t + \gamma \cdot R_t
\]

1. \(\Delta N_t\): number of newly added entity/concept pages on day t (new-creation cost).
2. \(N_t\): cumulative entity/concept total up to day t (historical-volume cost).
3. \(R_t\): number of `grep/read` operations for relation-building and verification on day t (re-check cost).

Plain interpretation:

1. More new pages mean higher write cost on that day.
2. Larger cumulative volume means more old pages to link and recheck when creating one new page.
3. During backlinking or correction, agents frequently `grep/read` old content; old errors keep entering new pages, so re-check token usage keeps rising.

Related experience note (original methodology):

- `Vault B/wiki/synthesis/analysis-LLM-Wiki-Ingest-context-bottleneck-and-chunked-reading.md` (path anonymized)

## 1. File2Agent (Reference Ideas)

Core recommendation:  
When one `raw` file cannot be fully covered in one `read`, it is usually better to assign one subagent to own that file end-to-end.

Why:

1. Under concurrent processing, an agent may read each file only once; the latter part of long files is easy to miss.
2. A subagent has independent context and can usually read one file deeper and more completely.
3. After finishing the subtask, returning entity/concept candidates to the main agent for aggregation helps reduce main-context pressure.

## 2. Context Engineering (Reference Ideas)

The directions below are more engineering-oriented:

1. Main agent is better for orchestration; subagents are better for single-file deep reading.
2. Separate responsibilities of `grep` and `read`: locate first, then close-read.
3. Use chunked reading for long files to reduce first-half bias.
4. Keep output structured during backfill to avoid large raw passages occupying main context.
5. Ensure facts and links first, then optimize expression quality.

## 3. Engineering Recommendations (Optional)

This section is not a hard rule; it is a direction for future engineering:

1. Build an ingest manifest and processing states to track which files have been fully processed.
2. Keep stage-wise intermediates (for example extracts, candidate cards, conflict markers) for review and replay.
3. Define minimal observability metrics (for example file-read coverage, candidate output count, conflict count, broken links after backfill).
4. Treat “main-agent aggregation + subagent deep-read” as the default collaboration mode.
5. Add merge-back constraints for chunk processing: chunk files are only intermediate artifacts, not final `src`.
6. Maintain a `1 src -> 1 raw` mapping: even if one raw is processed in multiple chunks, it should be merged back to the original pre-slice raw view.
7. Avoid ingesting slide/chunk files as independent srcs; otherwise AI can easily misclassify intermediates as official sources.

## 4. Claude Hook Recommendations (Optional)

Hook has been split into a standalone document; refer directly to:

- `schema/details/06-claude-hook-guidelines.md`

In this file, keep only one conclusion:  
Hook should provide lightweight full-process guardrails, especially fast lint reminders “after every AI file write.”

## 5. Naming Recommendation

1. The current name `File2Agent` is usable (intuitive and aligned with current problems).
2. If future inputs expand to web/API, consider upgrading to `Source2Agent`.
3. Whichever naming is used, keep SPEC, details, and AGENT naming consistent.
