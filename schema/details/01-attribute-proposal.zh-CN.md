# 属性规范倡议（详细）
**中文** | [English](./01-attribute-proposal.md)

## 背景

1. 我们在维护大规模 LLM-Wiki 时发现，仅靠正文很难稳定维护。
2. 页面要同时给人读、给 AI 读，如果没有基础属性，后续 ingest/query/lint 很容易混乱。
3. 这个文档的目标不是“多加字段”，而是给出一套最小、可扩展的字段约定。

## 为什么要做属性倡议

1. 没有统一属性时，同一类页面会被写成不同结构，后续查询和校验会变慢。
2. 没有最小字段契约时，很多检查只能靠反复 grep/read，维护成本会持续上升。
3. 把属性先约定好，后续扩展 `topic`、`status` 等字段会更稳。

## 建议字段集合（可裁剪）

```yaml
id: "wk-..."
title: "..."
type: "entity|concept|topic|source|synthesis"
source_ids:
  - "src-..."
last_reviewed: "YYYY-MM-DD"
status: "draft|stable|deprecated"   # 可实验
topic: "..."                        # 可实验
category: "..."                     # 可扩展
```

## 每个字段是做什么的

| 字段 | 作用 |
|---|---|
| `id` | 给页面一个稳定标识，便于去重和更新。 |
| `title` | 让人和 AI 都能快速定位页面。 |
| `type` | 明确页面类型，便于规则分流。 |
| `source_ids` | 保留来源线索，支持追溯。 |
| `last_reviewed` | 记录最近复核时间，便于发现过时内容。 |
| `status` | 区分草稿、稳定、废弃状态。 |
| `topic` | 支持按主题组织页面。 |
| `category` | 预留项目自定义分类空间。 |

## 使用原则

1. 这些字段是倡议，不是统一硬强制。
2. 字段数量以够用为准，不追求越多越好。
3. 新增字段前，先在 `ingest` 里定义入库规则，再开始批量写入。