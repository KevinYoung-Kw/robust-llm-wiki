# Claude Hook Guidelines
[中文](./06-claude-hook-guidelines.zh-CN.md) | **English**

## The Short Version

Hooks exist to catch obvious mistakes while the work is still cheap to correct.

They are not substitutes for judgment, and they are not full audits. Their job is to provide light, repeatable process guardrails at the moments where teams most often forget to check.

In this repo, the most important hook principle is simple:

run fast checks close to writes, not only at the end of a long editing session.

## What Problem Hooks Solve

Many wiki maintenance errors do not come from a lack of knowledge. They come from delayed verification.

1. a page is edited,
2. the change seems plausible,
3. the team keeps moving,
4. only much later does someone discover a missing field, broken link, naming conflict, or obvious contradiction.

By then the cost is higher, because later edits may already depend on that page.

Hooks push a small amount of checking earlier in the loop. That is their value.

## What A Hook Should Be In This Framework

Within robust LLM-Wiki, a hook should be:

1. lightweight,
2. predictable,
3. close to the moment of risk,
4. focused on errors that are cheap to detect and expensive to ignore.

That means hooks are best used for structural checks, naming checks, link checks, and other fast safeguards that help the repo stay tidy while people and agents are still editing.

## The Most Useful Trigger Points

The following trigger points usually provide the best return:

### Before reading

Warn when a file is large, structurally dense, or likely to need a dedicated subagent.

### Before writing

Check whether the target page has the required fields, source anchors, and expected page type.

### After every AI file write

Run the fastest meaningful lint pass. This is the single most valuable trigger because it catches damage at the point where correction is still cheap.

### After batch changes

Run a broader pass for broken links, alias conflicts, duplicate names, and obvious repository-level inconsistencies.

### After backfill or relation updates

Confirm that newly added links and the pages they point to still agree with each other.

## What Hooks Are Good At

Hooks are well suited for:

1. missing required fields,
2. obvious formatting mistakes,
3. title or type mismatches,
4. broken-link or missing-backlink signals,
5. duplicate-name and alias-conflict warnings,
6. shallow factual red flags such as impossible dates or self-contradiction on the same page.

These checks are valuable precisely because they are narrow. A good hook does not try to be wise. It tries to be timely.

## What Hooks Should Not Try To Do

Hooks should not absorb tasks that belong to slower review layers.

Avoid using hooks for:

1. complex multi-step reasoning,
2. large-scale semantic rewriting,
3. whole-repo deep audits,
4. final conclusions that depend on nuanced human context.

When hooks try to do too much, they become noisy, slow, and easy to ignore. Once that happens, teams stop trusting them.

## Relationship To Lint

Hooks and lint are complementary, not interchangeable.

1. hooks catch mistakes near the point of change,
2. lint performs broader and more systematic scanning,
3. both are needed if the repo is meant to stay stable over time.

Another simple way to say it:

hooks reduce bad writes entering the graph,
lint reduces unnoticed drift accumulating across the graph.

## Relationship To File2Agent

`File2Agent` decides who should read and own a difficult source.

Hooks decide when to stop and verify before errors become expensive.

Together they create a healthier loop:

1. deep reading gets assigned more deliberately,
2. writing gets checked sooner,
3. structural mistakes stop spreading quite so easily.

## Recommended Operating Posture

If a team is unsure where to begin, start with one discipline:

always run a fast hook-style lint after each AI file write.

That single habit already prevents a surprising amount of rework. Later, the hook system can expand to include stronger checks, but it should earn that complexity rather than start with it.

## See Also

1. [03-lint-playbook.md](./03-lint-playbook.md)
2. [05-file2agent-context-engineering.md](./05-file2agent-context-engineering.md)
3. [robust-llm-wiki-CLAUDE.md](../robust-llm-wiki-CLAUDE.md)
