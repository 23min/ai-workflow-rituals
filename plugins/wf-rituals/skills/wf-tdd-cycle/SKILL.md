---
name: wf-tdd-cycle
description: Red/green/refactor for a single acceptance criterion or feature unit, ending with a hard-rule branch-coverage audit. Write a failing test, write the minimum code to pass, refactor, then walk every reachable conditional branch and confirm an explicit test exercises it. Use during milestone implementation and inside `wf-patch` when the change touches logic.
---

# wf-tdd-cycle

A single iteration of test-first development for one acceptance criterion or one focused feature unit. Ends with a branch-coverage audit that is a **hard rule**, not a guideline.

## When to use

- The user is implementing one acceptance criterion of an in-progress milestone.
- The user is on a `wf-patch` branch and the change touches logic (not pure config / dependency bumps).
- Any other moment where a unit of behavior change wants a test before it has code.

If you find yourself running `wf-tdd-cycle` for a config nudge, you don't need it.

## The cycle

### RED — Write the failing test first

- Write the test(s) that describe the expected behavior. Test names follow the project's convention; if there is none, prefer `MethodName_Scenario_ExpectedResult` (or the language-idiomatic equivalent).
- Use the project's test framework. Don't introduce a new one mid-cycle.
- Mock or stub external dependencies (network, clock, filesystem if the test isn't about the filesystem). Tests must be deterministic.
- Run the tests. Confirm they **fail for the right reason** — the test reaches the assertion and the assertion is the thing that fails. A test that errors on import or fails on a typo isn't red yet.

### GREEN — Make it pass with the minimum code

- Write the smallest code change that turns the failing test green.
- Don't add features the test doesn't require. If you find yourself thinking "while I'm here…", stop — that's the next cycle.
- Run the full test suite. Confirm the new test passes *and* nothing else broke.

### REFACTOR — Clean up

- Remove duplication introduced by the green step.
- Improve names that became wrong as the code grew.
- Extract methods or types if shape demands it.
- Run tests after every meaningful refactor. Stay green.

### RECORD — Update where progress lives

- Mark the acceptance criterion done (in whatever the project uses to track AC progress — a tracking doc, an issue, a checklist).
- Note any decisions or deviations made mid-cycle.
- If the project has no AC-tracking habit, skip — don't invent one.

## Branch-coverage audit (HARD RULE — runs before declaring done)

Before declaring this cycle complete, you walk every reachable conditional branch in the diff and confirm an explicit test exercises each side. **Saying "every branch covered" without performing the audit is the failure mode this rule exists to prevent.**

### How to audit

- Open each new or changed source file.
- Walk it line by line.
- For every `if`/`else`/`switch`/`case`/`catch`/`?:`/early-return/short-circuit, identify which test exercises each side.
- If a branch has no test, write one. **Defensive paths count** — if a guard, an exception catch, or a malformed-input handler ships, it gets a test.
- If a helper is private and the branch is hard to reach via the public API, expose it to tests using the language's friend-assembly or package-private mechanism (C# `internal` + `InternalsVisibleTo`, Rust `pub(crate)`, Python `_internal` + explicit import, Java/Go package-private). Then write a direct test.
- Genuinely unreachable branches (e.g., a defensive `null` check on a value the type system guarantees non-null) are documented where the project records such things. Include the reason.

### What "reachable" means

A branch is reachable if any caller of the function — direct or transitive — can produce inputs that select it. The compiler can't prove unreachability for most defensive code. **Default to reachable; require a written reason to call something unreachable.**

## Anti-patterns

- *Writing code before the test.* The test that comes after the code is verification, not specification.
- *Writing tests that can't fail.* If you can comment out the assertion and the test still passes, the test is broken.
- *Skipping the refactor step.* Green-then-rush is how the codebase rots.
- *Testing implementation details.* Tests should describe behavior; private internals are leverage points, not assertion targets.
- *Tests that depend on execution order.* Each test owns its state.
- *Declaring "every branch covered" without performing the audit.* See the hard rule above.

## Test-quality checklist

Before declaring done, every new test passes:

- Deterministic (no randomness, no real clock, no real network).
- Independent (no shared mutable state with other tests).
- Covers edge cases (null, empty, boundary values, error paths).
- Named so the reader knows what's being tested without reading the body.

## Constraints

- 🛑 **Branch-coverage audit is a hard rule.** It runs before the commit-approval prompt of whatever calling skill invoked this cycle, not after a human asks. If the calling skill (e.g., `wf-patch`) is about to ask "commit?", the audit must already be complete.
- Tests must be deterministic. No flakes shipped.
- The cycle ends green. Never leave a branch with a red test you'll "fix in the next cycle."
