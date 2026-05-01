# ai-workflow-rituals

A Claude Code plugin marketplace shipping engineering-ritual skills and role agents that complement [`aiwf`](https://github.com/23min/ai-workflow-v2).

> Pre-alpha. Marketplace and plugin manifests scaffolded; no skills shipped yet.

## What this is

`aiwf` is a narrow planning kernel: six entity kinds (epic, milestone, ADR, gap, decision, contract), stable IDs, validators, git-trailer history. It deliberately has no opinion about how the work *gets done* — branching, TDD, code review, milestone lifecycle, release ceremonies.

This repo ships those opinions, separately, as Claude Code plugins. Two plugins, both optional:

| Plugin | Contains | When to install |
|---|---|---|
| **aiwf-extensions** | Milestone-lifecycle skills (`aiwfx-start-milestone`, `aiwfx-wrap-milestone`, `aiwfx-wrap-epic`, `aiwfx-release`, `aiwfx-record-decision`, …) plus four role agents (planner, builder, reviewer, deployer) | If you use `aiwf` as your planning kernel and want the milestone-driven engineering loop |
| **wf-rituals** | Generic engineering rituals (`wf-patch`, `wf-tdd-cycle`, `wf-review-code`, `wf-doc-lint`) | If you want the engineering rituals on their own — works on any repo, with or without `aiwf` |

## Install

In a Claude Code session, from inside a consumer repo:

```
/plugin marketplace add 23min/ai-workflow-rituals
/plugin install aiwf-extensions@ai-workflow-rituals
/plugin install wf-rituals@ai-workflow-rituals    # optional
```

Updates: `/plugin update <name>@ai-workflow-rituals`. Auto-update on startup is enabled by default while the marketplace is in active development.

### Choosing an install scope

Claude Code asks where to install. Pick **Project** scope by default. It writes `.claude/settings.json` into the consumer repo, which means:

- Collaborators who clone the repo get the same plugins automatically.
- Your other projects (using v1, a different framework, or none at all) stay clean.
- The team's choice of rituals is reproducible and reviewable in PRs.

**User scope** is only right if you're sure you want these plugins available in *every* project on your machine. **Local scope** (gitignored) is right for short-term experimentation in a single repo.

For the planning kernel itself, see [`ai-workflow-v2`](https://github.com/23min/ai-workflow-v2):

```bash
go install github.com/23min/ai-workflow-v2/tools/cmd/aiwf@latest
aiwf init                # in your consumer repo
```

## Coupling boundary

The two plugins differ in how tightly they couple to `aiwf`. This is deliberate.

- **`aiwfx-*` skills** — call `aiwf` shell verbs, reference `aiwf`'s ID format (`E-NN`, `M-NNN`, …), use `aiwf`'s status vocabulary. By definition coupled. If you don't use `aiwf`, this plugin is dead weight.
- **`wf-*` skills** — speak the language of the work (milestone, acceptance criteria, branch, test, PR) without mentioning `aiwf`, its IDs, its status vocabulary, or its directory layout. By design they survive an `aiwf` swap. They work the same against a repo using `aiwf`, against a repo using a different planning kernel, or against a repo using none.

This is a discipline, not an enforcement. Every `wf-*` skill goes through a coupling-boundary review at port time: *"if `aiwf` were deleted tomorrow, would this skill still make sense?"* If no, it belongs in `aiwfx-*`.

## Repository layout

```
ai-workflow-rituals/
├── .claude-plugin/
│   └── marketplace.json
├── plugins/
│   ├── aiwf-extensions/
│   │   ├── .claude-plugin/plugin.json
│   │   ├── skills/aiwfx-*/SKILL.md
│   │   ├── agents/{planner,builder,reviewer,deployer}.md
│   │   └── templates/
│   └── wf-rituals/
│       ├── .claude-plugin/plugin.json
│       ├── skills/wf-*/SKILL.md
│       └── templates/
├── README.md
└── CHANGELOG.md
```

Each plugin's directory layout follows Claude Code's plugin reference. Contents are auto-discovered.

## Status

| Plugin | Stage |
|---|---|
| Marketplace manifest | scaffolded |
| `aiwf-extensions` plugin manifest | scaffolded |
| `wf-rituals` plugin manifest | scaffolded |
| `aiwf-extensions` skills | not started |
| `aiwf-extensions` agents | not started |
| `aiwf-extensions` templates | not started |
| `wf-rituals` skills | not started |
| `wf-rituals` templates | not started |

The porting plan and the rationale for what's in scope vs. deferred lives in [`ai-workflow-v2/docs/rituals-plugin-plan.md`](https://github.com/23min/ai-workflow-v2/blob/poc/aiwf-v3/docs/rituals-plugin-plan.md).

## License

Apache-2.0. See [`LICENSE`](LICENSE).
