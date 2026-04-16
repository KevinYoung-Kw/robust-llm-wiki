# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/),
and this project follows [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [Unreleased]

### Added

- Added `schema/README.md` as a start-here guide for the schema docs, with reader paths, top-level document roles, and an explicit reading order.
- Added `schema/README.zh-CN.md` so the schema start-here path also exists for Chinese readers.
- Added `schema/robust-llm-wiki-CLAUDE.zh-CN.md` and rewrote `schema/SPEC.zh-CN.md` so the core schema guidance now has a Chinese mirror in the new narrative style.
- Surfaced the current released schema baseline (`v0.1.1`) inside the schema documentation.

### Changed

- Updated the root READMEs to point to the new schema start-here guide instead of dropping readers directly into one policy file.

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

[Unreleased]: https://github.com/KevinYoung-Kw/robust-llm-wiki/compare/v0.1.1...HEAD
[0.1.1]: https://github.com/KevinYoung-Kw/robust-llm-wiki/compare/v0.1.0...v0.1.1
[0.1.0]: https://github.com/KevinYoung-Kw/robust-llm-wiki/releases/tag/v0.1.0
