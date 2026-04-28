---
name: aiwfx-wrap-epic
description: Closes an aiwf epic — verifies all milestones done, scaffolds a wrap artefact, harvests ADR candidates, runs scoped doc-lint, merges the epic branch into mainline, promotes the epic to done. Use when the user says "wrap E-NN" or "close the auth epic" and every milestone in the epic is wrapped. Commit and push require explicit human approval.
---

# aiwfx-wrap-epic

Closes an epic. The epic itself is a coordination unit — closing it means: every milestone is `done`, the integration branch merges to mainline, the wrap artefact captures what shipped and what didn't, and the epic's status flips to `done`.

## Principles

- **Wrap is closure, not release.** Tagging, packaging, publishing — those are `aiwfx-release`. This skill ends the planning unit.
- **Branch cleanup is opt-in.** Local branches are preserved (so `tig` / `gitk` keep labelling history); origin branches for completed milestones are deleted to reduce remote refname clutter.
- **Nothing is deleted at wrap.** Specs, tracking docs, the wrap artefact — all stay readable forever. Closure is a status change, not a deletion.

## Precondition

1. Every milestone in this epic has `status: done`. Run `aiwf check` and verify; if any are still `in_progress` or `draft`, stop and surface them.
2. The epic branch (if used) is up to date — every milestone's final merge commit is on it.
3. Working tree clean.
4. Integration target identified (usually `main`).

If any precondition fails, stop and report. Do not improvise around an unfinished epic.

## Workflow

### 1. Scaffold the wrap artefact

Create `work/epics/E-NN-<slug>/wrap.md` (staged, not yet committed):

```markdown
# Epic wrap — E-NN

**Date:** <today>
**Closed by:** <actor>
**Integration target:** main
**Epic branch:** epic/E-NN-<slug>
**Merge commit:** <SHA — filled at step 5>

## Milestones delivered

- M-NNN — <title> (merged <short-sha>)
- M-NNN — <title> (merged <short-sha>)

## Summary

Two to four sentences on what shipped and why. Reference the goal from the epic spec; honest about what scope shifted mid-flight.

## ADRs ratified

- ADR-NNNN — <slug>          (or "none")

## Decisions captured

- D-NNN — <slug>             (or "none")

## Follow-ups carried forward

- G-NNN — <slug>             (gap that survives the epic)

## Handoff

What is ready for the next epic; what is deliberately left open.
```

Use **reference-phrasing for any list-derived count** ("every ADR listed in *ADRs ratified*" rather than "all 4 ADRs"). Avoids drift.

### 2. ADR check — harvest decisions worth keeping

Walk the epic's commits. For each candidate decision, ask: *"Would a future reader regret missing the reasoning?"* Signals an ADR is warranted:

- A default changed or a new default introduced.
- A strategy considered and rejected.
- A scope cut or framing shift affecting downstream work.
- A supersession of a prior ADR.

For each candidate, invoke `aiwfx-record-decision` and choose ADR (architectural, durable) or D-NNN (project-scoped, more local). Record the resulting ids in the wrap artefact's `## ADRs ratified` or `## Decisions captured` section.

### 3. Doc-lint sweep (scoped)

Invoke `wf-doc-lint` against the epic's change-set (every file touched on `epic/E-NN-<slug>` since it diverged from the integration target).

Append the report to `wrap.md` under a `## Doc findings` section. If findings include broken references or removed-feature docs, fix or open as gaps before proceeding. `wf-doc-lint` reports only — prose fixes are deliberate edits here.

### 4. Promote the epic to `done`

```bash
aiwf promote E-NN done
```

aiwf validates `active → done`, rewrites frontmatter, commits with `aiwf-verb: promote`. (If the epic is still `proposed`, that means no milestone ever started — wrap doesn't apply. Investigate.)

Add `completed: <YYYY-MM-DD>` to the epic spec frontmatter as a separate edit (the date stamp is yours; aiwf owns `status:`).

### 5. 🛑 Merge gate — merge epic branch into integration target

Confirm with the user. Then:

```bash
git checkout main
git pull --ff-only origin main
git merge --no-ff epic/E-NN-<slug>
```

`--no-ff` preserves the epic as a single merge commit; full history is recoverable via `git log <merge-sha>^2`. Record the merge SHA in `wrap.md`.

**Do not push yet.**

### 6. Stage the wrap artefact and any spec edits

```bash
git add work/epics/E-NN-<slug>/wrap.md
git add work/epics/E-NN-<slug>/epic.md     # for the `completed:` date
git status
```

### 7. 🛑 Commit gate

Show the user:
- The wrap artefact summary.
- `git diff --staged`.
- The proposed wrap commit message: `chore(E-NN): wrap epic — <one-line summary>`.

**Stop and wait for "commit" approval.**

### 8. After commit approval

```bash
git commit -m "<approved-message>"
```

### 9. 🛑 Push gate

Confirm. Then:

```bash
git push origin main
```

### 10. Branch cleanup (origin only)

Plan the deletions first. List the milestone and epic branches to delete. For each, verify it's merged:

```bash
git branch -r --merged main | grep "milestone/M-NNN"
git branch -r --merged main | grep "epic/E-NN"
```

If a branch isn't shown as merged, stop and report — don't force.

After explicit approval per branch (or batch approval for the full list), delete on origin only:

```bash
git push origin --delete milestone/M-NNN-<slug>
git push origin --delete epic/E-NN-<slug>
```

Local branches are not touched. Operators prune local branches on their own schedule.

### 11. Update the roadmap

```bash
aiwf render roadmap --write
```

### 12. Record learnings

Append to `work/agent-history/<agent>.md` (whoever closed the epic): patterns that worked across the epic, pitfalls, conventions established. 2–5 line entries. Roll up older history if the file exceeds ~200 lines.

## Constraints

- 🛑 **Never merge or push without explicit approval** (steps 5, 9, 10).
- Every milestone must be `done` before wrap — `aiwf check` and `aiwf history E-NN` confirm.
- Branch-cleanup is origin-only. Do not delete local branches.
- The wrap artefact is mandatory. Don't close an epic without one.

## Anti-patterns

- *Wrapping while a milestone is still `in_progress`.* Run `aiwf check` first.
- *Force-deleting an unmerged branch.* Reconcile the work or the name; don't force.
- *Slipping a code change into the wrap commit.* If the change is real, it's a milestone or a `wf-patch`.
- *Skipping the ADR harvest.* The window to record "why we did it this way" closes when the team forgets.
- *Pushing before approval.*

## Out of scope

Releases, changelogs, version tags, package publishing, deployment. Those belong to `aiwfx-release`.

## Next step

If a release follows: → `aiwfx-release`.
If not: → `aiwfx-plan-epic` for whatever's next, or stop here.
