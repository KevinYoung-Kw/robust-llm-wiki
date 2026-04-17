# Lint Playbook
**中文** | [English](./03-lint-playbook.md)

> 当你拿不准“先检查什么、后检查什么，才不会把 lint 做成重复劳动”时，请看这篇。

## 1. 最基本的顺序

Lint 建议按这个顺序执行：

1. `property`
2. `link`
3. `content`

这个顺序不是拍脑袋定出来的。它的存在，是因为后面的检查更贵，而且如果前面的结构问题还没解决，后面的判断往往会失真。

## 2. 为什么这个顺序更省时间

如果页面结构还是坏的，内容检查就做早了。

如果链接还是坏的，内容检查就会制造噪音。

只有结构和链接都先被整理干净，内容检查才值得投入。

所以更实用的顺序一直是：

1. 先清结构问题
2. 再清导航问题
3. 最后再判断内容

## 3. 建议节奏

不要把 lint 当成一次性动作，而要把它当成一种节奏。

1. 每次 ingest 之后，先跑快速 `property + link` lint。
2. `content lint` 用更宽的周期去跑。
3. 对重大改动保留前后快照，方便 diff 和 rollback。

## 4. 哪些热点值得额外注意

不是所有文件都同样值得怀疑。

1. `log.md` 和 `index.md` 通常会逐渐变成长且忙的操作热点。
2. 新增了大量关系的 entity/concept 页面，值得多加一轮 `read/grep` 快检。
3. 疑似工程噪声路径要专项检查，而不是等它真的污染内容后再发现。

## 5. 路径与 Shell 安全

Lint 还承担另一件事：用仓库卫生去防止工具链错误污染 wiki。

1. 新增路径时，默认避免未转义空格、单引号和双引号。
2. 如果业务上必须保留空格或引号，脚本层就必须统一正确 quoting。
3. 不要用不安全的方式拼接原始路径。
4. 路径风险扫描至少覆盖：
   - 疑似未展开变量，例如 `$(...)`
   - 含空格或引号、但没有被安全处理的路径拼接模式
5. 一旦出现路径风险，先修脚本安全，再恢复 batch ingest/query。

## 6. 这篇 playbook 真正想避免什么

真正的敌人并非 lint 本身的成本，回查成本才是问题所在。

只要 lint 顺序错了，团队就会在结构还没修好的时候过早判断内容，最后又不得不重新判断一遍。一个好的 lint playbook，目的就是减少这种白白浪费掉的重复回路。

## 7. 可参考来源

1. ShellCheck 仓库：<https://github.com/koalaman/shellcheck>
2. ShellCheck SC2086：<https://github.com/koalaman/shellcheck/wiki/SC2086>
3. ShellCheck SC2046：<https://github.com/koalaman/shellcheck/wiki/SC2046>
4. Karpathy 原始 gist：<https://gist.github.com/karpathy/442a6bf555914893e9891c11519de94f>
