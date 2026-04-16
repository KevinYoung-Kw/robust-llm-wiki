# Lint Playbook（详细）
**中文** | [English](./03-lint-playbook.md)

## 背景

1. 当 Wiki 页面规模变大后，手工检查会越来越慢。
2. 如果 lint 顺序不对，会出现大量重复检查。
3. 这个文档的目标是：用最少轮次完成必要检查。

## 建议顺序

1. Property lint
2. Link lint
3. Content lint

## 为什么按这个顺序

1. Property lint 最快，先把结构问题清掉。
2. Link lint 第二步做，可以尽早发现断链和孤岛页。
3. Content lint 最后做，因为语义检查最耗时。

## 不按顺序会发生什么

1. 直接做内容检查，会在坏结构上浪费时间。
2. 链接问题没先修，会让内容判断出现误报。
3. 最后还是要回头补查，等于重复工作。

## 建议节奏

1. 每次 ingest 后，先跑快速 `property + link`。
2. 周期性跑全量 `content lint`。
3. 重大改动前后保留 lint 快照，便于对比和回滚。

## 高风险位点提示（来自真实样本）

1. `log.md` 与 `index.md` 通常是最长页面，建议进入高频检查清单。
2. 对疑似工程噪声路径做专项检查（例如包含 `$(...)` 的异常目录名）。
3. 对“刚新增关联较多”的 entity/concept 页增加一轮 `read/grep` 快检，降低旧误差扩散风险。

## 路径与 Shell 安全检查（新增）

1. 新增目录或文件命名，默认避免未转义空格、单引号、双引号。
2. 如业务必须保留空格或引号，脚本层必须统一做路径 quote，禁止裸拼接路径。
3. lint 增加路径风险扫描：
   - 疑似未展开变量（如 `$(...)`）
   - 含空格/引号但未被安全处理的路径拼接
4. 发现路径风险后，先修脚本安全性，再继续批量 ingest/query 任务。

## 来源依据（外部 Gist/Repo + 本项目推断）

外部可定位来源：

1. ShellCheck 仓库：<https://github.com/koalaman/shellcheck>
2. ShellCheck 规则 SC2086（避免未加引号导致的分词/通配扩展）：<https://github.com/koalaman/shellcheck/wiki/SC2086>
3. ShellCheck 规则 SC2046（命令替换结果应加引号）：<https://github.com/koalaman/shellcheck/wiki/SC2046>
4. Karpathy 原始 gist（工作流基线）：<https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f>

口径说明：

1. 本节关于路径命名与 quote 的具体约束属于本项目安全加固规则，基于 shell 最佳实践与样本风险观察落地，不是某一上游来源的逐字条款。
