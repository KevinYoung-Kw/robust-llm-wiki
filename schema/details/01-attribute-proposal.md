# Attribute Proposal (Detailed)
[中文](./01-attribute-proposal.zh-CN.md) | **English**

## Background

1. During large-scale LLM-Wiki maintenance, we found that body text alone is hard to maintain stably.
2. Pages are read by both humans and AI. Without baseline attributes, ingest/query/lint quickly becomes inconsistent.
3. The goal of this document is not “adding more fields,” but proposing a minimum and extensible field convention.

## Why Attribute Proposal Is Needed

1. Without unified attributes, pages of the same type end up in different structures, slowing down later query and validation.
2. Without a minimum field contract, many checks depend on repeated grep/read, and maintenance cost keeps rising.
3. If attributes are agreed first, extending fields such as `topic` and `status` later is more stable.

## Recommended Field Set (Trimmable)

```yaml
id: "wk-..."
title: "..."
type: "entity|concept|topic|source|synthesis"
source_ids:
  - "src-..."
last_reviewed: "YYYY-MM-DD"
status: "draft|stable|deprecated"   # experimental
topic: "..."                        # experimental
category: "..."                     # extensible
```

## What Each Field Is For

| Field | Purpose |
|---|---|
| `id` | Gives each page a stable identifier for deduplication and updates. |
| `title` | Helps both humans and AI locate the page quickly. |
| `type` | Clarifies page type for rule-based routing. |
| `source_ids` | Preserves source trace for provenance. |
| `last_reviewed` | Records latest review date for staleness detection. |
| `status` | Distinguishes draft/stable/deprecated states. |
| `topic` | Supports topic-based organization. |
| `category` | Reserves room for project-specific custom categories. |

## Usage Principles

1. These fields are recommendations, not a uniform hard mandate.
2. Field count should be “enough to use,” not “the more the better.”
3. Before adding new fields, define ingest rules first, then start batch writing.