# Lint Playbook
[中文](./03-lint-playbook.zh-CN.md) | **English**

> Read this page when you need to decide what to check first, what to check later, and how to keep lint from turning into repeated wasted work.

## 1. The Basic Order

Run lint in this order:

1. `property`
2. `link`
3. `content`

This order is not arbitrary. It exists because the later checks are more expensive and more misleading if the earlier shape problems are still broken.

## 2. Why This Order Saves Time

If the page structure is broken, content checks are premature.

If links are broken, content checks produce noise.

If both structure and links are healthy, then content checks become worth the effort.

That is why the practical sequence is:

1. clear structural issues first
2. clear navigational issues second
3. judge meaning last

## 3. Suggested Rhythm

Use lint as a rhythm, not as a one-time event.

1. After each ingest, run fast `property + link` lint first.
2. Run fuller `content lint` on a broader cadence.
3. Keep snapshots before and after major changes so diff and rollback stay easy.

## 4. Hotspots Worth Extra Attention

Some files deserve more suspicion than others.

1. `log.md` and `index.md` often become the longest and busiest operational pages.
2. Newly added entity/concept pages with many fresh relations deserve one extra `read/grep` check.
3. Suspected engineering-noise paths should be checked explicitly instead of waiting for them to become content corruption.

## 5. Path And Shell Safety

Lint is also where repository hygiene protects the wiki from tooling mistakes.

1. Avoid unescaped spaces, single quotes, and double quotes in new paths by default.
2. If spaces or quotes are unavoidable, the scripts must quote them consistently.
3. Do not concatenate raw paths unsafely.
4. Add path-risk scans for:
   - suspected unexpanded variables such as `$(...)`
   - path concatenation patterns with spaces or quotes but without safe handling
5. When path risk appears, fix the script safety issue first and resume batch ingest/query second.

## 6. What This Playbook Is Trying To Prevent

The real enemy is not lint cost by itself. The real enemy is re-check cost.

When lint order is wrong, teams spend time judging content that will later need to be re-judged after structure and links are repaired. A good lint playbook reduces that wasted loop.

## 7. Useful References

1. ShellCheck repository: <https://github.com/koalaman/shellcheck>
2. ShellCheck SC2086: <https://github.com/koalaman/shellcheck/wiki/SC2086>
3. ShellCheck SC2046: <https://github.com/koalaman/shellcheck/wiki/SC2046>
4. Karpathy original gist: <https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f>
