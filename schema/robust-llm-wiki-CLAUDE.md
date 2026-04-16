# robust-llm-wiki-CLAUDE

## 0. Purpose

This document defines top-level operating guidance for Claude-style agents in this repository.

It is a strategy and governance guide, not an implementation script spec.

1. Focus on principles, ownership, and decision order.
2. Keep tools/scripts/CI choices flexible where safety is not affected.
3. Enforce hard constraints only when they protect correctness, traceability, safety, or legal compliance.

## 1. Non-Negotiable Red Lines (Mandatory / 不可妥协红线)

1. Preserve Karpathy kernel exactly: Wiki form, wikilink network, and `ingest -> query -> lint` loop.
2. Keep strict layering and boundaries:
   - Raw sources are immutable source of truth; AI reads but does not modify them.
   - Wiki is AI-maintained markdown knowledge where controlled updates are allowed.
   - Schema defines governance rules and constraints.
   - `SPEC` defines boundaries, `schema/details/` defines detailed rules, and `research/` stores evidence.
3. Keep the three core operations explicit and always available: `ingest`, `query`, `lint`.
4. Any extension (types, fields, workflow, tooling) is valid only if kernel invariants stay intact.
5. Keep `index.md` as the content navigation entry and update it after ingest or major structure changes.
6. Keep `log.md` as append-only, parseable operation history; do not rewrite history in place.
7. Keep `[[wikilink]]` as the structural cross-reference mechanism for key entities/concepts.
8. Keep YAML frontmatter and minimum required production fields:
   - `id`, `title`, `type`, `source_ids`, `last_reviewed`, `status`
9. Keep role split clear: human curates goals/sources/questions; AI executes maintenance and synthesis updates.
10. Keep source grounding and promotion gates:
    - factual claims must remain traceable to evidence;
    - unsourced or unresolved claims must not enter `stable`.
11. Enforce `draft -> stable` gate:
    - new factual claims without `source_ids` are draft-only;
    - promotion requires `source_ids`, one cross-check round, and passing minimum lint.
12. Enforce independent verification:
    - writer cannot self-approve `draft -> stable` promotion;
    - high-risk promotions require a separate verifier (agent/model/human).
13. Enforce legal and compliance provenance:
    - reject unverified-license snippets and unattributed third-party material;
    - require contributor rights attestation and third-party source/license metadata;
    - track dependency license/version inventory;
    - keep repo license statements consistent, with SPDX where practical.
14. Security issues use private disclosure first; avoid public technical details before mitigation.
15. Keep maintenance auditable, rollback-friendly, and anonymized in examples/cases.

## 2. Decision Priority

When tradeoffs appear, prioritize in this order:

1. factual correctness and provenance
2. safety/legal/compliance constraints
3. structural integrity (fields, links, naming consistency)
4. maintainability and future rework cost
5. style and narrative polish

## 3. Operating Ownership By Loop Stage

### 3.1 Ingest

1. Own source assimilation and draft-page updates.
2. Treat raw sources as input truth layer, not output artifacts.
3. Compile into structured pages with explicit type and source trace.
4. Handle conflicts and uncertainty explicitly, not by silent guessing.

### 3.2 Query

1. Use wiki as working memory.
2. Query may write answer artifacts; direct wiki mutation still follows ingest-level evidence gates.
3. High-risk conclusions require extra verification before stable use.

### 3.3 Lint

1. Own maintenance guardrails, with layered order: `property -> link -> content`.
2. Lint may auto-fix structural issues; semantic edits require content-owner approval.
3. Use lint as risk control, not as a substitute for judgment.

### 3.4 Stage Handoff Contract

1. Each stage must emit a machine-parseable handoff:
   - `run_id`, `files_changed`, `evidence_refs`, `open_questions`, `risk_level`, `next_owner`
2. Next stage must validate required handoff fields before execution.
3. Missing required handoff fields is a stop condition that triggers escalation.

## 4. Agent Team Collaboration Rules

1. Default rule for long/high-impact files: `1 subagent -> 1 file` end-to-end ownership.
2. Each subagent must declare `owned_files` before edits.
3. In one batch, overlapping write ownership is disallowed by default.
4. If overlap is unavoidable, serialize updates with deterministic precedence and record resolution in `log.md`.
5. Main agent orchestrates and aggregates; subagents deep-read and execute file-scoped tasks.
6. Exceptions are allowed for short files or proven full-coverage workflows, but must be explicit.
7. Chunk/slice files are intermediate artifacts only:
   - never ingest chunk files as independent `src`;
   - always merge back to original source view (`1 src -> 1 raw`).

## 5. Context Engineering For Scale

1. Assume long files are normal; "processed" does not imply "fully read."
2. Separate responsibilities: locate first (`grep/index`), then deep-read (with chunked coverage when needed).
3. Treat hotspot files (especially `index.md` and `log.md`) as high-frequency check targets.
4. Favor structured intermediate summaries over raw copy-paste backfill.
5. Minimize context waste with explicit task decomposition and stable handoff records.

## 6. Field And Schema Governance

1. Before adding any new field/type, define ingest contract first:
   - source, normalization, default behavior, update strategy, conflict handling
2. No schema expansion without documented and reviewable rules.
3. Rules must be lint-checkable.
4. Rule changes and stable-content changes must record effective date and impact scope.
5. `type` and `status` must use approved enums; unknown values should fail ingest/lint.
6. Define staleness policy from `last_reviewed`; stale pages should be flagged and blocked from auto-promotion.

## 7. Lint And Hook Execution Cadence

1. Mandatory lint rhythm:
   - after each ingest/repair batch: fast `property + link`;
   - before `draft -> stable` promotion: `content lint`;
   - periodic broader lint still required.
2. Keep pre/post lint snapshots for major changes to support diff and rollback.
3. Hook trigger minimums:
   - before read: file-size/risk reminder and decomposition hint
   - before write: required fields/sources/wikilinks precheck
   - after each AI write: fast minimum lint
   - after batch/backfill: broader lint and consistency quick check
4. If path-risk checks detect unexpanded variables or unsafe quoting/concatenation, pause batch ingest/query until fixed.

## 8. Hallucination And Risk Control

1. Follow low-hallucination-first policy for long-running wiki maintenance.
2. Model selection must use comparable benchmark evidence (same benchmark/scope).
3. Evaluate hallucination rate together with answer rate; avoid single-metric decisions.
4. Do not assume newer model version is automatically safer.
5. Use risk classes for operations:
   - `R1`: routine maintenance
   - `R2`: high-risk page/update
   - `R3`: critical conclusion/arbitration
6. `R2/R3` require extra read/grep validation and stricter promotion checks.
7. After factual/link repairs, repeat `read/grep/lint` until conflicts and broken links are reduced to a controllable threshold.

## 9. Model Allocation Thinking

1. Use lower-hallucination paths for ingest and high-impact synthesis.
2. Use faster/lower-cost models for repetitive maintenance (links, formatting, structural cleanup).
3. Do not use Turbo-only flow for high-uncertainty fact arbitration or high-quality new page creation.
4. High-risk outputs require regression pass by human or higher-capability model.

## 10. Path And Workspace Safety (Mandatory)

1. Avoid fragile path patterns by default, especially unsafe handling of spaces/quotes.
2. Treat suspicious path artifacts (for example unexpanded variable fragments) as risk signals.
3. Keep knowledge area separated from temporary tooling artifacts.
4. Path-risk findings are stop-the-line signals until script safety is repaired.

## 11. Escalation Protocol

Escalate to human owner when any of the following occurs:

1. high-impact claims lack reliable sources
2. conflicts persist after repeated attempts
3. critical schema/lint checks fail repeatedly
4. required handoff fields are missing

During escalation:

1. freeze affected files to read-only except assigned resolver
2. record owner, deadline, and unblock criteria in `log.md`
3. resume normal flow only after unblock checks pass

## 12. What Is Intentionally Left Flexible

The following remain project-dependent:

1. exact scripts, commands, and CI wiring
2. lint threshold values and alert levels
3. model vendor/version choices under this policy
4. folder-level mechanics beyond core boundaries

## 13. Minimum Output Contract For AI Changes

For each meaningful change batch, provide:

1. why the change was needed
2. what was changed (`files_changed`, and `owned_files` when agent team is used)
3. evidence references and unresolved conflicts/open questions
4. risk class (`R1/R2/R3`) and whether escalation is active
5. what should be checked next
6. ingest/batch coverage metrics:
   - files fully read
   - candidate outputs
   - unresolved conflict count
   - broken-link delta
7. when rules/stable content are changed, include effective date and impact scope
