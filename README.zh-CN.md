# change-plan

[English](README.md) | **简体中文**

[![Version](https://img.shields.io/github/v/tag/zyxhangzhou/change-plan?label=version)](https://github.com/zyxhangzhou/change-plan/tags)
[![License: MIT](https://img.shields.io/badge/license-MIT-blue.svg)](LICENSE)
[![Claude Code skill](https://img.shields.io/badge/Claude%20Code-skill-d97757)](https://docs.claude.com/en/docs/claude-code/skills)

一个 [Claude Code](https://claude.com/claude-code) skill：为任何一个 change 装不下的需求维护一份**带版本的计划**。

> 状态：**v0.1，实验阶段。** 已在真实项目中使用过，但次数不多。模板还会变。

## 要解决的问题

你提出一个功能需求。agent 分析之后给出拆分：change A、change B、change C。然后开始开发，计划也开始变：

- 一个新发现让 A 拆成了两块；
- 测试中发现的 bug 变成了一个新 change；
- 某个前提被证明是错的，C 不再需要。

两周后，对话里满是「P0-A 后半」「F4」「原 #3」这样的标签。无论人还是 agent，都说不清哪份计划是最新的、它为什么变了。每个新 session 要么从头重新推导计划，要么照着一份过期的计划干活。

## 这个 skill 做什么

它把计划写成一个文件夹，里面是带版本的 Markdown 文件，并持续保持最新：

```
mydoc/plans/checkout-redesign/
├── README.md                  当前版本 + 版本链
├── checkout-redesign-v1.md    已冻结，带「已被 v2 取代」横幅
└── checkout-redesign-v2.md    当前版本
```

- **只有结构变化才出新版本。** 新增、拆分、合并、作废、重排或重新划定某个 change 的范围，或者某个前提被推翻，才会产生新版本，并附一张「相比 vN 的变化」表，说明改了什么、为什么改。状态变化（PR 已开、已合并）直接在当前版本上修改，免得到周五就出到了 v9。
- **change 编号永久不变。** `A` 永远是 `A`。拆分后变成 `A.1`、`A.2`，父行保留并标注 `split → A.1, A.2`。新插入的 change 取下一个未用过的字母。你见过的每个编号，在当前版本里都还能找到。
- **记录 session 来源。** 每个版本、每条状态记录都写上 Claude Code 的 session ID，`claude --resume <id>` 就能回到做出该决定的那次对话。但计划本身仍完整写明每条发现，因为对话记录会过期。
- **接续成本低。** 新 session 只读 README 和当前版本，开工前先拿计划和已合并的 PR 对一遍。
- **语言可选。** 计划正文可以用任何语言；编号、引用和文件名保持英文。

[`examples/checkout-redesign/`](examples/checkout-redesign/) 是一份虚构的示例计划，从 v1 演进到 v2，包含一次拆分、一次插入和一次作废。

## 安装

```bash
git clone https://github.com/zyxhangzhou/change-plan ~/.claude/skills/change-plan
```

重启 Claude Code，skill 列表里应出现 `/change-plan`。

## 使用

| 场景 | 这样说 |
|---|---|
| 开始一个需要多个 change 的需求 | `/change-plan <feature-slug>`，然后描述需求 |
| 中途有事情改变了计划 | 「更新计划：我们发现了 X」（skill 会写出 vN+1） |
| 某个 change 已交付 | 「PR #123 已合并，更新计划」 |
| 新 session 接手这条线 | 「接续 `<slug>` 计划」 |

agent 也可以自行调用这个 skill，但中途修订和合并后打勾是最常被漏掉的两步。请明确说出来，或在你的 `CLAUDE.md` 里加一行，要求 agent 在计划中的 change 交付后更新计划。

## 配置

编辑 [`config.md`](config.md)：

- `plans_root`（默认 `mydoc/plans`）：计划文件夹的存放位置。计划是你和 agent 之间的个人工作笔记，请把这个文件夹排除在 git 之外（`.gitignore` 或 `.git/info/exclude`）。skill 第一次创建该文件夹时会检查这一点。
- `default_language`（默认 `en`）：计划正文的语言。可以按计划单独覆盖，例如 `/change-plan <slug> zh`；每份计划会在自己的 README 里记住所用语言。

## 搭配使用

适用于你交付的任何工作单元：一个分支、一个 PR、一张工单，或一个 [OpenSpec](https://github.com/Fission-AI/OpenSpec) change。计划负责拆分和顺序；每个 change 的详细范围由它自己的 spec 或工单负责。

## 许可证

[MIT](LICENSE)
