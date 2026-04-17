# Ingest 字段规则
**中文** | [English](./02-ingest-field-rules.md)

> 当你要判断一个新字段是否真的可以被大规模写进 wiki 时，请读这篇。

## 1. 先讲核心规则

一个字段不是因为名字看起来合理，就算准备好了。

只有当这个字段的 `raw -> wiki` 路径被说清楚了，它才真正可用。也就是说，系统必须知道：这个值从哪里来、怎么归一化、什么时候允许更新。如果这些问题还没回答清楚，这个字段迟早会把返工成本带回来。

所以，这篇文档把字段规则视为“规模化之前就要定义的东西”，而不是“写多了之后再补”的东西。

## 2. 新增字段前必须定义什么

在一个新字段被广泛写入之前，先把下面五件事定义完整：

1. `Source`：这个字段从哪类 raw 证据中抽取
2. `Normalization`：它最终要被统一成什么格式
3. `Default`：证据缺失或不完整时该怎么处理
4. `Update strategy`：什么时候覆盖，什么时候保留，什么时候延后
5. `Conflict handling`：多来源冲突时按什么规则裁决

这五项里只要有一项还很模糊，这个字段就还没准备好。

## 3. 为什么一定是这五项

它们不是形式上的填空题，而是用来阻止常见失控路径的。

1. Source 让字段可追溯。
2. Normalization 让同类值可比较。
3. Default 避免空值或猜测值伪装成事实。
4. Update strategy 防止新噪声覆盖旧的确认信息。
5. Conflict handling 防止同一页面长期自相矛盾。

## 4. 最低落地要求

即使这五项都写了，规则也还不算真的落地，除非它已经能被执行。

至少要满足：

1. 规则必须写入文档
2. 规则必须能被复查
3. 规则最好能被 lint 检查
4. 规则更新应记录生效时间和影响范围

## 5. `draft -> stable` 闸门

最重要的一条写入规则其实很简单：缺少证据，就缺少进入稳定层的许可。

1. 任何没有 `source_ids` 的新增事实，只能留在 `draft`。
2. 它不能直接进入 `stable`。
3. 从 `draft` 进入 `stable`，通常至少要满足：
   - 有可追溯 `source_ids`
   - 完成一轮 `read/grep` 交叉核验
   - 通过最低限度的 `property + link` lint
4. `query` 产出的内容默认先回写到 `draft`，而不是直接覆盖 `stable`。
5. 如果 `stable` 页面被修改，就要同时保留来源痕迹和变更痕迹。

## 6. 这条规则在实践里到底是为了什么

这个闸门的目的不是为了让流程看起来更严格。

它真正防的是另一件事：让还没收敛的综合结果，悄悄伪装成“已经坐实的知识”。在长期运行的 wiki 里，真正危险的从来不是一页弱页面，而是这页弱页面在后续 ingest 和 query 里又被重新引用，好像它早就已经稳定了一样。

## 7. 上游依据与项目扩展

这篇文档与 Karpathy 风格的 source-grounded workflow 是兼容的，但这里的 `draft/stable` 闸门是本项目额外加上的治理规则，不是上游文本中的逐字原句。

可对照的上游参考：

1. Karpathy 原始 gist：<https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f>
2. Karpathy gist raw：<https://gist.githubusercontent.com/karpathy/442a6bf555914893e9891c11519de94f/raw/ac46de1ad27f92b28ac95459c782c07f6b8c964a/llm-wiki.md>
3. Astro-Han/karpathy-llm-wiki：<https://github.com/Astro-Han/karpathy-llm-wiki>
4. xoai/sage-wiki：<https://github.com/xoai/sage-wiki>
