# Changelog

All notable changes to this marketplace are recorded here.

The format follows [Keep a Changelog](https://keepachangelog.com/en/1.1.0/), and the marketplace itself is versioned per-plugin via Claude Code's plugin auto-versioning (every commit produces a new version while `version` is omitted from `plugin.json`).

## [Unreleased]

### Fixed

- `templates/adr.md`, `templates/decision.md`, and `skills/aiwfx-record-decision`: removed `date:` and `decided_by:` from frontmatter — aiwf core's parser is strict (`KnownFields(true)`) and rejects unknown fields, so the templates as previously written caused `aiwf check` and `aiwf add adr/decision` to fail. The two fields now live as a body block-quote header (`> **Date:** ... · **Decided by:** ...`) just under the `# ADR-NNNN` / `# D-NNN` heading. The canonical timestamp + actor are also recoverable via `aiwf history <id>`.

### Added

- Initial marketplace scaffolding: `.claude-plugin/marketplace.json` listing two plugins (`aiwf-extensions`, `wf-rituals`).
- Plugin manifests at `plugins/aiwf-extensions/` and `plugins/wf-rituals/`.
- README describing the install path, the two plugins, and the coupling-boundary discipline.
- `wf-rituals` plugin: `wf-patch`, `wf-tdd-cycle`, `wf-review-code`, `wf-doc-lint` skills (aiwf-free by design).
- `aiwf-extensions` plugin: 8 skills (`aiwfx-plan-epic`, `aiwfx-plan-milestones`, `aiwfx-start-milestone`, `aiwfx-wrap-milestone`, `aiwfx-wrap-epic`, `aiwfx-release`, `aiwfx-track`, `aiwfx-record-decision`), 4 role agents (`planner`, `builder`, `reviewer`, `deployer`), 5 templates (`epic-spec`, `milestone-spec`, `tracking-doc`, `adr`, `decision`).
