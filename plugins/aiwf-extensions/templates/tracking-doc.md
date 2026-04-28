# <Milestone Title> — Tracking

**Started:** <YYYY-MM-DD>
**Completed:** <YYYY-MM-DD or pending>
**Branch:** milestone/<M-id>-<slug>
**Spec:** <path to the milestone spec, e.g. work/epics/E-NN-<slug>/M-NNN-<slug>.md>
**Commits:** <SHA, SHA, SHA>

<!-- Status is not carried here. The milestone spec's frontmatter `status:` field is
     canonical (and is what `aiwf promote` writes to). `**Completed:**` is filled
     iff the spec is `done`. -->

## Acceptance criteria

<!-- Mirror ACs from the spec. Check each when its Work Log entry lands. -->

- [ ] AC1: <criterion>
- [ ] AC2: <criterion>

## Decisions made during implementation

<!-- Decisions that came up mid-work that were NOT pre-locked in the milestone spec.
     For each: what was decided, why, and a link to the ADR or D-NNN entry if one
     was opened (use `aiwfx-record-decision` to author it). If no new decisions
     arose, say "None — all decisions are pre-locked in the milestone spec." -->

- (none)

## Work log

<!-- One entry per AC (preferred) or per meaningful unit of work.
     Header: "AC<N> — <short title>" or "<short title>" if not AC-scoped.
     First line: one-line outcome · commit <SHA> · tests <N/M>
     Optional prose paragraph for non-obvious context: what changed, file:line
     references, why a detour was needed. Append-only — don't rewrite earlier entries. -->

### AC1 — <short title>

<one-line outcome> · commit <SHA> · tests <N/M>

<Optional prose paragraph.>

### <short title for a non-AC unit>

<one-line outcome> · commit <SHA>

## Reviewer notes (optional)

<!-- Things the reviewer should specifically examine — trade-offs, deliberate
     omissions, places where the obvious approach was rejected and why.
     Empty/omitted if none. -->

- (none)

## Validation

- <Run the project's validation pipeline; record pass/fail and deltas from baseline>

## Doc findings (optional)

<!-- If `wf-doc-lint` was run during the wrap, paste its report here for the
     historical record. -->

## Deferrals

<!-- Work observed during this milestone but deliberately not done.
     For each: open a gap entity via `aiwf add gap --title "..." --discovered-in M-NNN`
     so it survives in the planning state, then mirror the gap id here. -->

- (none)
