---
name: implement
description: Implement a user-requested feature or bugfix, including tests, exhaustive diff review, verified fixes, and a bounded completion decision.
---

# Implement

## Define

- Read the provided context, repository instructions, and relevant code.
- Clarify only gaps that materially affect the result.
- Agree on acceptance criteria, out-of-scope work, and the exact diff base.

## Plan

- Make a proportional implementation plan.
- For substantial work, present it with `plan-presenter` when available and wait for approval.

## Build

- Implement the smallest coherent change that satisfies the acceptance criteria and repository standards.
- Add focused tests without unrelated refactoring.
- Run the relevant tests, lint, type checks, and build before review.

## Discovery review

Freeze the exact review diff and independently inventory its files, hunks, additions, and deletions. Spawn one independent reviewer with that snapshot, the acceptance criteria, and repository standards; do not give it your conclusions.

The reviewer must:

- Inspect every added, changed, and deleted line, including tests, configuration, migrations, and generated files. Read enough surrounding code and call sites to judge each line.
- Keep a file-and-hunk coverage checklist. Inspect file by file or in smaller batches so output cannot hide lines through truncation.
- Report a finding only when it has a concrete failure scenario and is caused by the diff. A blocker is an evidenced acceptance, correctness, security, data-loss, compatibility, or required-standard violation. Preferences, speculative hardening, unrelated debt, and optional refactors are non-blocking advisories.
- Cite `file:line`, severity, violated criterion or invariant, evidence, and the smallest valid fix for every finding.
- Return `PASS` or `CHANGES NEEDED`, followed by blockers, advisories, and coverage totals: files, hunks, additions, deletions, and any uncovered item.

Compare its coverage to the independent inventory and review any missing batches. Do not accept uncovered lines. Run exactly one discovery review.

## Fix and verify

- If the review passes, do not spawn a fixer.
- Give verified blockers—not advisories—to an independent fixer. The fixer must validate each finding, make minimal fixes, and record fixed, rejected-with-reason, and unresolved items.
- Rerun the relevant checks.
- Freeze a new candidate snapshot, then run targeted verification against the blocker ledger and every line changed from the last reviewed snapshot. It may add a blocker only for a fix regression or an obvious release-critical defect in that scope.
- If verification fails, allow one final fix and targeted verification. If blockers remain, stop and report them without editing again.
- Never start another discovery review for the same implementation.

## Finish

Finish when acceptance criteria and checks pass, all blockers are resolved or explicitly accepted by the user, and review coverage is complete. Advisories do not prevent completion.

Report the changed behavior, checks run, review disposition, residual risks, and practical test steps.
