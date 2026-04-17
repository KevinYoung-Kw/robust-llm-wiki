# robust-llm-wiki-CLAUDE

> Status: active operator policy for release `v0.1.2`
> Scope: this is the runbook for agents working in this repository.
> Boundary: `SPEC.md` explains what the system is; this file explains how to run it safely.

## 1. What This File Is For

Think of `SPEC.md` as the architectural boundary and this file as the operator handbook.

`SPEC.md` says what must remain true about the system: it is still a wiki, it still uses wikilinks, and it still runs the `ingest -> query -> lint` loop.

This document starts one layer lower. It answers questions such as:

1. when an agent is allowed to write
2. what must stay in `draft`
3. how an agent team should divide work
4. what to check before promoting content to `stable`
5. what should be recorded so the next operator can trust the work

## 2. When In Doubt, Decide In This Order

Most day-to-day decisions become easier if the priority order is clear.

1. protect factual correctness and provenance
2. protect safety, legal, and compliance requirements
3. protect structural integrity: fields, links, naming, and page status
4. protect maintainability and future rework cost
5. polish style only after the first four are safe

## 3. The Operating Posture

Before talking about individual stages, it helps to keep a simple mental model in view.

1. Preserve the Karpathy kernel exactly: wiki form, wikilink network, and the `ingest -> query -> lint` loop.
2. Treat raw sources as the read-only truth layer. Agents may read them, cite them, and synthesize from them, but do not rewrite them.
3. Treat the wiki as the maintained knowledge layer. This is where controlled AI updates are allowed.
4. Treat schema as governance. `SPEC.md` defines the architectural boundary, this file defines operator behavior, `schema/details/` holds focused rules and playbooks, and `research/` holds evidence and case material.
5. Keep the human/AI split legible: humans decide goals, source scope, and exception handling; agents do maintenance, synthesis, and structured checking.
6. Prefer explicit uncertainty over silent guessing. If a claim is shaky, keep it marked, scoped, or blocked rather than smoothing it over.
7. Keep provenance and compliance visible. Do not accept third-party material with unverifiable origin or unclear license. Require contributor rights attestation and third-party source or license metadata, keep dependency license and version inventory, keep repo license statements consistent with SPDX where practical, and route security disclosures through a private channel first.

### 3.1 What "Preserve The Karpathy Kernel" Means In Practice

The phrase "preserve the Karpathy kernel" is too abstract on its own. An agent should be able to picture the original operating pattern concretely.

At minimum, the baseline looks like this:

1. There are three layers: `raw sources`, `wiki`, and `schema`.
2. `raw sources` are immutable input documents. The agent reads them, but does not rewrite them.
3. `wiki` is the maintained markdown knowledge base. The agent is allowed to create, revise, and cross-link pages here.
4. `schema` is the operating contract. It tells the agent how the wiki is shaped, how ingest/query/lint work, and what good maintenance looks like.

The core workflow from Karpathy's original pattern is also concrete, not symbolic:

1. `ingest` reads a newly added source,
2. writes or updates a source summary page,
3. updates relevant entity, concept, comparison, overview, or synthesis pages,
4. updates `index.md`,
5. appends an operation record to `log.md`.

That means ingest is not "write one summary and stop." A single source may legitimately update many existing pages because the whole point is cumulative maintenance of the wiki graph.

### 3.2 What Query And Lint Mean In The Original Pattern

The original pattern also makes `query` and `lint` more specific than many agents assume.

For `query`:

1. the agent searches the wiki first, not the raw corpus first,
2. often reads `index.md` before drilling into specific pages,
3. synthesizes an answer from maintained wiki pages,
4. may file valuable answer artifacts back into the wiki as new markdown pages instead of leaving them trapped in chat history.

For `lint`:

1. the agent scans for contradictions across pages,
2. looks for stale claims superseded by newer sources,
3. checks for orphan pages, weak cross-references, or important missing concept pages,
4. suggests concrete maintenance actions that keep the wiki healthy as it grows.

So `lint` is not just formatting. In the Karpathy pattern, it is a recurring wiki health pass.

### 3.3 Two Special Files Are Part Of The Baseline

Agents should also treat `index.md` and `log.md` as first-class operating files, not optional decoration.

1. `index.md` is content-oriented. It is the catalog of the wiki and the first navigation surface for both humans and agents.
2. `log.md` is chronological and append-only. It records ingests, queries, lint passes, and other meaningful operations in a parseable way.

If an implementation omits these files, it can still be inspired by LLM-Wiki, but it is no longer following the original baseline very closely.

## 4. Run The Core Loop Deliberately

The repository stays healthy when `ingest`, `query`, and `lint` are treated as distinct jobs instead of one blurred activity.

### 4.1 Ingest

Ingest turns source material into draft knowledge.

1. Ingest owns source assimilation and draft-page updates.
2. Every factual claim written during ingest should remain traceable to evidence.
3. New factual claims without `source_ids` stay in `draft` and do not enter `stable`.
4. Conflicts should be carried forward explicitly as conflicts, open questions, or blocked promotions. Do not resolve them by confident wording alone.

### 4.2 Query

Query uses the wiki as working memory, but it does not get to bypass evidence rules.

1. Query may produce answer artifacts or synthesis notes.
2. Direct mutation of canonical wiki content still follows ingest-level evidence and promotion gates.
3. High-impact conclusions drawn during query should be treated as provisional until they pass the same verification path expected of other wiki changes.

### 4.3 Lint

Lint is the maintenance guardrail. It keeps the system shaped correctly so content work stays trustworthy.

1. Run lint in layered order: `property -> link -> content`.
2. Lint may auto-fix structural issues such as metadata shape or broken formatting.
3. Lint should not silently make semantic decisions. Meaning-changing edits require content-owner approval or a new ingest-style review.

### 4.4 Stage Handoff

Each stage should leave the next one with enough structure to continue safely.

1. Every stage emits a machine-parseable handoff with `run_id`, `files_changed`, `evidence_refs`, `open_questions`, `risk_level`, and `next_owner`.
2. The next stage validates those fields before starting work.
3. Missing handoff fields are a stop condition, not a soft warning.

## 5. Keep `draft` And `stable` Meaningfully Different

One of the easiest ways to damage a wiki is to let unfinished work quietly masquerade as settled knowledge. The `draft -> stable` boundary exists to prevent that.

1. Production pages should carry the minimum contract: `id`, `title`, `type`, `source_ids`, `last_reviewed`, and `status`.
2. `type` and `status` must come from approved enums. Unknown values should fail ingest or lint.
3. `draft` is where new, incomplete, or disputed knowledge can live while it is being worked through.
4. `stable` is reserved for knowledge that is source-traceable, cross-checked, and structurally clean.
5. Promotion to `stable` requires `source_ids`, at least one cross-check round, and passing minimum lint.
6. The writer does not approve their own `draft -> stable` promotion.
7. Stable promotion requires an independent verification pass. For `R2` and `R3` work, use a separate verifier agent, model, or human reviewer.
8. Pages past the project staleness window should be flagged from `last_reviewed` and blocked from automatic promotion.

## 6. Extend The Schema Carefully

The schema should be able to grow, but growth needs a contract before it needs a field.

1. Before adding a new field or page type, define its ingest contract first: source, normalization, default behavior, update strategy, and conflict handling.
2. No schema expansion should be merged if those pieces are missing or too vague to review.
3. New rules should be lint-checkable whenever possible.
4. Rule changes and `stable` content changes should record an effective date and impact scope.

## 7. Work As An Agent Team, Not A Swarm

Agent teams help most when they reduce ambiguity, not when they multiply it.

1. For long or high-impact files, default to `1 subagent -> 1 file` end-to-end ownership.
2. Each subagent declares `owned_files` before editing.
3. In a single batch, each target wiki file should have exactly one writer. Other agents may read, review, or prepare context, but they stay read-only for that file.
4. If overlapping edits are unavoidable, serialize them with deterministic precedence and record the resolution in `log.md`.
5. The main agent orchestrates, aggregates, and resolves cross-file questions. Subagents do the deep reading and file-scoped execution.
6. Short files or proven full-coverage workflows may justify exceptions, but make those exceptions explicit.

Long files need special handling because "processed" and "fully read" are not the same thing.

1. Assume long files are normal, not exceptional.
2. Treat hotspot files such as `index.md` and `log.md` as high-frequency check targets.
3. Locate first, then deep-read. Use indexes, search, or chunking to find the relevant region before assigning deep ownership.
4. Chunk or slice files are intermediate artifacts only. Never ingest them as independent `src`.
5. Always merge chunked work back to the original source view: `1 src -> 1 raw`.

## 8. Verify On A Rhythm, Not By Vibe

Reliable maintenance comes from a repeated checking rhythm. The point is not to lint constantly; the point is to lint at the moments when bad state is most likely to enter the system.

1. Before read: run a light reminder about file size, risk, and whether decomposition is needed.
2. Before write: check required fields, source trace, and wikilink expectations.
3. After every AI write: run fast minimum lint.
4. After every ingest or repair batch: run fast `property + link` lint.
5. Before `draft -> stable` promotion: run content lint.
6. After larger backfills or batch work: run broader lint and a consistency spot-check.
7. Keep pre- and post-lint snapshots for major changes so diffs and rollback decisions remain easy.
8. If factual or link repairs expose more drift, repeat `read/grep/lint` until conflicts and broken links are reduced to a controllable threshold.
9. If path-risk checks find unexpanded variables, unsafe quoting, or suspicious concatenation, pause batch ingest and query work until the script safety issue is fixed.

## 9. Classify Risk Early And Escalate Cleanly

Not all work deserves the same process. Risk classes help the team spend extra care where it matters.

1. `R1`: routine maintenance, formatting, straightforward metadata repair, or low-impact updates
2. `R2`: high-risk page updates, important synthesis changes, or edits that could spread through many links
3. `R3`: critical conclusions, fact arbitration, or work that could materially change downstream interpretation

Use the class to shape the workflow.

1. `R2` and `R3` require extra `read/grep` validation and stricter promotion checks.
2. Escalate to a human owner when high-impact claims lack reliable sources, conflicts persist after repeated attempts, critical schema or lint checks keep failing, or required handoff fields are missing.
3. During escalation, freeze affected files to read-only except for the assigned resolver.
4. Record the owner, deadline, and unblock criteria in `log.md`.
5. Resume normal flow only after the unblock checks pass.

## 10. Allocate Models By Failure Mode

Model choice matters most when it changes the kind of mistake the system is likely to make.

1. Use lower-hallucination paths for ingest and high-impact synthesis.
2. Use faster or lower-cost models for repetitive maintenance such as formatting cleanup, wikilink repair, and structural consistency work.
3. Do not run a Turbo-only path for high-uncertainty fact arbitration or high-quality new page creation.
4. Evaluate model choices on comparable evidence. Use the same benchmark or task scope when comparing versions.
5. Look at hallucination rate together with answer rate. A low hallucination score alone is not enough if the model stops answering useful questions.
6. Do not assume a newer version is automatically safer.
7. High-risk outputs should receive a regression pass from a higher-capability model or a human reviewer before they are treated as dependable.

## 11. Repository Conventions That Keep Work Legible

These are not the architectural core of the system, but they make the repository easier to operate and audit.

1. Keep `index.md` as the content navigation entry and update it after ingest or major structural changes.
2. Keep `log.md` append-only and parseable. It is an operation history, not a scratchpad.
3. Keep `[[wikilink]]` as the structural cross-reference mechanism for important entities and concepts.
4. Keep temporary tooling artifacts out of the knowledge area.
5. Keep examples and case material anonymized when the context requires it.

## 12. Leave An Audit Trail Others Can Trust

Every meaningful batch of work should make the next review easier, not harder.

1. Record why the change was needed.
2. Record `files_changed`, and `owned_files` when an agent team was used.
3. Record `evidence_refs`, unresolved conflicts, open questions, and `next_owner` when the work is not complete.
4. Record the risk class and whether escalation is active.
5. Record what should be checked next.
6. Report basic coverage signals for ingest or batch work: files fully read, candidate outputs, unresolved conflict count, and broken-link delta.
7. When rules or `stable` content change, include the effective date and impact scope.

## 13. What Stays Flexible

This policy is intentionally strict about correctness and traceability, but it leaves room for local engineering choices.

1. Exact scripts, commands, and CI wiring remain project-dependent.
2. Lint thresholds and alert levels may evolve with repository scale.
3. Model vendor and version choices remain flexible as long as they respect the operating policy above.
4. Folder-level mechanics beyond the core system boundary can change without rewriting the core policy.
