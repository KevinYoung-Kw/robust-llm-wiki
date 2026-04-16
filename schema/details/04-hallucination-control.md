# Hallucination Control (Detailed)
[中文](./04-hallucination-control.zh-CN.md) | **English**

## 0. Background (Why This Matters)

1. At the beginning, we used models with higher hallucination rates for compilation and maintenance, and factual deviations appeared in the Wiki.
2. Later, we had to switch to lower-hallucination models and run multiple lint/grep/read rounds for repair.
3. In one repair cycle, we fixed 7 entities, but still needed multiple grep/read rounds to stabilize.
4. This shows that LLM-Wiki must follow a “low-hallucination-first” principle.
5. The reason is direct: if older content contains hallucinations, new ingest/query rounds may keep citing it, and errors will be inherited and spread.

So this document is not for arguing “which model is stronger.”  
It answers: in continuous Wiki maintenance, how to push hallucination risk lower.

## 1. Practical Cases (Anonymized)

| Wiki Page | Status | Key Findings (Anonymized) |
|---|---|---|
| Entity A | Fixed | Corrected media source naming; added method/model description |
| Entity B | Fixed | Resolved key date conflicts |
| Entity C | Supplemented | Added alias and identity information |
| Entity D | Fixed | Split same-name confusion; corrected wrong jump links |
| Entity E | Supplemented | Added competition result and work list |
| Entity F | Fixed | Corrected participant-count statement; synchronized subpage information |

## 2. Same-Benchmark Model Comparison (Primary Benchmark: Vectara)

Main leaderboard page update date: 2026-03-20 (no newer public update as of 2026-04-17).

| Vendor/Model Entry | Hallucination Rate | Notes |
|---|---:|---|
| deepseek-ai/DeepSeek-V3.2-Exp | 5.3% | Relatively low under the same benchmark |
| openai/gpt-5.4-nano-2026-03-17 | 3.1% | One of the lower entries under the same benchmark |
| openai/gpt-5.4-mini-2026-03-17 | 5.5% | Relatively low under the same benchmark |
| MiniMaxAI/minimax-m2p5 | 9.1% | Better than `minimax-m2p1(11.8%)` on the same leaderboard |
| zai-org/GLM-4.7-flash | 9.3% | Newer than `GLM-4.5-AIR-FP8`, same value under this benchmark |
| zai-org/glm-5 | 10.1% | Newer version, but higher under this benchmark |
| anthropic/claude-haiku-4-5-20251001 | 9.8% | Current visible Haiku entry |
| anthropic/claude-sonnet-4-6 | 10.6% | Newer than `claude-sonnet-4(10.3%)`, but higher |
| moonshotai/Kimi-K2.5 | 14.2% | Current visible Kimi primary entry |
| moonshotai/Kimi-K2-Instruct-0905 | 17.9% | In-family comparison entry |

Additional notes:
1. Codex appears on some evaluation pages, but static pages do not provide one unified, citable hallucination-rate number.
2. A newer version does not necessarily mean a lower hallucination rate. Selection should use same-benchmark measured values.

## 3. How To Read These Scores

1. Only results from the same leaderboard can be compared directly.
2. Different leaderboards (SimpleQA, AA-Omniscience, Vectara) use different definitions, so absolute values are not directly comparable.
3. This is mainly a summarization-task benchmark and does not equal your full production-task profile.
4. When reading hallucination rate, also check answer rate to avoid single-metric misjudgment.

## 4. A Simple Judgment: If Initial Compilation Is Not Clean, It Becomes Hard To Lower Later

Use a simple formula first:

\[
\Delta H \approx p \times (n_{entity}+n_{concept}) + c \times n_{read/grep}
\]

Where:

1. \(p\): hallucination rate of the current model
2. \(n_{entity}\): number of newly generated entities in this round
3. \(n_{concept}\): number of newly generated concepts in this round
4. \(n_{read/grep}\): number of read/grep operations for relation-building in this round
5. \(c\): average impact factor of each read/grep operation carrying old errors into new pages
6. \(\Delta H\): newly added hallucination amount in this round

This means:  
If one compilation round produces many entities/concepts and the model hallucination rate is high, newly added hallucinations increase significantly.  
When adding new relations later, agents repeatedly read/grep old pages. If old pages already contain hallucinations, those errors keep entering new pages.  
So even after fixes begin (for example, 7 entities were already fixed in this cycle), multiple grep/read/lint rounds are often still required to push down the overall hallucination level.

## 5. Execution Recommendations (Low-Hallucination First)

1. For ingest compilation, prioritize lower-hallucination models under the same benchmark.
2. For critical query conclusions, perform secondary verification, focusing on sources and links.
3. After changes on high-risk pages, add one extra read/grep validation round before moving back to stable.
4. After each repair round, run lint and keep snapshots for comparison.

## 6. Data Sources (Reproducible)

1. Main leaderboard (GitHub): https://github.com/vectara/hallucination-leaderboard
2. Leaderboard raw text (README): https://raw.githubusercontent.com/vectara/hallucination-leaderboard/main/README.md
3. Methodology (README#Methodology): https://github.com/vectara/hallucination-leaderboard#methodology
4. Vectara methodology blog: https://www.vectara.com/blog/introducing-the-next-generation-of-vectaras-hallucination-leaderboard