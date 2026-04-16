# The Value Of Turbo Models (Low-Parameter Models) (Detailed)
[中文](./07-turbo-model-value.zh-CN.md) | **English**

## 0. Background (Why Add This Item)

In real Wiki maintenance, we observed a stable pattern:

1. Turbo/low-parameter models such as `GPT-5.3-Codex-Spark` and `GLM-5-Turbo` are average at “creating new wiki pages.”
2. But they perform very well at “maintaining wikilinks, checking format, and fixing structural consistency.”

So this section is not a “model ranking”; it is a task-division recommendation.

## 1. Tasks Suitable For Turbo Models

1. Wikilink maintenance: add links, repair broken links, backlink checks, and patching after isolated-page scans.
2. Format maintenance: frontmatter completeness, date-format consistency, naming conventions, heading hierarchy cleanup.
3. Structural lint: minimum-structure checks for `entity/concept/source/synthesis` pages.
4. Batch consistency: attribute alignment for similar pages, template-residue cleanup, duplicate-format-noise cleanup.

Shared characteristics of these tasks:  
clear rules, explicit goals, and relatively stable decision boundaries.

## 2. Tasks Not Recommended For Turbo-Only Execution

1. Creating high-quality new entity/concept pages (especially when multi-source synthesis is needed).
2. High-uncertainty fact merging and conflict arbitration.
3. Synthesis writing that requires strong narrative ability or deep reasoning.

These tasks rely more on knowledge integration and expression quality, and are not ideal for low-parameter-only execution.

## 3. Recommended Division Of Labor (Proposal)

1. Creation stage: prioritize a more stable primary model for fact compilation and page skeleton building.
2. Maintenance stage: use Turbo models for wikilinks, format, field consistency, and batch cleanup.
3. Regression stage: run another review on high-risk pages by humans or higher-capability models.

The core is not “who is stronger,” but “placing each model in the most suitable position.”

## 4. Relationship With Karpathy Core

This proposal does not change the bottom line:

1. It is still a Wiki, not chat logs.
2. It still uses wikilink network structure as the core.
3. It still serves the `ingest -> query -> lint` maintenance loop.

The value of Turbo is:  
making maintenance actions faster, more stable, and cheaper, so the loop is more sustainable.