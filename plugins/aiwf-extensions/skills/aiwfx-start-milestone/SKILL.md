---
name: aiwfx-start-milestone
description: Sets up and begins an aiwf milestone — preflight checks, branch setup, tracking-doc scaffolding, status promotion to in_progress, then iterative TDD via wf-tdd-cycle. Use when the user says "start milestone M-NNN" or "implement M-NNN" and a draft milestone spec exists. Commits and pushes require explicit human approval.
---

# aiwfx-start-milestone

Begins implementation of an existing milestone. Promotes status, sets up the branch, scaffolds the tracking doc, and hands off to `wf-tdd-cycle` for each acceptance criterion.

## When to use

A milestone spec exists at `work/epics/E-NN-<slug>/M-NNN-<slug>.md` with status `draft`. The user says: *"start M-NNN"*, *"implement the cache milestone"*, *"begin M-007"*.

If the spec doesn't exist or isn't ready, use `aiwfx-plan-milestones` first.

## Workflow

### 1. Preflight

- Read the milestone spec. Confirm every AC is concrete and testable. If any AC is vague, stop and ask the user to refine before starting work.
- Read the parent epic's spec for context.
- Read prior milestone specs in the same epic if this milestone builds on them.
- Run the project's build. **Confirm green** before introducing any change.
- Run the project's tests. **Confirm green.**

If anything is red before you start, stop. Don't begin a milestone on a broken baseline.

### 2. Promote status to `in_progress`

```bash
aiwf promote M-NNN in_progress
```

aiwf validates the transition (`draft → in_progress` is legal), rewrites frontmatter, produces one commit with `aiwf-verb: promote` trailers.

### 3. Branch setup

If the project uses an epic integration branch:

```bash
git checkout -b epic/E-NN-<slug> origin/main      # if missing
git push -u origin epic/E-NN-<slug>
```

Then the milestone branch:

```bash
git checkout -b milestone/M-NNN-<slug>            # from epic branch if epics are integration-batched, otherwise from main
```

If the project lands milestones directly on `main` via PR (no epic-integration branch), skip the epic-branch step and create `milestone/M-NNN-<slug>` from `main`.

### 4. Scaffold the tracking doc

Create `work/tracking/M-NNN-<slug>.md` from this plugin's `templates/tracking-doc.md`. Fill in:

- `**Started:** <today>`
- `**Branch:** milestone/M-NNN-<slug>`
- `**Spec:** work/epics/E-NN-<slug>/M-NNN-<slug>.md`
- The `## Acceptance criteria` section: list each AC from the spec as an unchecked checkbox.

Don't commit the tracking doc yet — the wrap commit (`aiwfx-wrap-milestone` step) bundles it. The doc is a working document, edited continuously through the milestone.

See `aiwfx-track` for the full convention this doc follows.

### 5. Implementation — iterate via `wf-tdd-cycle`

For each AC, in sequence:

- Invoke `wf-tdd-cycle` (red → green → refactor).
- Update the tracking doc's `## Work log` section with the cycle's outcome.
- Check off the AC in `## Acceptance criteria` when its tests pass green and the cycle's branch-coverage audit is clean.

If a decision surfaces mid-implementation that wasn't pre-locked in the spec, invoke `aiwfx-record-decision` to capture it. Mirror the decision id under the tracking doc's `## Decisions made during implementation` section.

### 6. Self-review before declaring complete

Run a self-review pass before invoking `aiwfx-wrap-milestone`:

- Re-read the milestone spec; confirm every AC has at least one passing test.
- Run the **branch-coverage audit** from `wf-tdd-cycle` — every reachable conditional branch in the diff has an explicit test. This is a hard rule.
- Run through the `wf-review-code` checklist mentally (correctness, edge cases, conventions, no unrelated changes).
- If the project has its own end-to-end smoke procedure, run it.

Fix anything you find before declaring done.

### 7. Hand off to wrap

When self-review is clean, declare:

> *"Implementation complete. <N> tests passing, build green, branch-coverage audit clean, self-review passed. Ready for `aiwfx-wrap-milestone`."*

Do not commit the implementation yet — `aiwfx-wrap-milestone` bundles the implementation, the tracking-doc updates, and the milestone-status closure into a single approved sequence.

## Constraints

- 🛑 **Never commit or push without explicit human approval.** Every commit gate is the human's, not the AI's.
- 🛑 **Branch-coverage hard rule** (see `wf-tdd-cycle`). Audit runs before declaring complete, not after the human asks.
- Tests must be deterministic. No clock, no network, no flakes shipped.
- Build must be green before declaring done.
- Follow existing code conventions. Prefer minimal changes — don't refactor unrelated code along the way.

## Anti-patterns

- *Promoting to `in_progress` before preflight passes.* If the baseline is broken, fix it under a `wf-patch` first, then start the milestone.
- *Skipping the tracking doc.* The doc is the audit trail of mid-flight decisions and the work log. Don't reconstruct it after the fact.
- *Mixing milestones.* One milestone per branch. Don't fold "while I was here" work into the diff.
- *Skipping the branch-coverage audit.* "I'll catch it in review" doesn't catch it.

## Next step

→ `aiwfx-wrap-milestone M-NNN` after self-review is clean.
