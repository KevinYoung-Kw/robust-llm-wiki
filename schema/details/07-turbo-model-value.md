# The Value Of Turbo Models
[中文](./07-turbo-model-value.zh-CN.md) | **English**

## The Short Version

Turbo or lower-cost models are often not the best choice for first-pass knowledge synthesis.

They can, however, be excellent choices for maintenance work with clearer rules and tighter boundaries: links, fields, formatting, cleanup, consistency passes, and other repetitive structural tasks.

So this page is not a ranking. It is a routing guide.

## Why This Distinction Matters

LLM-Wiki work is not one task. It is a stack of different tasks with different risk profiles.

Some tasks demand careful fact integration, ambiguity handling, and narrative judgment. Others are closer to constrained maintenance: fix the link, normalize the field, repair the heading, remove the residue.

When those two categories are treated as identical, teams often either:

1. overspend on simple maintenance, or
2. underspecify high-risk synthesis.

The point of this document is to keep model allocation aligned with task shape.

## Where Turbo Models Usually Shine

Turbo-style models are often strong fits for work that is:

1. rule-bound,
2. repetitive,
3. easy to verify,
4. low-risk if reviewed once more at the end.

Typical examples include:

1. wikilink repair and backlink maintenance,
2. frontmatter and required-field cleanup,
3. heading and formatting normalization,
4. duplicate residue or template-noise removal,
5. batch consistency checks across similar pages,
6. lightweight structural lint assistance.

These are not trivial tasks, but their boundaries are usually clearer than open-ended synthesis.

## Where Turbo Models Should Not Be The Default

Turbo models are a weaker default when the work requires:

1. creating new entity or concept pages from mixed sources,
2. resolving conflicting facts,
3. writing synthesis pages that need careful narration,
4. handling ambiguous identity or timeline problems,
5. producing pages that will become high-authority references inside the repo.

These tasks are more expensive when wrong, so they deserve stronger factual and reasoning margins.

## A Practical Division Of Labor

A healthy pattern for this repository is:

1. use a stronger or lower-hallucination primary model for ingest and factual synthesis,
2. use turbo models for link, format, field, and consistency maintenance,
3. send high-risk outputs back through either human review or a stronger second pass before stabilization.

This is the same design instinct found elsewhere in the framework: do not ask one tool to be equally good at every layer of the loop.

## Why Turbo Still Matters Strategically

Turbo models are valuable not because they replace the core model, but because they keep the maintenance loop sustainable.

They allow the repo to do more of the following at reasonable cost:

1. routine cleanup,
2. post-write guardrails,
3. repeated consistency passes,
4. broad but shallow maintenance sweeps.

If these tasks are too expensive, teams delay them. When teams delay them, small inconsistencies accumulate. Over time, that weakens the whole wiki.

So the strategic value of turbo is not glamour. It is upkeep.

## Relationship To The Core Framework

Nothing in this page changes the central commitments of robust LLM-Wiki:

1. the repo is still a wiki rather than a transcript dump,
2. wikilinks still matter as structure,
3. `ingest -> query -> lint` remains the maintenance loop,
4. stronger judgment is still reserved for higher-risk tasks.

Turbo models become useful precisely because the framework already distinguishes between "authoring knowledge" and "maintaining knowledge structure."

## See Also

1. [04-hallucination-control.md](./04-hallucination-control.md)
2. [03-lint-playbook.md](./03-lint-playbook.md)
3. [robust-llm-wiki-CLAUDE.md](../robust-llm-wiki-CLAUDE.md)
