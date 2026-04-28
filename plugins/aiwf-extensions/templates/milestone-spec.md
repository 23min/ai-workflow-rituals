---
id: M-NNN
title: <imperative title>
parent: E-NN              # required: the parent epic id
status: draft             # aiwf milestone statuses: draft | in_progress | done | cancelled
depends_on: []            # optional: prior milestone ids the DAG depends on
completed:                # optional: YYYY-MM-DD, filled at wrap
---

# M-NNN — <Milestone Title>

## Goal

<1–2 sentences: what this milestone achieves.>

## Context

<!-- 2–3 sentences: what exists before this milestone, what must be in place, why now.
     Prior milestones, blocking dependencies resolved, decisions landed.
     Not a re-telling of the epic. -->

<What exists before this milestone? What prior milestones does it build on? Why now?>

## Acceptance criteria

<!-- Each AC must be observable behavior, not an implementation detail.
     Good:  "When X occurs, the system emits Y with property Z."
     Bad:   "X is tested." / "Refactor complete." / "Feature implemented."
     Number ACs so they can be referenced as AC1, AC2 in commits and tracking. -->

1. <Testable criterion — pass/fail, no ambiguity>
2. <Testable criterion>

## Constraints

- <Non-negotiable invariants, banned shortcuts, shim-policy exceptions with a named removal trigger>

## Design notes

- <Locked decisions approved before implementation. Reference ADRs by id (ADR-NNNN) or aiwf decisions (D-NNN)>

## Surfaces touched (optional)

<!-- 1–5 items, not an exhaustive file dump. A pointer so an implementer knows where to
     start reading. Omit for small or obvious milestones. -->

- <path or module>

## Out of scope

- <What this milestone explicitly does NOT do>

## Dependencies

- <Prior milestone, external dep, decision record — what must exist before starting>

## Coverage notes (optional)

<!-- Reachable branches the implementation deliberately leaves untested, with the reason.
     The wf-tdd-cycle branch-coverage hard rule expects every reachable branch to have a
     test. Genuinely unreachable branches (defensive null checks the type system already
     guarantees, etc.) are documented here. -->

- <branch> — <why it can't be reached>

## References

- <ADRs (ADR-NNNN), aiwf decisions (D-NNN), related specs, external docs>
