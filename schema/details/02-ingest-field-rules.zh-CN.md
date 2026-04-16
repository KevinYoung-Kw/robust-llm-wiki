# Ingest 字段落地规则（详细）
**中文** | [English](./02-ingest-field-rules.md)

## 背景

1. 字段是否可用，不在于字段名本身，而在于 `raw -> wiki` 的落地规则是否明确。
2. 我们在真实维护中看到：字段先写、规则后补，会带来大量返工。
3. 这个文档是为了把字段规则前置，减少后续修复成本。

## 为什么这件事要先做

1. 没有入库规则时，同名字段在不同页面会表达不同意思。
2. 规则不清时，AI 写入会不一致，后续只能多轮 grep/read 去排查。
3. 页面规模一大，字段冲突会在 ingest/query 里反复出现。

## 新增字段前必须定义

1. 来源：这个字段从哪类 raw 信息抽取。
2. 归一化：值要按什么格式统一。
3. 默认值：缺少证据时填什么，不填什么。
4. 更新策略：什么时候更新，什么时候保留旧值。
5. 冲突处理：多来源不一致时按什么规则决策。

## 为什么要定义这五项

1. 定义来源：让字段可追溯。
2. 定义归一化：让同类值可比较、可检索。
3. 定义默认值：避免把空值当成真值。
4. 定义更新策略：避免新噪声覆盖已确认信息。
5. 定义冲突处理：避免同一实体长期自相矛盾。

## 最低落地要求

1. 规则必须写入文档，能被复查。
2. 规则必须能被 lint 检查。
3. 规则更新要记录生效时间和影响范围。

## `draft/stable` 写入闸门（建议最小规则）

1. 任何新增事实声明，如果没有 `source_ids`，只能写入 `draft`，不得直接进入 `stable`。
2. 从 `draft` 进入 `stable`，至少满足三项：
   - 有可追溯 `source_ids`
   - 完成一轮 `read/grep` 交叉核验
   - 通过本轮 `property + link` 最小 lint
3. `query` 结果默认先回写到 `draft`，不直接覆盖 `stable`。
4. `stable` 页面若需修改，必须保留来源与变更痕迹（例如变更摘要或日志记录）。

## 来源依据（外部 Gist/Repo + 本项目推断）

外部可定位来源：

1. Karpathy 原始 gist：<https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f>
2. Karpathy gist raw：<https://gist.githubusercontent.com/karpathy/442a6bf555914893e9891c11519de94f/raw/ac46de1ad27f92b28ac95459c782c07f6b8c964a/llm-wiki.md>
3. Astro-Han/karpathy-llm-wiki（repo）：<https://github.com/Astro-Han/karpathy-llm-wiki>
4. xoai/sage-wiki（repo）：<https://github.com/xoai/sage-wiki>

口径说明：

1. 本文的 `draft/stable` 写入闸门是本项目治理规则，属于在上述 gist/repo 模式与本仓库调研结论基础上的规范化落地，不是某一上游来源的逐字原句。
