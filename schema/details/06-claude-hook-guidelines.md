# Claude Hook Recommendations (Detailed)
[中文](./06-claude-hook-guidelines.zh-CN.md) | **English**

## 0. What Problem This Document Solves

1. In LLM-Wiki maintenance, many errors are not caused by “not knowing what to do,” but by “not checking in time after doing it.”
2. If all checks are postponed to the end, rework cost keeps increasing.
3. Hook adds baseline checks earlier in the workflow, reducing omissions and rework.

## 1. Core Positioning

Hook is a lightweight full-process guardrail, not a feature tied to only one stage.  
It can support multiple stages: reading, writing, backfilling, lint, and more.

## 2. Suggested Trigger Points (Reference Ideas)

1. Trigger before reading: remind file size and suggested division of labor (whether a single-file subagent is better).
2. Trigger before writing: check required fields, source references, and wikilink placeholders.
3. Trigger after every AI file write: run one fast lint round (minimum checks).
4. Trigger after batch changes: run a broader lint round (broken links, naming conflicts, obvious contradictions).
5. Trigger after entity/concept backfill: run a read/grep quick check to confirm new-old page consistency.

## 3. Checks Suitable For Hook

1. Structure checks: missing fields, obvious format errors, title/type mismatch.
2. Link checks: broken links, island-page tendency, missing backlinks.
3. Naming checks: duplicate names, same-name ambiguity, alias conflicts.
4. Basic factual checks: obvious time conflicts, missing sources, self-contradiction on the same page.

## 4. What Should Not Be Put Into Hook

1. Complex long-chain reasoning and large-scale semantic rewriting.
2. High-cost, full-repo deep audits.
3. Final decisions that strongly depend on human context judgment.

Note: Hook is for “fast reminders” and “baseline guardrails,” not a replacement for full review.

## 5. Relationship With Lint

1. Hook can handle “fast lint after each write.”
2. Lint still needs periodic full checks.
3. They are complementary: Hook blocks bad changes early; lint performs systematic scanning.

## 6. Relationship With File2Agent

1. File2Agent solves division of labor: who reads and how to read.
2. Hook solves process timing: when to check and which errors to block first.
3. Combined together, they usually reduce omission rates in long-file ingest significantly.