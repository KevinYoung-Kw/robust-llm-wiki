# Robust LLM-Wiki Survey (2026-04-17)

[中文](./2026-04-17-llm-wiki-ecosystem-survey.zh-CN.md) | **English**

> Scope: Karpathy `llm-wiki` source text + GitHub repository ecosystem + GitHub Gist ecosystem + structure observations from your two current vaults.

## 1) Karpathy `llm-wiki` Core Pattern (Facts)

Core consensus from the original gist:

1. This is not "recompute everything every query" RAG; it is a persistent Wiki that is continuously compiled.
2. Three-layer architecture: `raw sources` (read-only ground truth) / `wiki` (LLM-maintained) / `schema` (rules and process).
3. Three-operation loop: `ingest` / `query` / `lint`.
4. `index.md` and `log.md` are the minimum-cost navigation layer.
5. High-value Q&A should be written back into the Wiki to form knowledge compounding.

Additional observation:
- At the time of snapshot capture, Karpathy's gist page showed `5,000+` stars and `4,259` forks (page-displayed values).

## 2) GitHub Repository Ecosystem Snapshot (Facts)

Data sources: `research/snapshots/repos_llm_wiki.json`, `repos_llm_dot_wiki.json`, `repos_obsidian_llm_wiki.json`.

| Repo | Stars | Updated | URL | Notes |
|---|---:|---|---|---|
| Lum1104/Understand-Anything | 8412 | 2026-04-16 | https://github.com/Lum1104/Understand-Anything | Knowledge-graph visualization direction; graph navigation from code/knowledge base |
| SamurAIGPT/llm-wiki-agent | 1964 | 2026-04-16 | https://github.com/SamurAIGPT/llm-wiki-agent | "drop in sources, auto-maintain wiki" direction |
| nashsu/llm_wiki | 1463 | 2026-04-16 | https://github.com/nashsu/llm_wiki | Desktop/productized LLM-Wiki |
| AgriciDaniel/claude-obsidian | 1461 | 2026-04-16 | https://github.com/AgriciDaniel/claude-obsidian | Obsidian + agent collaboration |
| atomicmemory/llm-wiki-compiler | 515 | 2026-04-16 | https://github.com/atomicmemory/llm-wiki-compiler | Compile-first route |
| lucasastorian/llmwiki | 447 | 2026-04-16 | https://github.com/lucasastorian/llmwiki | Open-source implementation + MCP integration |
| Astro-Han/karpathy-llm-wiki | 438 | 2026-04-16 | https://github.com/Astro-Han/karpathy-llm-wiki | Agent workflow compatibility |
| Ar9av/obsidian-wiki | 409 | 2026-04-16 | https://github.com/Ar9av/obsidian-wiki | Obsidian wiki maintenance framework |
| xoai/sage-wiki | 408 | 2026-04-16 | https://github.com/xoai/sage-wiki | CLI + compile/query/lint/serve |
| skyllwt/OmegaWiki | 266 | 2026-04-16 | https://github.com/skyllwt/OmegaWiki | Full-lifecycle research platform direction |

## 3) GitHub Gist Ecosystem Snapshot (Facts)

Data sources: `research/snapshots/gist_forks.json`, `gist_comments.json`.

### 3.1 Fork Spread (Sample)

Karpathy's gist quickly produced many forks after publishing (top 10 in captured sample):

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

### 3.2 Implementation Directions Seen In Comments (Sample)

GitHub links extracted from comments indicate roughly four ecosystem branches:

1. Workflow-packaging direction: for example `Astro-Han/karpathy-llm-wiki`, `SamurAIGPT/llm-wiki-agent`.
2. CLI compiler route: for example `xoai/sage-wiki`, `VihariKanukollu/browzy.ai`.
3. Memory middleware/database route: for example `mpazik/binder`, `lobu-ai/owletto`.
4. Multi-agent and verifiable pipelines: for example `Arrmlet/tracecraft`, `scopeblind/scopeblind-gateway`.

## 4) Structure Observations From Your Two Current Vaults (Facts)

### 4.1 Repository Scale

- `Vault A (anonymized path)`
  - Markdown files: `1736`
  - Top-level files: `18`
  - Top-level directories: `10`
- `Vault B (anonymized path)`
  - Markdown files: `1345`
  - Top-level files: `4`
  - Top-level directories: `13`

### 4.2 Structural Risk Signals

1. Path naming has shell risk: directory names include spaces and `'`, which can break scripts if quoting is weak.
2. An anomalous directory fragment was found (suspected script variable not expanded): `$(cd "$SCRIPT_DIR`.
3. One vault had both `AGENTS.md` and `CLAUDE.md` at top level, which can create rule-source conflicts.
4. Top level mixed many lint artifacts/intermediate files, and knowledge trunk versus tooling output was not fully isolated.
5. Multiple zones coexisted (`raw/wiki/scripts/recycle-bin`), but without one explicit lifecycle contract (ingest/archive/delete/rollback policy).

## 5) Design Suggestions For Robust LLM-Wiki (Inferred)

1. Single rule source: each vault should keep one primary schema (for example `AGENTS.md`), while other tool docs reference it.
2. Strong frontmatter contract: each page should include at least `status/source_ids/last_reviewed`, with optional project fields like `topic`.
3. Write gate: claims without sources must not enter the `stable` area.
4. Two-track pages: separate `draft` and `stable`; query should not directly overwrite `stable`.
5. Unified logging: every ingest/query/lint run should append to `wiki/log.md`.
6. Recurring lint: run at least weekly and output reports for broken links/orphan pages/outdated pages.
7. Directory isolation: separate `tmp/`, `reports/`, `scripts/` from `wiki/` to avoid trunk pollution.
8. Rollback-ready changes: use a three-stage flow for batch writes: plan -> preview -> commit.
9. Path policy: forbid new directories with unescaped spaces/quotes; require consistent path quoting in scripts.
10. Start small then scale: stabilize with 5-10 sources first, then expand automation.

## 6) Data Files Produced In This Survey

- `research/snapshots/repos_llm_wiki.json`
- `research/snapshots/repos_llm_dot_wiki.json`
- `research/snapshots/repos_obsidian_llm_wiki.json`
- `research/snapshots/gist_forks.json`
- `research/snapshots/gist_comments.json`

## 7) Main External Source Links

- Karpathy original gist: https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f
- Karpathy gist raw: https://gist.githubusercontent.com/karpathy/442a6bf555914893e9891c11519de94f/raw/ac46de1ad27f92b28ac95459c782c07f6b8c964a/llm-wiki.md
- GitHub Search API: https://api.github.com/search/repositories
- GitHub Gist API: https://api.github.com/gists/442a6bf555914893e9891c11519de94f/forks
- GitHub Gist Comments API: https://api.github.com/gists/442a6bf555914893e9891c11519de94f/comments
