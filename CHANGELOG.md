# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/),
and this project follows [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [Unreleased]

## [0.1.3] - 2026-04-17

### Changed

- Continued the schema and research refinement work across the current draft set, including updates to the bilingual schema docs and detail pages.
- Tightened the public `research/RESEARCH.md` and `research/RESEARCH.zh-CN.md` benchmark section so the repository now links to original public evaluation sources instead of restating vendor/model score tables.
- Updated schema-facing version markers so the released schema baseline is now tracked as `v0.1.3`.

## [0.1.2] - 2026-04-17

### Added

- Added `schema/README.md` and `schema/README.zh-CN.md` as start-here guides for the schema docs, with reading order, reader paths, and top-level document roles.
- Added `schema/robust-llm-wiki-CLAUDE.zh-CN.md` so the main operator guide now has a Chinese mirror.
- Added root-level `CLAUDE.md` as a concise agent entry that makes the original Karpathy LLM-Wiki baseline explicit before readers dive into repo-specific policy.
- Added bilingual `quickstart/` guides for two real onboarding paths: adapting the framework into an existing LLM-Wiki, or generating a fresh agent operating file from this repository.

### Changed

- Rewrote `schema/details/01-07` and their Chinese mirrors into a more narrative, easier-to-follow style while preserving the same framework boundaries and operating intent.
- Strengthened `schema/robust-llm-wiki-CLAUDE.md` and its Chinese mirror with a concrete explanation of the Karpathy baseline, including the role of `raw sources`, `wiki`, `schema`, `index.md`, and `log.md`.
- Updated the root READMEs so the Quick Start now points to the new onboarding path before sending readers deeper into the schema docs.
- Updated schema-facing version markers so the released schema baseline is now tracked as `v0.1.2`.

## [0.1.1] - 2026-04-17

### Changed

- Reworked `README.md` and `README.zh-CN.md` to make the repository entry points clearer and easier to scan.
- Strengthened `schema/robust-llm-wiki-CLAUDE.md` with clearer operating guidance for agent teams, including per-file ownership, promotion gates, lint cadence, risk classes, model allocation guardrails, and compliance requirements.
- Improved schema-facing documentation flow so the schema entry points and reading order are more obvious for new readers.

## [0.1.0] - 2026-04-17

### Added

- Initial public release for `robust-llm-wiki`.
- `research/` and `schema/` baseline documents.
- Bilingual README, contributing, and i18n documentation.
- GitHub issue and pull request templates.

### Changed

- Removed `skill/` directory and related README references for this release.
- Applied anonymization updates in research and schema detail documents.

### Notes

- Workflow implementation content is intentionally deferred and will be introduced in a future version.

[Unreleased]: https://github.com/KevinYoung-Kw/robust-llm-wiki/compare/v0.1.3...HEAD
[0.1.3]: https://github.com/KevinYoung-Kw/robust-llm-wiki/compare/v0.1.2...v0.1.3
[0.1.2]: https://github.com/KevinYoung-Kw/robust-llm-wiki/compare/v0.1.1...v0.1.2
[0.1.1]: https://github.com/KevinYoung-Kw/robust-llm-wiki/compare/v0.1.0...v0.1.1
[0.1.0]: https://github.com/KevinYoung-Kw/robust-llm-wiki/releases/tag/v0.1.0
