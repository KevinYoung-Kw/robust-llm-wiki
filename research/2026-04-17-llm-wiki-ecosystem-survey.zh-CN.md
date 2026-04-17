# Robust LLM-Wiki 调研（2026-04-17）
**中文** | [English](./2026-04-17-llm-wiki-ecosystem-survey.md)

> 调研范围：Karpathy `llm-wiki` 原文 + GitHub 仓库生态 + GitHub Gist 生态 + 你当前两个 vault 的结构观察。

## 1) Karpathy `llm-wiki` 的核心模式（事实）

来自原始 gist 的核心共识：

1. 与其说是“每次提问都重算”的 RAG，不如说是“持续编译”的持久 wiki。
2. 三层架构：`raw sources`（只读真值）/ `wiki`（LLM 维护）/ `schema`（规则与流程）。
3. 三个操作闭环：`ingest` / `query` / `lint`。
4. `index.md` 和 `log.md` 是最低成本导航层。
5. 高价值问答应回写 wiki，形成知识复利。

补充观察：
- 截至页面抓取时，Karpathy gist 显示 `5,000+` stars、`4,259` forks（页面显示值）。

## 2) GitHub 仓库生态快照（事实）

数据来源：`research/snapshots/repos_llm_wiki.json`、`repos_llm_dot_wiki.json`、`repos_obsidian_llm_wiki.json`。

| Repo | Stars | Updated | URL | 备注 |
|---|---:|---|---|---|
| Lum1104/Understand-Anything | 8412 | 2026-04-16 | https://github.com/Lum1104/Understand-Anything | 知识图谱可视化方向，强调从代码/知识库构建图导航 |
| SamurAIGPT/llm-wiki-agent | 1964 | 2026-04-16 | https://github.com/SamurAIGPT/llm-wiki-agent | “drop in sources, auto-maintain wiki” 方向 |
| nashsu/llm_wiki | 1463 | 2026-04-16 | https://github.com/nashsu/llm_wiki | 桌面化/产品化的 LLM-Wiki |
| AgriciDaniel/claude-obsidian | 1461 | 2026-04-16 | https://github.com/AgriciDaniel/claude-obsidian | Obsidian + Agent 协作 |
| atomicmemory/llm-wiki-compiler | 515 | 2026-04-16 | https://github.com/atomicmemory/llm-wiki-compiler | compile-first 路线 |
| lucasastorian/llmwiki | 447 | 2026-04-16 | https://github.com/lucasastorian/llmwiki | 开源实现 + MCP 连接 |
| Astro-Han/karpathy-llm-wiki | 438 | 2026-04-16 | https://github.com/Astro-Han/karpathy-llm-wiki | Agent 工作流兼容化 |
| Ar9av/obsidian-wiki | 409 | 2026-04-16 | https://github.com/Ar9av/obsidian-wiki | Obsidian wiki 维护框架 |
| xoai/sage-wiki | 408 | 2026-04-16 | https://github.com/xoai/sage-wiki | CLI + compile/query/lint/serve |
| skyllwt/OmegaWiki | 266 | 2026-04-16 | https://github.com/skyllwt/OmegaWiki | 全生命周期研究平台方向 |

## 3) GitHub Gist 生态快照（事实）

数据来源：`research/snapshots/gist_forks.json`、`gist_comments.json`。

### 3.1 Fork 扩散（样本）

Karpathy gist 在发布后快速出现大量 fork（以下为抓取样本中的前 10 条）：

| Owner | Date | Fork Gist |
|---|---|---|
| danieldietrich | 2026-04-04 | https://gist.github.com/danieldietrich/18fea7cc8590f81d63b9ebad366065ae |
| thewertzgroup | 2026-04-04 | https://gist.github.com/thewertzgroup/e7581540e0d9522cba2f92c33dd80fa1 |
| darkolorin | 2026-04-04 | https://gist.github.com/darkolorin/49e63d4bb847d592db167552e7d6ee9a |
| pedromandelli | 2026-04-04 | https://gist.github.com/pedromandelli/25a61c1af45d39cce2e0fbdbb660861b |
| nanbada | 2026-04-04 | https://gist.github.com/nanbada/2fa37ca361b804feb33379ab3e90de05 |
| kinjidesu | 2026-04-04 | https://gist.github.com/kinjidesu/32d72484d229ae545c6e0dffacb64f01 |
| i1r0 | 2026-04-04 | https://gist.github.com/i1r0/7526290f5fba1f8ccc6ee66ca1dc622f |
| higanand | 2026-04-04 | https://gist.github.com/higanand/c95a25a4ca56b4d5fecd52e58a43e91b |
| Dnamb | 2026-04-04 | https://gist.github.com/Dnamb/1d2b19ea5dbc42a7b15e55b0f86fda0e |
| puneetagg | 2026-04-04 | https://gist.github.com/puneetagg/de0f8c513bb77db9a3a8b6c8413f732e |

### 3.2 评论区出现的实现方向（样本）

从评论正文抽取到的 GitHub 链接显示，生态分化大概有四类：

1. 工作流封装：如 `Astro-Han/karpathy-llm-wiki`、`SamurAIGPT/llm-wiki-agent`。
2. CLI 编译器：如 `xoai/sage-wiki`、`VihariKanukollu/browzy.ai`。
3. 记忆中间层/数据库路线：如 `mpazik/binder`、`lobu-ai/owletto`。
4. 多 Agent 协作与可验证路线：如 `Arrmlet/tracecraft`、`scopeblind/scopeblind-gateway`。

## 4) 你当前两个 vault 的结构观察（事实）

### 4.1 仓库规模

- `Vault A（路径已脱敏）`
  - Markdown 文件数：`1736`
  - 顶层文件：`18`
  - 顶层目录：`10`
- `Vault B（路径已脱敏）`
  - Markdown 文件数：`1345`
  - 顶层文件：`4`
  - 顶层目录：`13`

### 4.2 结构性风险信号

1. 路径命名存在 shell 风险：例如目录名包含空格与 `'`，脚本不严谨时容易出错。
2. 发现异常目录名片段（疑似脚本变量未正确展开）：`$(cd "$SCRIPT_DIR`。
3. 顶层同时存在 `AGENTS.md` 与 `CLAUDE.md`（在一个 vault 内），容易造成规则源冲突。
4. 顶层混入大量 lint 产物/中间文件，主干知识区与工具产出未完全隔离。
5. `raw/wiki/scripts/回收站` 等多区并存，但缺少统一生命周期约束（入库、归档、删除、回滚策略）。

## 5) Robust LLM-Wiki 的设计建议（推断）

1. 单一规则源：每个 vault 只保留一个主 schema（如 `AGENTS.md`），其它工具文档引用它。
2. 强 frontmatter 契约：页面至少含 `status/source_ids/last_reviewed`，并按项目需要扩展 `topic` 等字段。
3. 写入闸门：无来源 claim 禁止写入 `stable` 区。
4. 双轨页面：`draft` 与 `stable` 分层，禁止 query 直接覆盖 `stable`。
5. 统一日志：所有 ingest/query/lint 都必须 append 到 `wiki/log.md`。
6. 自动 lint 周期化：至少每周一次，产出断链/孤儿页/过时页报告。
7. 目录隔离：`tmp/`、`reports/`、`scripts/` 与 `wiki/` 分离，避免污染知识主干。
8. 变更可回滚：批量写入采用“计划 -> 预览 -> 提交”三段式。
9. 路径规范：禁止新目录含未转义空格/引号；脚本统一做路径 quote。
10. 先小后大：先 5-10 个来源稳定流程，再扩展自动化。

## 6) 本次调研数据文件

- `research/snapshots/repos_llm_wiki.json`
- `research/snapshots/repos_llm_dot_wiki.json`
- `research/snapshots/repos_obsidian_llm_wiki.json`
- `research/snapshots/gist_forks.json`
- `research/snapshots/gist_comments.json`

## 7) 主要外部来源链接

- Karpathy 原始 gist: https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f
- Karpathy gist raw: https://gist.githubusercontent.com/karpathy/442a6bf555914893e9891c11519de94f/raw/ac46de1ad27f92b28ac95459c782c07f6b8c964a/llm-wiki.md
- GitHub Search API: https://api.github.com/search/repositories
- GitHub Gist API: https://api.github.com/gists/442a6bf555914893e9891c11519de94f/forks
- GitHub Gist Comments API: https://api.github.com/gists/442a6bf555914893e9891c11519de94f/comments
