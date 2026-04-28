---
name: aiwfx-track
description: Describes the convention for milestone tracking documents under aiwf. Tracking docs live at `work/tracking/M-NNN-<slug>.md`, are scaffolded by `aiwfx-start-milestone`, updated continuously through implementation, and finalized at `aiwfx-wrap-milestone`. Advisory only — aiwf does not validate tracking docs. Read this skill when authoring or updating a tracking doc and you want the convention.
---

# aiwfx-track

Tracking documents are *not* aiwf entities. They have no id allocated by aiwf, no status FSM, no validators. They are free-form markdown narratives kept alongside an in-progress milestone — the audit trail of what happened, what was decided mid-flight, what got deferred.

This skill is the convention. Aiwf doesn't enforce any of it.

## Where they live

`work/tracking/M-NNN-<slug>.md`

The slug matches the milestone's slug (the part after `M-NNN-` in the milestone spec's filename). One tracking doc per milestone. Not per epic, not per AC.

If the project's planning state lives elsewhere (a different folder convention), use the project's location instead. The framework default is `work/tracking/`.

## Lifecycle

| When | Who | What happens |
|---|---|---|
| `aiwfx-start-milestone` step 4 | The skill | Scaffolds the doc from `templates/tracking-doc.md`. Header is filled (started date, branch, spec path); ACs are mirrored as unchecked checkboxes; everything else is a placeholder. |
| Throughout implementation | The builder agent / human | Append entries to `## Work log` per AC. Check off ACs as they go green. Capture mid-flight decisions under `## Decisions made during implementation` (each linked to an `ADR-NNNN` or `D-NNN` from `aiwfx-record-decision`). |
| Mid-implementation, when work surfaces that's deferred | The builder | Open a gap (`aiwf add gap --title "..." --discovered-in M-NNN`). Mirror the gap id under `## Deferrals`. |
| `aiwfx-wrap-milestone` steps 4, 7 | The skill | Fills `**Completed:**`, `**Commits:**`, `## Validation`, finalizes `## Reviewer notes`, and bundles the doc into the wrap commit. |

After the milestone is `done`, the tracking doc becomes a frozen historical record. Don't edit it.

## Section structure

The template at this plugin's `templates/tracking-doc.md` is the canonical shape. Sections in order:

1. **Header** — start date, completed date, branch, spec path, commits.
2. **Acceptance criteria** — mirror from the spec; check off as work lands.
3. **Decisions made during implementation** — only mid-flight decisions, *not* pre-locked design notes from the spec. Each decision should reference an ADR or D-NNN id (created via `aiwfx-record-decision`).
4. **Work log** — one entry per AC (preferred) or per meaningful unit of work. Append-only — never rewrite earlier entries.
5. **Reviewer notes** — trade-offs, deliberate omissions, places where the obvious approach was rejected.
6. **Validation** — pipeline / test results captured at wrap.
7. **Doc findings** — `wf-doc-lint` report from the wrap.
8. **Deferrals** — gap ids opened during the milestone.

Sections that don't apply can be empty (`(none)`) or omitted entirely.

## Why aiwf doesn't validate this

The tracking doc is prose. It's not a structured artefact aiwf can mechanically check (and the design principle says aiwf only validates what it can mechanically check). The tracking-doc convention belongs in the rituals layer — described here, scaffolded by `aiwfx-start-milestone`, finalized by `aiwfx-wrap-milestone`. If you skip the convention, aiwf doesn't notice.

That's a feature, not a gap. aiwf's invariants are about identity, references, and status. The tracking doc is about *the story of the work*. Both are valuable; they live in different layers.

## What goes into the tracking doc vs. the spec vs. an ADR/D-NNN

| Goes into | What lives there |
|---|---|
| Milestone spec (`work/epics/.../M-NNN-<slug>.md`) | What the milestone is about. ACs. Constraints. Pre-locked design notes. Out-of-scope. Locked at start; rarely edited mid-flight. |
| Tracking doc (`work/tracking/M-NNN-<slug>.md`) | What happened. AC checkmarks. Work log. Mid-flight decisions (with pointers). Reviewer notes. Validation. Deferrals. Updated continuously. |
| ADR (`docs/adr/ADR-NNNN-<slug>.md`) | Architectural decisions worth keeping for future readers. Created via `aiwfx-record-decision`. |
| D-NNN decision (`work/decisions/D-NNN-<slug>.md`) | Project-scoped decisions tied to an epic or milestone. Created via `aiwfx-record-decision`. |

The split is: the spec is what we said we'd do; the tracking doc is what we did and how it went; the ADR/D-NNN is the durable reasoning for choices that future readers care about.

## Anti-patterns

- *Hand-writing structural status into the tracking doc.* The milestone spec's frontmatter is canonical for status; the tracking doc's `**Completed:**` is filled iff the spec is `done`.
- *Long prose explanations in `## Decisions made during implementation`.* Those go into a real ADR or D-NNN. The tracking doc lists the ids; the doc behind the id holds the reasoning.
- *Letting `## Deferrals` accumulate as bullets that nothing else points at.* Every deferral becomes a gap (`aiwf add gap`); the tracking doc lists the ids.
- *Editing the tracking doc after wrap.* It's a frozen record. Future amendments live in new artefacts.

## Constraints

- 🛑 The tracking doc is a working document, not a contract surface. Don't mistake it for one.
- The spec's frontmatter `status:` is the single source of truth for status. Tracking-doc dates are derived.
