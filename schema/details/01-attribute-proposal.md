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

## 5. Why This Contract Matters

Without a shared attribute contract, pages of the same kind drift into different shapes.

That creates a familiar failure pattern:

1. ingest writes one shape
2. query assumes another shape
3. lint has to recover meaning with repeated grep/read passes

The result is not just messy metadata. The result is rising maintenance cost.

## 6. Extension Rule

When you want to add a new field, do not start by writing it everywhere.

Start by defining:

1. where the field comes from
2. how it is normalized
3. what happens when evidence is missing
4. when it should update
5. how conflicts are resolved

That next step is covered in [02-ingest-field-rules.md](./02-ingest-field-rules.md).
