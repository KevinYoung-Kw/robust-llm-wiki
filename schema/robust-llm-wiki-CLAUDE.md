# robust-llm-wiki-CLAUDE

## 0. Purpose

This document defines high-level operating thinking for Claude-style agents in this repository.

It is a strategy guide, not an implementation spec.

1. Focus on principles and decision order.
2. Keep concrete tooling and scripting choices flexible.
3. Only enforce hard constraints when they protect correctness, traceability, or safety.

## 1. Non-Negotiable Red Lines (Mandatory / 不可妥协红线)

1. Preserve Karpathy kernel exactly: Wiki form, wikilink network, and `ingest -> query -> lint` loop.
2. Keep the three-layer architecture:
   - Raw sources are immutable source of truth; the AI reads them but does not modify them.
   - Wiki is AI-maintained markdown knowledge; the AI can create/update pages here.
   - Schema defines rules, conventions, and workflow constraints.
3. Keep the three core operations explicit and always available:
   - `ingest`: integrate new sources into the wiki.
   - `query`: answer from wiki pages and optionally file high-value outputs back.
   - `lint`: run periodic health checks for structural and semantic drift.
4. Keep `index.md` as the content navigation entry:
   - list major wiki pages with short summaries;
   - update after ingest or large structural changes.
5. Keep `log.md` as an append-only chronological operation record:
   - record ingest/query/lint events in a parseable format;
   - do not rewrite history in place.
6. Keep `[[wikilink]]` syntax for cross-reference structure; avoid plain-text-only references for key entities/concepts.
7. Keep YAML frontmatter on wiki pages for minimum machine readability and governance checks.
8. Keep Obsidian compatibility for wiki browsing and graph navigation.
9. Keep role split clear: human curates goals/sources/questions; AI performs maintenance/bookkeeping/synthesis updates.
10. Keep source grounding and quality gates:
    - factual claims should remain traceable to source evidence;
    - unsourced or unresolved claims should not directly enter stable state.
11. Keep maintenance auditable and rollback-friendly.
12. Keep strict anonymization in examples and case references.

## 2. Decision Priority

When tradeoffs appear, prioritize in this order:

1. factual correctness and provenance
2. structural integrity (fields, links, naming consistency)
3. maintainability and future rework cost
4. style and narrative polish

## 3. Operating Thinking By Loop Stage

### 3.1 Ingest

1. Treat raw sources as truth input layer, not as output artifacts.
2. Compile into structured wiki pages with clear type and source trace.
3. Handle conflict and uncertainty explicitly instead of silently guessing.

### 3.2 Query

1. Use wiki as the working memory layer.
2. For high-risk conclusions, require additional verification before treating as stable knowledge.
3. Query-derived updates may be written back, but should still pass quality gates.

### 3.3 Lint

1. Apply layered lint in order: `property -> link -> content`.
2. Prefer quick checks after writes and broader checks after batches.
3. Use lint as a maintenance guardrail, not as a substitute for judgment.

## 4. Page State And Promotion Logic

1. Keep a practical two-lane mindset: `draft` for provisional synthesis, `stable` for vetted knowledge.
2. Promote from draft to stable only after minimum evidence and consistency checks are satisfied.
3. Use templates mainly to constrain attributes, not to hard-lock body outline style.
4. Keep pages readable for both humans and AI instead of optimizing for only one reader.

## 5. Context Engineering For Scale

1. Assume long files are common; avoid shallow one-pass reading on high-impact files.
2. Separate responsibilities: locate first, then deep-read where needed.
3. Favor structured intermediate summaries over large raw copy-paste backfill.
4. Minimize context waste from repeated re-check loops by explicit task decomposition.

## 6. Hook Philosophy (Guidance)

1. Treat hooks as lightweight process guardrails across read, write, and backfill stages.
2. Keep hook checks cheap, frequent, and focused on obvious breakage risks.
3. Leave heavy semantic reasoning and major rewrites to normal workflow, not hook callbacks.

## 7. Model Allocation Thinking

1. Use lower-hallucination paths for ingest and high-impact fact synthesis.
2. Use faster/lower-cost models for repetitive maintenance (links, formatting, structural cleanup).
3. Do not assume newer model versions are automatically safer; rely on comparable evidence.

## 8. Path And Workspace Safety (Mandatory)

1. Avoid fragile path patterns by default (especially unsafe handling of spaces/quotes).
2. Treat suspicious path artifacts (for example, unexpanded variable fragments) as risk signals.
3. Keep knowledge area separated from temporary tooling artifacts when possible.

## 9. What Is Intentionally Left Flexible

The following should be decided by the executing AI or downstream project context:

1. exact scripts, commands, and CI wiring
2. lint cadence and threshold values
3. prompt formats and agent decomposition details
4. folder-level implementation mechanics beyond core boundaries

## 10. Minimum Output Contract For AI Changes

For each meaningful change batch, provide:

1. why the change was needed
2. what was changed
3. which risks remain
4. what should be checked next
