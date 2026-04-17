# Page Attribute Contract
[中文](./01-attribute-proposal.zh-CN.md) | **English**

> Use this page when you need to answer a simple question: what fields should a production wiki page carry so the repository stays traceable and maintainable?

## 1. The Short Answer

Body text alone is not enough once a wiki grows.

When pages are read by both humans and AI, a page needs a small amount of stable metadata so ingest, query, and lint can keep agreeing on what the page is, where it came from, and whether it is ready to trust.

That is why this document recommends a minimum page attribute contract.

## 2. Recommended Minimum Fields

A stable production page should usually carry at least:

```yaml
id: "wk-..."
title: "..."
type: "entity|concept|topic|source|synthesis"
source_ids:
  - "src-..."
last_reviewed: "YYYY-MM-DD"
status: "draft|stable|deprecated"
topic: "..."      # optional / project-specific
category: "..."   # optional / project-specific
```

The first six fields are the important part. The last two are examples of safe extension points.

## 3. What These Fields Are For

| Field | Why it exists |
|---|---|
| `id` | Gives the page a stable identity for deduplication, updates, and references. |
| `title` | Makes the page easy for both humans and AI to locate. |
| `type` | Tells the system what kind of page it is routing or validating. |
| `source_ids` | Preserves provenance so claims can be traced back to evidence. |
| `last_reviewed` | Makes freshness and staleness visible. |
| `status` | Separates draft knowledge from stable knowledge. |
| `topic` | Helps projects group pages by theme if needed. |
| `category` | Leaves room for project-specific classification. |

## 4. How To Use This Contract

Use this field set as a minimum operating baseline, not as a decorative template.

1. Keep the contract small enough that teams will actually maintain it.
2. Treat the first six fields as the core production contract.
3. Add project-specific fields only when they have a clear ingest-time meaning.
4. If a field cannot be updated or validated consistently, it probably should not exist yet.

## 5. Why We Do Not Recommend Outline Templates For `entity` / `concept` Pages

The key distinction is this: we are not rejecting templates entirely. We are rejecting templates that hard-code page bodies into one fixed outline.

The reason is simple: an entity page or concept page must first remain a wiki page, not a form to fill.

When a template locks the body outline too tightly, models tend to become mechanical:

1. every page inherits the same heading skeleton, which looks tidy but drifts away from a real wiki
2. wikilinks become weaker because the model spends effort filling outline slots instead of organizing relations
3. sources often get pushed to the end while the page body lacks enough `[[wikilinks]]`
4. pages become self-contained documents rather than navigable nodes in a graph

But a wiki is not trying to make every page look identical.

A wiki is trying to preserve knowledge as maintainable pages and connect those pages through links. Different entities and concepts naturally need different shapes. Some pages should start with a definition. Others should start with relationships, chronology, or disputes. If body structure is locked too early, the result is often a document that looks page-like, but not a page that behaves like a maintainable wiki.

## 6. Why Templates Should Mainly Constrain Attributes

The stable part of a template is metadata, not the body skeleton.

That is because metadata directly serves system maintenance:

1. AI needs fields to know what a page is, where it came from, and whether it is safe to trust
2. ingest, query, and lint need the same fields to stay aligned
3. fields are reviewable, traceable, and governable at scale, while body outlines usually are not

That is why this repository recommends:

1. let templates constrain the minimum frontmatter / note attributes
2. keep body requirements at the wiki level: readable, linkable, rich in wikilinks when appropriate, and not isolated
3. separate human-read needs from AI-read needs instead of forcing both into one rigid outline

In short:

1. AI readability mainly comes from stable attributes
2. human readability mainly comes from natural body writing
3. wiki maintainability mainly comes from the wikilink network, not from a uniform outline

## 7. Why This Contract Matters

Without a shared attribute contract, pages of the same kind drift into different shapes.

That creates a familiar failure pattern:

1. ingest writes one shape
2. query assumes another shape
3. lint has to recover meaning with repeated grep/read passes

The result is not just messy metadata. The result is rising maintenance cost.

## 8. Extension Rule

When you want to add a new field, do not start by writing it everywhere.

Start by defining:

1. where the field comes from
2. how it is normalized
3. what happens when evidence is missing
4. when it should update
5. how conflicts are resolved

That next step is covered in [02-ingest-field-rules.md](./02-ingest-field-rules.md).
