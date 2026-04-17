# Robust LLM-Wiki Research

[中文](./RESEARCH.zh-CN.md) | **English**

## Research Goal

This research supports a "better-practice" direction: keep Karpathy's LLM-Wiki kernel unchanged while making large-scale LLM-Wiki maintenance more stable over time.

## Baseline Sources

1. Karpathy original gist: https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f
2. GitHub/Gist ecosystem snapshots (see `research/snapshots/`)
3. Two long-running real LLM-Wiki practice samples (anonymized as A/B)

## Practice Sample Scale (Anonymized A/B)

As of 2026-04-17:

1. Wiki A (life / relationships / long-term matters)
- 1,748 Markdown files
- 238,293 lines of Markdown text
- 13,090 wikilinks
- 14,470,735 Markdown bytes

2. Wiki B (AI / technical learning / research / workflows)
- 1,345 Markdown files
- 541,083 lines of Markdown text
- 4,690 wikilinks
- 33,034,280 Markdown bytes

3. Combined
- 3,093 Markdown files
- 779,376 lines of Markdown text
- 17,780 wikilinks
- 47,505,015 Markdown bytes

Measurement method: `find` for `*.md` file count and bytes; `find -print0 | xargs -0 cat | wc -l` for line count; `rg -uu -o '\[\[[^]]+\]\]'` for wikilink count.

## Key Problems (From Real Operations)

1. Human-read and AI-read goals are mixed in one layer, so pages become either hard to read or hard to process.
2. Over-reliance on body templates (outlines) makes page style rigid and drifts away from Wiki form.
3. Sources are often appended at the end, while body wikilinks are weak, creating readable but non-navigable islands.
4. Lint depends on heavy manual `read/grep` checking, consuming both context and time.

## Core Research Conclusions

1. Preserving Karpathy's kernel is the prerequisite: Wiki form, wikilink network, and `ingest/query/lint` loop.
2. The best use of templates is constraining attributes, not forcing body outlines.
3. Pages should be layered for human-read and AI-read needs, not forced into one mode.
4. Lint should run in layers: Property lint -> Link lint -> Content lint (script implementation belongs to downstream projects).

## Hallucination Rates (Same-Benchmark Comparison)

Primary benchmark: Vectara Hallucination Leaderboard (last updated 2026-03-20; no newer public update as of 2026-04-17).

This repository does not restate vendor-by-vendor model tables or publish its own public ranking here.

If you want same-benchmark public data, go directly to the original sources:

1. Vectara Hallucination Leaderboard: https://github.com/vectara/hallucination-leaderboard
2. AA-Omniscience live page: https://artificialanalysis.ai/evaluations/omniscience
3. SimpleQA: https://arxiv.org/abs/2411.04368
4. AA-Omniscience: https://arxiv.org/abs/2511.13029

Additional notes:
1. Different leaderboards use different definitions, so absolute values should not be compared directly.
2. A newer version under the same benchmark is not guaranteed to be lower-hallucination.
3. For any comparison, consult the original release pages and methodology directly.

## Benchmark-Scope Limits (Must Read Together)

1. Different leaderboards use different definitions (SimpleQA, AA-Omniscience, Vectara cannot be compared by absolute value directly).
2. Relative model selection is valid within the same leaderboard.
3. Task shape differences (summarization/QA/tool use) significantly affect hallucination metrics.
4. Evaluator differences and model hot updates can introduce drift.

## Simplified Hallucination Propagation Model (Including Multiple Read Calls)

\[
H_{t+1}=\alpha H_t + \lambda k_t h_m - \beta u_t
\]

1. \(H_t\): hallucination volume at round \(t\) (polluted entity scale)
2. \(k_t\): number of agent `read/grep` tool calls in this round
3. \(h_m\): hallucination rate of the selected model on this path
4. \(\lambda\): coefficient from each read into newly written claims
5. \(u_t\): number of corrected entities in this round
6. \(\alpha\), \(\beta\): propagation and repair efficiency

If
\[
\alpha H_t + \lambda k_t h_m \ge \beta u_t
\]
then net hallucination will not decrease.

## Practical Repair Cases (User-Provided, Anonymized)

Entity pages fixed in this round (summary):

1. Entity-A: corrected media source naming and added model/method details.
2. Entity-B: fixed key date conflicts.
3. Entity-C: added alias and identity information.
4. Entity-D: split same-name confusion and fixed incorrect links.
5. Entity-E: added competition and work-list details.
6. Entity-F: corrected participant-count facts and synchronized subpages.

Conclusion: even after focused fixes on multiple entities, multiple `read/grep` and lint rounds are still often required before the system stabilizes.

## Model-Selection Suggestions For LLM-Wiki

1. Prioritize lower-hallucination models on the ingest compilation path.
2. Run secondary verification for high-risk query conclusions.
3. Maintain layered lint plus rollback strategy.
4. Rich expression can be traded off; high-hallucination contamination cannot.

## Traceable References

1. Karpathy gist: https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f
2. Vectara leaderboard: https://github.com/vectara/hallucination-leaderboard
3. AA-Omniscience live page: https://artificialanalysis.ai/evaluations/omniscience
4. SimpleQA: https://arxiv.org/abs/2411.04368
5. AA-Omniscience: https://arxiv.org/abs/2511.13029
6. Ecosystem snapshots and raw data: `research/snapshots/`
7. Engineering notes from two real repositories: [`research/2026-04-17-two-wikis-engineering-notes.md`](./2026-04-17-two-wikis-engineering-notes.md)

Note:
1. These references are provided as research entry points and methodology sources only.
2. They should not be read as this repository making a public judgment about any model vendor, family, or product capability.
