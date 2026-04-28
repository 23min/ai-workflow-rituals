---
name: aiwfx-plan-milestones
description: Decomposes an approved aiwf epic into a sequenced set of independently-shippable milestones. Allocates each M-NNN via `aiwf add milestone --epic E-NN`, scaffolds each milestone spec from the plugin's template, sequences them by dependency. Use after `aiwfx-plan-epic` when the user says "break this into milestones" or "plan the work for E-NN".
---

# aiwfx-plan-milestones

Decomposes an existing epic into milestones. The skill drives the conversation about *what to ship in what order*; aiwf owns id allocation and per-milestone commits.

## When to use

An epic spec exists. The user says: *"break E-NN into milestones"*, *"plan the work for the auth epic"*, *"sequence the milestones for X"*.

If the epic doesn't exist yet, use `aiwfx-plan-epic` first.

## Workflow

1. **Read the epic spec.** Open `work/epics/E-NN-<slug>/epic.md`. Understand:
   - The goal — what the epic is delivering.
   - The scope (in / out).
   - The constraints — what each milestone must respect.
   - The success criteria — what "done" looks like at epic close.

2. **Decompose into milestones.** Each milestone:
   - Is **independently shippable**. After M-1 lands, the system is in a coherent state even if M-2 never runs.
   - Has clear, **testable acceptance criteria**.
   - Targets **1–3 days of focused work**. If a candidate is bigger, split it. If smaller, fold it into a sibling.
   - Has explicit dependencies (or none). Forward-flowing — M-2 may depend on M-1; never the reverse.

3. **Sequence them.** Foundational first. Group related work; don't scatter concerns. Identify any milestones that can be parallelized (no dependency between them).

4. **Allocate each milestone via aiwf.** For each one in sequence:

   ```bash
   aiwf add milestone --epic E-NN --title "<imperative title>"
   ```

   `aiwf` allocates the next free `M-NNN` (global, not epic-scoped), creates `work/epics/E-NN-<slug>/M-NNN-<slug>.md` with the minimal body skeleton, sets `parent: E-NN` in frontmatter, and produces one commit per milestone with `aiwf-verb: add` trailers.

5. **Replace each milestone's body with the rich template** at this plugin's `templates/milestone-spec.md`. Fill in:
   - **Goal** — 1–2 sentences.
   - **Context** — what exists before; what must be in place; why now.
   - **Acceptance criteria** — testable, numbered (AC1, AC2, …).
   - **Constraints** — non-negotiable invariants for *this* milestone.
   - **Design notes** — locked decisions; reference ADRs by id.
   - **Out of scope** — what this milestone explicitly does NOT do.
   - **Dependencies** — prior milestones, external deps, decision records.

   Frontmatter (`id`, `parent`, `status: draft`) was set by `aiwf add` — don't touch.

6. **Add `depends_on:` to milestone frontmatter when relevant.** If `M-005` depends on `M-003`, edit `M-005`'s frontmatter:

   ```yaml
   depends_on: [M-003]
   ```

   `aiwf check` validates `depends_on` is a real DAG (no cycles).

7. **Update the epic's Milestones list.** Edit the epic spec to list all milestones in execution order. Use the format from the epic template — link, one-line description, dependencies.

8. **Update `ROADMAP.md`** by running:

   ```bash
   aiwf render roadmap --write
   ```

9. **Confirm the sequence with the user.** Walk through the milestone list together. Identify any scope adjustments before drafting begins.

## What this skill does NOT do

- Does not draft individual milestone specs in deep detail — that happens just-in-time when each milestone is started (via `aiwfx-start-milestone`). This skill produces the milestone *list and shape*, not the full spec body for milestones not yet started.
- Does not promote any milestone past `draft`. Promotion happens at `aiwfx-start-milestone`.

## Anti-patterns

- *Front-loading detail.* Don't write 10 fully-specced milestones up front. Spec details rot fast; AC definitions written 6 weeks before the work starts are usually wrong.
- *Inventing global ordering when only local matters.* If M-3 and M-4 don't depend on each other, leave their order soft.
- *Scope creep mid-decomposition.* If decomposition surfaces work that wasn't in the epic, decide: amend the epic spec (and re-confirm with the user) or capture as a gap (`aiwf add gap`) for later.

## Next step

→ `aiwfx-start-milestone <M-NNN>` for the first milestone in the sequence.
