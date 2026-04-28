---
name: aiwfx-wrap-milestone
description: Closes an aiwf milestone — verifies all ACs met, runs scoped doc-lint, finalizes the tracking doc, promotes status to done, prepares the wrap commit. Use when the user says "wrap M-NNN" or "finish the cache milestone" and self-review per `aiwfx-start-milestone` has passed. Commit and push require explicit human approval.
---

# aiwfx-wrap-milestone

Closes a milestone. Verifies completeness, reconciles the tracking doc, promotes the milestone to `done`, prepares the single wrap commit.

## When to use

The milestone's implementation is complete and self-reviewed (`aiwfx-start-milestone` step 6 ran clean). The user says: *"wrap M-NNN"*, *"finish M-007"*, *"close out the cache milestone"*.

If the milestone isn't actually done — failing tests, unmet ACs, broken build — stop and report. Don't paper over.

## Workflow

### 1. Verify completion

- Re-read the milestone spec. Walk every AC. Confirm each has at least one test that exercises it green.
- Re-read the tracking doc. Confirm every AC is checked off.
- Run the full test suite. **All pass.**
- Run the project's build. **Green.**
- Run any project-specific lint or type-check. Clean.

If anything is red, stop and report. Wrap does not paper over failure.

### 2. Final code review

- Skim for `TODO` / `FIXME` left behind. If they're intentional, document them in the tracking doc's `## Reviewer notes` section. If they're unintentional, fix or open as gaps (`aiwf add gap --title "..." --discovered-in M-NNN`).
- Skim for debug code, commented-out blocks, scratch logging. Remove.
- Confirm public-API or schema changes are reflected in README, inline docs, or wherever the project publishes its surface.

### 3. Doc-lint sweep (scoped)

Invoke `wf-doc-lint` against the milestone's change-set (every file the milestone branch touched since diverging from its base). Append the report to the tracking doc's `## Doc findings` section.

If the report is clean, note "doc-lint: clean" and continue. If findings:

- **Broken code references** — fix in this milestone, or open a gap.
- **Removed-feature docs** — same.
- **Orphan files / TODOs** — record under `## Reviewer notes` for the reviewer to consider; don't block wrap.

`wf-doc-lint` reports only — it does not rewrite prose. Any prose changes happen here as deliberate edits.

### 4. Finalize the tracking doc

- `**Completed:** <today>`.
- `**Commits:** <SHA list>` — every commit on the milestone branch.
- `## Acceptance criteria` — every AC checked.
- `## Work log` — final entry per AC; one-line outcomes with commit SHAs.
- `## Validation` — paste the test-suite and build results.
- `## Deferrals` — list any work this milestone deliberately punted; for each, **open a gap entity** so it survives:

  ```bash
  aiwf add gap --title "<deferred-work>" --discovered-in M-NNN
  ```

  Then mirror the resulting `G-NNN` id under `## Deferrals`.

- `## Decisions made during implementation` — confirm every mid-flight decision is captured (each should already have an `ADR-NNNN` or `D-NNN` from `aiwfx-record-decision` invocations during work).

### 5. Promote the milestone status

```bash
aiwf promote M-NNN done
```

aiwf validates `in_progress → done`, rewrites frontmatter, commits with `aiwf-verb: promote` trailers. The promote commit is *separate* from the implementation commits — it captures the moment of closure.

### 6. Update the roadmap

```bash
aiwf render roadmap --write
```

The roadmap reflects the milestone's new status without hand-edits.

### 7. Stage all changes and prepare the wrap commit

```bash
git add work/tracking/M-NNN-<slug>.md
git status
git diff --staged --stat
```

Draft a conventional commit message: `feat(<scope>): <one-line summary> (M-NNN)`.

### 8. 🛑 Commit gate

Show the user:
- `git diff --staged --stat`
- The proposed commit message.
- A summary of what landed: AC count green, doc-lint summary, deferrals opened (with gap ids).

**Stop and wait for explicit "commit" approval.**

### 9. After commit approval

```bash
git commit -m "<approved-message>"
```

### 10. 🛑 Push gate

Confirm with the user before pushing. Then:

```bash
git push -u origin milestone/M-NNN-<slug>
```

Open the PR if the project's flow is PR-driven. Reference the milestone id in the PR title.

### 11. After merge

- If the project uses an epic-integration branch, merge the milestone branch into the epic branch (`--no-ff` to preserve the milestone shape).
- Delete the milestone branch on origin.
- Run `aiwf render roadmap --write` once more if the merge introduced any state aiwf would notice.

### 12. Record learnings

Append to `work/agent-history/builder.md` (or whichever agent drove the work):

- Patterns that worked (testing approaches, code organization).
- Pitfalls encountered (build issues, test flakiness, API quirks).
- Conventions established or discovered.

Keep entries concise (2–5 lines each). If the file exceeds ~200 lines, summarize older entries.

## Constraints

- 🛑 **Never commit or push without explicit human approval** (steps 8, 10).
- All ACs must be green before wrap proceeds. Wrap does not bury failure.
- Branch-coverage hard rule applies — re-run the audit if any code changed since `aiwfx-start-milestone`'s self-review.
- Deferrals must be captured as gaps. Don't leave deferred work as a tracking-doc bullet that nothing else points at.

## Anti-patterns

- *Wrapping with red tests.* Either fix the tests, escalate the AC failure, or cancel the milestone (`aiwf cancel M-NNN`). Don't wrap broken work as done.
- *Silent deferrals.* Every "we'll do that later" gets a gap entity.
- *Skipping doc-lint.* Doc drift compounds; the milestone wrap is the cheap moment to catch it.
- *Slipping unrelated code into the wrap commit.* If the change isn't part of this milestone, it's a separate `wf-patch`.

## Next step

If this is the last milestone in the epic: → `aiwfx-wrap-epic E-NN`.
Otherwise: → `aiwfx-start-milestone <next-M>`.
