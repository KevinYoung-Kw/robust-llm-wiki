# File2Agent And Context Engineering
[中文](./05-file2agent-context-engineering.zh-CN.md) | **English**

## The Short Version

When one source file is too large to be read carefully inside one agent pass, the safer pattern is usually:

`1 source file -> 1 subagent owner`

This page explains why that rule exists, when it should be used, and how it connects to broader context-engineering decisions.

## Why This Pattern Exists

Large-scale wiki ingest often fails in a deceptively quiet way.

1. a file is marked as "handled,"
2. but only its early sections were actually read,
3. later sections never become entity or concept candidates,
4. the omission is discovered only after downstream gaps appear.

That failure mode is common when long files meet read limits, concurrent processing stays shallow, or the main agent is overloaded with aggregation work.

So the core problem here is not merely token budget. The deeper problem is coverage illusion: the system appears complete before the source has actually been understood.

## The Rule: One Subagent Owns One File

If a single `raw` file cannot be comfortably covered in one focused pass, assign one subagent to that file end to end.

The point is ownership, not ceremony.

That file-owning subagent should be responsible for:

1. reading the file deeply enough to cover the full content,
2. extracting candidate facts, entities, and concepts,
3. flagging ambiguity or conflict instead of smoothing it over,
4. returning structured output to the main agent for merge or synthesis.

This lowers the chance that a long file is only half-read while still being treated as complete.

## Why File Ownership Helps

### 1. It protects coverage

An agent with only one file to care about is less likely to lose the second half of the source.

### 2. It protects the main context

The orchestrating agent can stay focused on routing, comparison, merge decisions, and repo-wide consistency, rather than carrying every raw passage in one giant working memory.

### 3. It makes omissions visible

When one file has one clear owner, it becomes easier to ask: was this source fully read, partially read, or still unresolved?

### 4. It scales better under concurrency

Parallelism is helpful only when ownership is clear. Without ownership, concurrent ingest can create duplication, omission, or contradictory partial reads.

## Context Engineering: What The Main Agent Should Do

In this framework, context engineering is mostly about deciding where different kinds of thinking should live.

A practical split looks like this:

1. the main agent handles orchestration, synthesis, and final repo decisions,
2. subagents handle single-file deep reading and localized extraction,
3. `grep` is used to locate,
4. `read` is used to confirm,
5. structured summaries are returned upward instead of dumping long passages back into the main thread.

That separation matters because raw text is expensive. If every subtask returns huge excerpts, the orchestration layer becomes noisy again.

## When To Use Chunking

Chunking is a support tactic, not the primary abstraction.

Use chunked reading when:

1. a file is too large for one careful pass,
2. later sections are likely to contain independent facts,
3. the file mixes logs, indexes, and narrative segments that are easier to inspect in slices.

But the repo should still preserve the higher-level rule:

`1 src -> 1 raw`

Even if one raw file is inspected in several chunks, those chunks are intermediate handling artifacts. They should not become independent long-term source identities inside the wiki.

## Common Failure Modes This Page Is Trying To Prevent

This guidance exists to prevent a few recurring mistakes:

1. treating "processed" as equivalent to "fully read,"
2. letting long files silently bias toward the opening sections,
3. allowing chunk artifacts to masquerade as canonical sources,
4. overloading the main agent until merge decisions become shallow,
5. spreading omissions into later entity, concept, and synthesis pages.

## Recommended Operating Pattern

For most larger ingest tasks, the default collaboration pattern should be:

1. the main agent enumerates source files and risk,
2. oversized or dense files are assigned to dedicated subagents,
3. each subagent returns structured candidates and unresolved questions,
4. the main agent merges, normalizes, cross-checks, and writes final wiki pages,
5. the repo runs lint before those pages move toward `stable`.

This makes `File2Agent` less of a slogan and more of a control mechanism for coverage quality.

## Naming Note

`File2Agent` works well for the current repository because most ingest work still begins from files.

If the project later expands deeply into web pages, APIs, or mixed-source pipelines, a broader name such as `Source2Agent` may become more accurate. Until then, consistency is more valuable than cleverness.

## See Also

1. [robust-llm-wiki-CLAUDE.md](../robust-llm-wiki-CLAUDE.md)
2. [02-ingest-field-rules.md](./02-ingest-field-rules.md)
3. [06-claude-hook-guidelines.md](./06-claude-hook-guidelines.md)
