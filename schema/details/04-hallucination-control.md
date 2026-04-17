# Hallucination Control
[中文](./04-hallucination-control.zh-CN.md) | **English**

## The Short Version

In a robust LLM-Wiki, hallucination control is not a late-stage cleanup task. It is an upstream design choice.

If the first ingest pass introduces invented names, dates, relationships, or citations, later `query`, `backfill`, and `lint` rounds tend to reuse those errors. Once that happens, the repository does not merely contain bad facts. It starts to build new structure on top of bad facts.

That is why this document frames the rule in a simple way:

1. Prefer lower-hallucination models for ingest and high-risk factual synthesis.
2. Treat benchmark numbers as routing signals, not marketing slogans.
3. Add extra verification whenever a page is likely to become a structural reference for other pages.

## Why This Matters

LLM-Wiki maintenance has a compounding failure mode.

1. One page contains a wrong statement.
2. Later pages `read` or `grep` that page while building relations.
3. The wrong statement is copied, paraphrased, or normalized into multiple new pages.
4. The repo now has several consistent-looking pages that all repeat the same mistake.

At that point, the problem is no longer "one hallucination." The problem is "a local falsehood that has become part of the graph."

This is why hallucination control belongs near the start of the workflow, alongside source handling and file ownership, rather than at the very end.

## What This Document Is For

Use this page when deciding:

1. which model should handle first-pass ingest,
2. which pages require secondary verification,
3. when a change should remain `draft` instead of returning to `stable`.

This page is not trying to prove that one vendor is universally best. It only defines a practical operating rule for this repository: when factual error propagation is expensive, lower hallucination risk is strategically valuable.

## How To Read Public Hallucination Benchmarks

Public benchmark numbers can help, but only if they are read carefully.

1. Compare entries only within the same benchmark.
2. Treat newer model versions as new candidates, not automatic upgrades.
3. Read the benchmark as one input into routing decisions, not as a complete production guarantee.
4. Watch for task mismatch: a summarization benchmark is useful, but it is not identical to full wiki authoring.

In other words, a benchmark should answer "which model is less likely to invent under this measurement," not "which model can replace judgment."

## A Practical Rule Of Thumb

The cost of hallucination is not linear.

When a maintenance round creates many new pages, links many old pages, or rewrites summary layers, one wrong factual seed can reappear many times. A simple way to think about it is:

\[
\Delta H \approx p \times (n_{new\ facts}) + c \times n_{reuse}
\]

Where:

1. \(p\) is the model's tendency to generate unsupported claims under the task,
2. \(n_{new\ facts}\) is the number of new factual units introduced,
3. \(n_{reuse}\) is the amount of old content reused during relation-building,
4. \(c\) is the average cost of inheriting one prior mistake into a new page.

The equation is only a mental model, but it captures the operational truth: a noisy first pass makes the whole graph more expensive to repair later.

## Recommended Operating Policy

### 1. Use lower-hallucination models for ingest

If a task is generating page skeletons, summarizing sources into canonical facts, or creating pages likely to be linked everywhere else, the safer default is the lower-hallucination option available to the team.

### 2. Escalate verification for high-leverage pages

Not every page carries the same risk. Add a secondary check when a page is:

1. an index, glossary, chronology, or synthesis page,
2. a frequently linked entity or concept,
3. a page that resolves identity, naming, or date conflicts,
4. a page that will be used as a later aggregation source.

### 3. Keep risky pages in `draft`

If source grounding is incomplete, cross-page consistency is unclear, or a recent rewrite touched many links, keep the page in `draft` until another pass confirms the facts.

### 4. Lint after factual repair, not only after formatting changes

After hallucination repair, run structure and link checks again. Factual cleanup often changes aliases, backlinks, dates, or page targets, which means it can silently create new structural issues.

## What Good Practice Looks Like

Good hallucination control in this repo usually looks like this:

1. the model used for ingest is chosen for factual restraint, not just fluency,
2. pages with higher blast radius receive a second verification pass,
3. pages with unresolved contradictions stay visibly provisional,
4. the team treats old wiki content as fallible unless it has already been stabilized.

That last point matters. A wiki page is not trustworthy merely because it already exists. In a living LLM-Wiki, existing content is part of the evidence chain, but it is also a potential carrier of prior mistakes.

## Data Sources

The benchmark references that informed this guidance are listed below for reproducibility:

1. Vectara Hallucination Leaderboard: <https://github.com/vectara/hallucination-leaderboard>
2. Leaderboard README: <https://raw.githubusercontent.com/vectara/hallucination-leaderboard/main/README.md>
3. Methodology section: <https://github.com/vectara/hallucination-leaderboard#methodology>
4. Methodology overview: <https://www.vectara.com/blog/introducing-the-next-generation-of-vectaras-hallucination-leaderboard>

## See Also

1. [SPEC](../SPEC.md)
2. [robust-llm-wiki-CLAUDE.md](../robust-llm-wiki-CLAUDE.md)
3. [07-turbo-model-value.md](./07-turbo-model-value.md)
