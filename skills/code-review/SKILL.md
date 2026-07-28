---
name: code-review
description: Exhaustively review every line in a diff from a fixed point along two independent axes—repository standards and the originating spec—with evidenced findings, explicit coverage, and bounded fix verification.
---

# Code Review

Review one immutable diff against:

- **Standards** — repository instructions and documented coding standards.
- **Spec** — the originating issue, PRD, or acceptance criteria.

## Prepare

1. Pin the requested input. Resolve committed endpoints to SHAs; if staged or working-tree changes are included, capture one patch snapshot. Give every reviewer that same immutable diff. Stop on invalid or empty input.
2. Find the spec from commit references, a user-provided path, or matching files under `docs/`, `specs/`, or `.scratch/`. If none exists, mark the Spec axis unavailable.
3. Find applicable repository instructions and standards such as `AGENTS.md`, `CONTRIBUTING.md`, and `CODING_STANDARDS.md`.
4. Inventory the complete diff: files, hunks, additions, deletions, renames, and binary files. Never rely on one possibly truncated diff output.

## Review

Spawn independent Standards and Spec reviewers in parallel. Give each the exact diff command, commit list, applicable sources, and the rules below. Skip only the unavailable Spec reviewer.

Each reviewer must:

- Inspect every added, changed, and deleted line in every file, including tests, docs, configuration, migrations, lock/generated files, renames, and binaries. Read surrounding code and call sites where needed.
- Maintain a file-and-hunk checklist against the inventory. Inspect file by file or in smaller batches so output cannot hide lines through truncation. Do not finish with an unchecked line or hunk.
- Report only issues introduced by the diff. A blocker requires evidence of an acceptance, correctness, security, data-loss, compatibility, or explicit required-standard violation. Style preferences, generic code smells, speculative hardening, optional refactors, and unrelated debt are non-blocking advisories.
- For every finding, provide severity, `file:line`, the violated spec/rule or invariant, a concrete failure scenario, evidence, and the smallest valid fix. Do not report vague possibilities.
- Return `PASS` or `CHANGES NEEDED`, then blockers, advisories, and coverage totals. `PASS` means zero blockers, not zero possible improvements.

Compare both coverage reports with the inventory. If anything is missing, review only the missing batches before reporting; an incomplete review cannot pass.

## Report

Present `## Standards` and `## Spec` separately, followed by `## Coverage` with totals and uncovered items. End with blocker and advisory counts per axis. Do not let advisories change a pass into failure.

Recommend `FIX BEFORE PROCEEDING`, `CONSIDER FIXING`, or `PROCEED` from the evidenced severity, confidence, and risk, and name the findings that drive the recommendation. The review is read-only: the user decides whether to fix. Ask for that decision and do not edit or dispatch a fixer without explicit approval.

## Review after approved fixes

If the user approves fixes, capture a new candidate snapshot and verify it against the prior reviewed snapshot:

- Check the original blocker ledger and every line changed by the fixes.
- Allow new blockers only for fix-induced regressions or obvious release-critical defects within that verification scope.
- Do not reopen general discovery or add new advisories.
- Stop when the ledger is resolved and coverage and relevant checks pass.

Reuse the prior result for the same base, candidate, spec, and scope. Start new discovery only after a material change to one of them; repeated requests do not reopen an unchanged diff.
