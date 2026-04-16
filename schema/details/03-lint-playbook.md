# Lint Playbook (Detailed)
[中文](./03-lint-playbook.zh-CN.md) | **English**

## Background

1. As Wiki page volume grows, manual checking gets slower and slower.
2. If lint order is wrong, repeated checking increases significantly.
3. The goal of this document is to finish required checks in the fewest rounds.

## Recommended Order

1. Property lint
2. Link lint
3. Content lint

## Why This Order

1. Property lint is the fastest; clear structural issues first.
2. Link lint in step two catches broken links and isolated pages early.
3. Content lint is last because semantic checks are the most time-consuming.

## What Happens If You Ignore This Order

1. If you start from content checks, you waste time on broken structures.
2. If link issues are not fixed first, content judgment generates false positives.
3. You still need to go back and recheck later, which means repeated work.

## Suggested Rhythm

1. After each ingest, run fast `property + link` first.
2. Run full `content lint` periodically.
3. Keep lint snapshots before and after major changes for diff and rollback.

## High-Risk Hotspot Hints (From Real Samples)

1. `log.md` and `index.md` are usually the longest pages and should be in the high-frequency check list.
2. Add dedicated checks for suspected engineering-noise paths (for example abnormal directory names containing `$(...)`).
3. For newly added entity/concept pages with many new relations, add one extra `read/grep` quick check to reduce old-error spread risk.

## Path And Shell Safety Checks (New)

1. For new directories/files, avoid unescaped spaces, single quotes, and double quotes by default.
2. If business needs require spaces or quotes, scripts must use consistent path quoting and must not concatenate raw paths unsafely.
3. Extend lint with path-risk scanning:
   - suspected unexpanded variables (for example `$(...)`)
   - path concatenation patterns with spaces/quotes but without safe handling
4. When path risk is found, fix script safety first, then continue batch ingest/query work.

## Source Basis (External Gist/Repo + Project Inference)

Direct external sources:

1. ShellCheck repository: <https://github.com/koalaman/shellcheck>
2. ShellCheck rule SC2086 (double-quote to prevent word splitting/globbing): <https://github.com/koalaman/shellcheck/wiki/SC2086>
3. ShellCheck rule SC2046 (quote command substitution output): <https://github.com/koalaman/shellcheck/wiki/SC2046>
4. Karpathy original gist (workflow baseline): <https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f>

Inference note:

1. The concrete path naming constraints here (spaces/quotes policy and lint checks) are project-level hardening rules derived from shell safety best practices plus observed risks in our Wiki samples.
