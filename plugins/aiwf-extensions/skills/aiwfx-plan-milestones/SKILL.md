---
name: aiwfx-plan-milestones
description: Decomposes an approved aiwf epic into a sequenced set of independently-shippable milestones. Allocates each M-NNNN via `aiwf add milestone --epic E-NNNN`, scaffolds each milestone spec from the plugin's template, sequences them by dependency. Use after `aiwfx-plan-epic` when the user says "break this into milestones" or "plan the work for E-NNNN".
---

# aiwfx-plan-milestones

Decomposes an existing epic into milestones. The skill drives the conversation about *what to ship in what order*; aiwf owns id allocation and per-milestone commits.

## When to use

An epic spec exists. The user says: *"break E-NNNN into milestones"*, *"plan the work for the auth epic"*, *"sequence the milestones for X"*.

If the epic doesn't exist yet, use `aiwfx-plan-epic` first.

## Workflow

1. **Read the epic spec.** Open `work/epics/E-NNNN-<slug>/epic.md`. Understand:
   - The goal — what the epic is delivering.
   - The scope (in / out).
   - The constraints — what each milestone must respect.
   - The success criteria — what "done" looks like at epic close.

2. **Decompose into milestones.** Each milestone:
   - Is **independently shippable**. After M-0001 lands, the system is in a coherent state even if M-0002 never runs.
   - Has clear, **testable acceptance criteria**.
   - Targets **1–3 days of focused work**. If a candidate is bigger, split it. If smaller, fold it into a sibling.
   - Has explicit dependencies (or none). Forward-flowing — M-0002 may depend on M-0001; never the reverse.

3. **Sequence them.** Foundational first. Group related work; don't scatter concerns. Identify any milestones that can be parallelized (no dependency between them).

4. **Allocate each milestone via aiwf.** For each one in sequence:

   ```bash
   aiwf add milestone --epic E-NNNN --title "<imperative title>"
   ```

   `aiwf` allocates the next free `M-NNNN` (global, not epic-scoped), creates `work/epics/E-NNNN-<slug>/M-NNNN-<slug>.md` with the minimal body skeleton, sets `parent: E-NNNN` in frontmatter, and produces one commit per milestone with `aiwf-verb: add` trailers.

5. **Replace each milestone's body with the rich template** at this plugin's `templates/milestone-spec.md`. Fill in:
   - **Goal** — 1–2 sentences.
   - **Context** — what exists before; what must be in place; why now.
   - **Acceptance criteria** — testable, numbered (AC1, AC2, …).
   - **Constraints** — non-negotiable invariants for *this* milestone.
   - **Design notes** — locked decisions; reference ADRs by id.
   - **Out of scope** — what this milestone explicitly does NOT do.
   - **Dependencies** — prior milestones, external deps, decision records.

   Frontmatter (`id`, `parent`, `status: draft`) was set by `aiwf add` — don't touch.

6. **Add `depends_on:` to milestone frontmatter when relevant.** If `M-0005` depends on `M-0003`, edit `M-0005`'s frontmatter:

   ```yaml
   depends_on: [M-0003]
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
- *Inventing global ordering when only local matters.* If M-0003 and M-0004 don't depend on each other, leave their order soft.
- *Scope creep mid-decomposition.* If decomposition surfaces work that wasn't in the epic, decide: amend the epic spec (and re-confirm with the user) or capture as a gap (`aiwf add gap`) for later.

## Next step

→ `aiwfx-start-milestone <M-NNNN>` for the first milestone in the sequence.
