---
name: code-review
description: Review code changes independently against their requirements and project rules. Use when the user asks to review code, a diff, commit, branch, pull request, issue implementation, patch, or current working-tree changes; requests a correctness, regression, security, or maintainability review; or asks to review and fix findings. Produce evidence-based findings with severity and precise locations, verify relevant behavior when possible, and return an explicit verdict. Default to review-only and modify code or external systems only when the user explicitly asks.
---

# Code review

Perform one rigorous, independent review of the assigned changes. Review the implementation,
not the implementer's explanation of it.

Default to review-only. Do not edit files, post comments, resolve threads, update tasks, commit,
push, approve, or merge unless the user explicitly asks.

## 1. Establish the review contract

Identify:

- the review target;
- the base and head of the change;
- the task, issue, specification, or expected behavior;
- the acceptance criteria;
- whether the user wants review-only or review-and-fix.

Accept targets such as:

- the current working-tree diff, including untracked files;
- a supplied patch;
- one or more commits;
- a branch comparison;
- a pull request;
- named files or directories;
- the implementation of a referenced issue.

When the target is omitted and the current project has one obvious set of uncommitted changes,
review those changes. If multiple plausible targets exist, ask which one to review.

Use the user's prompt as the source of scope. If the user references an issue, specification,
or task system, read the referenced material completely. Do not search a backlog or select a
different task.

Treat acceptance criteria as the definition of intended behavior. If formal criteria do not
exist, derive a narrow review contract from the request and authoritative project
documentation. State material assumptions.

## 2. Orient before judging

Read the applicable project instructions and conventions, including relevant:

- `AGENTS.md`, `CLAUDE.md`, `CONTRIBUTING.md`, and `README.md` files;
- architecture, security, and development documentation;
- package manifests, lockfiles, and task-runner configuration;
- CI workflows and required checks;
- specifications and decision records referenced by the task.

Inspect:

- repository and working-tree status;
- the complete diff, including new and deleted files;
- enough surrounding code to understand callers, invariants, and data flow;
- relevant tests, fixtures, snapshots, migrations, generated files, and configuration;
- related history when it clarifies intent or compatibility.

Do not judge a diff in isolation when correctness depends on code outside it. Preserve unrelated
user work.

## 3. Protect reviewer independence

Perform exactly one review round, even when the change appears trivial.

When subagents are available, use a fresh reviewer that did not implement the change. Do not
give it the implementer's reasoning or conclusions. Give it artifacts and requirements:

- the task, title, description, and acceptance criteria verbatim;
- the complete diff and contents of new files;
- applicable project rules;
- relevant verification results;
- project-specific traps or findings referenced by the task.

Ask the reviewer to return findings in the priority order defined in Â§4 and require an explicit
`PASS` or `CHANGES NEEDED` verdict.

When subagents are unavailable, make a deliberate fresh pass: re-read the requirements first,
then inspect the diff without relying on the implementation rationale.

Do not bias the reviewer with suspected answers. Validation is useful only when the reviewer
can reach conclusions from the artifacts.

## 4. Review in priority order

Review in this order:

1. **Correctness and completeness**
   - Check every acceptance criterion.
   - Identify behavior that is missing, only apparently implemented, or contradicted.
   - Trace normal, boundary, and failure paths.

2. **Regressions and project traps**
   - Check behavior outside the immediate happy path.
   - Apply findings and constraints recorded in the task or referenced project documents.
   - Look for silent failures, stale state, incorrect defaults, and compatibility breaks.

3. **Security and data integrity**
   - Check authorization, validation, injection boundaries, secret handling, privacy, unsafe
     deserialization, race conditions, destructive operations, and migration safety when
     relevant.

4. **Project rules and architecture**
   - Check naming, structure, dependencies, generated files, layering, ownership boundaries,
     error handling, and repository-specific constraints.
   - Flag duplicate sources of truth or second write, render, or side-effect sites when the
     architecture requires one canonical path.

5. **Tests and verification quality**
   - Confirm tests exercise the changed behavior rather than merely execute code.
   - Look for missing negative cases, assertions that cannot fail meaningfully, over-mocking,
     nondeterminism, and tests that encode the implementation instead of the requirement.

6. **Anything likely to bite later**
   - Check resource cleanup, concurrency, retries, timeouts, partial failure, exhaustiveness,
     performance cliffs, observability, and unsafe unwrap, force, or assertion behavior on
     normal failure paths when relevant.

7. **Maintainability**
   - Flag unnecessary complexity, duplication, speculative abstractions, oversized changes,
     or dependencies that are unjustified by the task.

Prioritize real behavioral risk over style preferences.

## 5. Validate every finding

Report a finding only when it is concrete, actionable, and caused or exposed by the reviewed
change.

For each finding:

- tag it `critical`, `major`, or `minor`;
- name the file and precise line or smallest useful range;
- state which requirement, invariant, or project rule is violated;
- explain the failing scenario;
- describe the user or system impact;
- give enough evidence for another engineer to verify it;
- suggest the smallest reasonable direction for a fix.

Use these severities:

- **critical** â€” security compromise, data loss or corruption, severe outage, or a change that
  is unsafe to ship.
- **major** â€” unmet acceptance criterion, functional defect, meaningful regression, normal
  failure-path bug, or significant project-rule violation.
- **minor** â€” real but limited defect or maintainability problem that does not block the main
  behavior.

Do not inflate severity. Do not report formatting preferences, vague concerns, pre-existing
problems unrelated to the change, or speculative risks without a plausible failure path.

If evidence is insufficient, label it as a question or verification gap rather than a finding.
Do not let unverified concerns determine the verdict.

## 6. Verify when possible

Discover relevant verification commands from project instructions, package scripts, task
runners, and CI configuration. Run focused checks that can confirm or disprove important
findings, then run required broader checks when practical.

Do not assume a language, framework, package manager, or test command.

In review-only mode, do not change source files to make tests pass. Safe read-only diagnostics
and ordinary test commands are allowed.

Report:

- commands run;
- pass or fail status;
- useful failure output;
- checks not run and why.

Never claim a check passed unless it was actually run. Passing tests do not override a
demonstrated correctness problem.

## 7. Return the review

Put findings first, ordered by severity and then impact. Use this shape:

```markdown
## Findings

- [major] Short finding title â€” `path/to/file.ext:42`
  - Violates: requirement or invariant
  - Scenario: how the problem occurs
  - Impact: what breaks
  - Fix: smallest reasonable direction

## Verdict

CHANGES NEEDED

## Acceptance criteria

- Criterion: satisfied / unmet / not verifiable â€” evidence

## Verification

- `command`: passed / failed
- Not run: reason

## Residual risks

- Anything material that could not be established
```

Use exactly one verdict:

- `PASS` â€” no critical or major finding remains. Minor findings may be present but must be
  explicitly listed as non-blocking.
- `CHANGES NEEDED` â€” at least one critical or major finding exists, or an acceptance criterion
  is unmet.

If the review cannot be performed because the target or required artifacts are unavailable,
report the blocker and do not invent a verdict.

When there are no findings, say so directly. Do not manufacture findings to appear thorough.

## 8. Fix mode

Enter fix mode only when the user explicitly asks to fix, address, or implement review
findings.

When subagents are available, use a separate fixer that did not perform the review. Give it:

- the findings;
- the task and acceptance criteria;
- the complete diff;
- applicable project rules;
- verification results.

Tell the fixer to:

- fix `critical` and `major` findings;
- use judgement on `minor` findings;
- preserve unrelated changes;
- avoid expanding the task;
- push back in its report when a finding is incorrect rather than making a change it
  disagrees with.

When subagents are unavailable, separate the roles deliberately: finish and record the review
before editing, then fix from the recorded findings.

After fixes, rerun the applicable verification. Do not loop review â†’ fix â†’ review. Adjudicate
after the single round:

- Findings fixed and verification passes â†’ report completion.
- Reviewer was wrong â†’ reject the finding and explain why.
- Finding is real but out of scope â†’ report it; create a follow-up only when the user requested
  that workflow.
- Work remains incomplete â†’ state that clearly and do not issue a false pass.

## 9. External actions

A request to review does not authorize external writes.

Do not:

- post review comments;
- submit an approval or request changes;
- resolve review threads;
- update or close an issue;
- create follow-up tasks;
- commit, push, merge, or deploy;

unless the user explicitly requests that action.

When external action is requested, apply it only to the specified target and report what was
actually changed.

## 10. Guardrails

- Never review only the happy path.
- Never substitute passing tests for checking acceptance criteria.
- Never trust comments or names over actual behavior.
- Never omit new, deleted, or untracked files from the review target.
- Never hide uncertainty inside a confident finding.
- Never lower severity merely because a fix is inconvenient.
- Never raise severity merely because code is unfamiliar.
- Never silently drop a valid out-of-scope finding.
- Never let the implementer fix findings before the review is recorded.
- Never run repeated review rounds to chase a preferred verdict.
- Never modify code in review-only mode.