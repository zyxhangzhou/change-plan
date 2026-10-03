# change-plan

**English** | [简体中文](README.zh-CN.md)

[![Version](https://img.shields.io/github/v/tag/zyxhangzhou/change-plan?label=version)](https://github.com/zyxhangzhou/change-plan/tags)
[![License: MIT](https://img.shields.io/badge/license-MIT-blue.svg)](LICENSE)
[![Claude Code skill](https://img.shields.io/badge/Claude%20Code-skill-d97757)](https://docs.claude.com/en/docs/claude-code/skills)

A [Claude Code](https://claude.com/claude-code) skill that keeps a **versioned plan** for any requirement too big for one change.

> Status: **v0.1, experimental.** It has been used on real work, but only a few times. Expect the template to change.

## The problem

You ask for a feature. The agent analyses it and proposes a split: change A, change B, change C. Then you start building, and the plan starts moving:

- a discovery turns A into two pieces,
- a bug found while testing becomes a new change,
- a premise turns out wrong and C is no longer needed.

Two weeks later the conversation is full of labels like "P0-A second half", "F4" and "original #3". Nobody, human or agent, can say which plan is current or why it changed. Each new session re-derives the plan from scratch, or works from a stale one.

## What the skill does

It writes the plan down as a folder of versioned Markdown files, and keeps it current:

```
mydoc/plans/checkout-redesign/
├── README.md                  current version + version chain
├── checkout-redesign-v1.md    frozen, with a "superseded by v2" banner
└── checkout-redesign-v2.md    current
```

- **Versions for structural changes only.** A change added, split, merged, dropped, reordered or re-scoped, or a premise overturned, makes a new version, with a "changes since vN" table saying what moved and why. Status updates (PR opened, merged) are edited in place, so you don't end up at v9 by Friday.
- **Permanent change IDs.** `A` stays `A`. A split becomes `A.1`, `A.2`, and the parent row stays with `split → A.1, A.2`. An inserted change takes the next unused letter. Every ID you have ever seen still resolves in the current version.
- **Session provenance.** Each version and each status line records the Claude Code session ID, so `claude --resume <id>` takes you back to the conversation that made the decision. The plan still states every finding in full, because transcripts expire.
- **Cheap to resume.** A new session reads the README and the current version only, then checks the plan against merged PRs before starting.
- **Language of your choice.** Plan prose can be in any language; IDs, refs and file names stay English.

See [`examples/checkout-redesign/`](examples/checkout-redesign/) for a made-up plan that goes from v1 to v2 with a split, an insertion and a drop.

## Install

```bash
git clone https://github.com/zyxhangzhou/change-plan ~/.claude/skills/change-plan
```

Restart Claude Code. `/change-plan` should appear in the skill list.

## Use

| When | Say |
|---|---|
| Starting a multi-change requirement | `/change-plan <feature-slug>`, then describe the requirement |
| Something reshapes the plan mid-way | "Update the plan: we found X" (the skill writes vN+1) |
| A change ships | "PR #123 merged, tick the plan" |
| A new session picks up the track | "Resume the `<slug>` plan" |

The agent can also reach for the skill on its own, but mid-task revisions and post-merge ticks are the steps most often skipped. Say it explicitly, or add a line to your `CLAUDE.md` asking the agent to tick the plan whenever a planned change ships.

## Configure

Edit [`config.md`](config.md):

- `plans_root` (default `mydoc/plans`): where plan folders go. Plans are personal working notes between you and the agent, so keep this folder out of git (`.gitignore` or `.git/info/exclude`). The skill checks this the first time it creates the folder.
- `default_language` (default `en`): the language of plan prose. Override per plan with `/change-plan <slug> zh`; each plan remembers its language in its README.

## Works well with

Any unit of work you ship: a branch, a PR, a ticket, or an [OpenSpec](https://github.com/Fission-AI/OpenSpec) change. The plan owns the breakdown and order; each change's own spec or ticket owns its detailed scope.

## License

[MIT](LICENSE)
