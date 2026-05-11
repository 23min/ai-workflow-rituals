# Changelog

All notable changes to this marketplace are recorded here.

The format follows [Keep a Changelog](https://keepachangelog.com/en/1.1.0/), and the marketplace itself is versioned per-plugin via Claude Code's plugin auto-versioning (every commit produces a new version while `version` is omitted from `plugin.json`).

## [Unreleased]

### Changed

- **Planning skills prompt merge-to-main at closure** (closes aiwf G-0082). `aiwfx-plan-epic` and `aiwfx-plan-milestones` now end with a strong-recommendation merge-to-main prompt so settled planning data lands on main rather than staying hostage on a long-lived ritual branch. `aiwfx-plan-epic` bifurcates its closing (continue to milestones → defer merge to plan-milestones' end; stop here → merge prompt fires now); `aiwfx-plan-milestones` adds an unconditional step 10. Both skills assume a single-checkout (not worktree) planning flow, prompt the in-place `git checkout main && git merge --ff-only <ritual-branch>` shape, and require an explicit reason on decline.
- **aiwf I2 alignment** (kernel-side: `ai-workflow-v2#poc/aiwf-v3` commits 9e5079c…1f2cd91). Acceptance criteria are now a kernel-validated section of the milestone spec — `aiwf add ac M-NNN --title "..."`, composite ids `M-NNN/AC-N`, the `acs-shape` / `acs-tdd-audit` / `milestone-done-incomplete-acs` finding codes, the `aiwf promote --phase` flow. The plugin shrinks to match:
    - `skills/wf-tdd-cycle/SKILL.md` — drives `aiwf promote M-NNN/AC-<N> --phase <p>` at red/green/(refactor/)done so the timeline shows up in `aiwf history`. Drops the "update tracking doc" record step in favor of the milestone spec's `## Work log` section.
    - `skills/aiwfx-start-milestone/SKILL.md` — preflight now verifies `acs[]` is populated and the `tdd:` policy is intentional. The "scaffold the tracking doc" step is gone (the milestone spec carries the wrap-side sections directly). Implementation step routes phase changes through `wf-tdd-cycle`, then promotes the AC to `met` via the kernel; the kernel records the timeline.
    - `skills/aiwfx-wrap-milestone/SKILL.md` — verifies via `aiwf show` and `aiwf check` that every AC is in a terminal state (no `open`) and the audit is clean. Finalizes the wrap-side sections in the milestone spec itself instead of a separate tracking doc.
    - `templates/milestone-spec.md` — expanded with `tdd: none` + `acs: []` frontmatter slots and the merged `## Work log` / `## Decisions made during implementation` / `## Validation` / `## Deferrals` / `## Reviewer notes` sections.
- `skills/aiwfx-track/` — **removed**. Its convention-pointer role is obsolete: tracking is now a kernel-validated section of the milestone spec, and the kernel's commit trailers (`aiwf-verb`/`aiwf-entity`/`aiwf-to`/`aiwf-force`) make the timeline queryable via `aiwf history`. Its workflow guidance moved into `aiwfx-start-milestone` and `aiwfx-wrap-milestone`.
- `templates/tracking-doc.md` — **removed**. Sections merged into `templates/milestone-spec.md`. The milestone spec is now the single document that travels through draft → in_progress → done.

### Fixed

- `templates/adr.md`, `templates/decision.md`, and `skills/aiwfx-record-decision`: removed `date:` and `decided_by:` from frontmatter — aiwf core's parser is strict (`KnownFields(true)`) and rejects unknown fields, so the templates as previously written caused `aiwf check` and `aiwf add adr/decision` to fail. The two fields now live as a body block-quote header (`> **Date:** ... · **Decided by:** ...`) just under the `# ADR-NNNN` / `# D-NNN` heading. The canonical timestamp + actor are also recoverable via `aiwf history <id>`.

### Added

- Initial marketplace scaffolding: `.claude-plugin/marketplace.json` listing two plugins (`aiwf-extensions`, `wf-rituals`).
- Plugin manifests at `plugins/aiwf-extensions/` and `plugins/wf-rituals/`.
- README describing the install path, the two plugins, and the coupling-boundary discipline.
- `wf-rituals` plugin: `wf-patch`, `wf-tdd-cycle`, `wf-review-code`, `wf-doc-lint` skills (aiwf-free by design).
- `aiwf-extensions` plugin: 8 skills (`aiwfx-plan-epic`, `aiwfx-plan-milestones`, `aiwfx-start-milestone`, `aiwfx-wrap-milestone`, `aiwfx-wrap-epic`, `aiwfx-release`, `aiwfx-track`, `aiwfx-record-decision`), 4 role agents (`planner`, `builder`, `reviewer`, `deployer`), 5 templates (`epic-spec`, `milestone-spec`, `tracking-doc`, `adr`, `decision`).
