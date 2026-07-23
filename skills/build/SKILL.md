---
name: build
description: Implement a user-assigned task in the current project end to end. Use when the user asks to build, implement, change, fix, or continue something, or asks to work on a specific issue. The task may be described directly or retrieved from a task system explicitly named or referenced by the user. Understand the requirements, implement them, verify the result, review the changes, and provide a clear handoff.
---

# build — autonomous task execution

Complete the task assigned by the user from start to finish.

The task may be:

- described directly in the prompt;
- referenced by an issue ID, URL, or document;
- retrieved from a task system named by the user;
- continued from unfinished work in the current session.

Work only on the assigned task. Do not search for, select, or start additional work.

Once the task is sufficiently clear, work hands-off. Do not ask routine questions about
implementation details, testing, committing, or whether to proceed. Make reasonable decisions
from the codebase and continue until the task is complete or genuinely blocked.

## 1. Resolve the task

Treat the user’s prompt as the source of work.

- If the task is described directly, use that description.
- If the user references an issue, read that issue completely.
- If the user names a task system, use it only as requested.
- If the user says `continue`, resume the unfinished task from the current context.
- If the task has acceptance criteria, treat them as the definition of done.
- If the task has no formal acceptance criteria, derive a narrow, testable definition of done
  from the prompt and project context.

When working from an issue or task system:

- read its description, acceptance criteria, notes, dependencies, and linked material;
- claim or mark the assigned task in progress when appropriate;
- do not browse for or select other tasks unless the user explicitly asks.

If no task can be identified from the prompt or current context, ask the user what they want
built. Do not invent work.

## 2. Orient

Before changing anything:

1. Find the project root.
2. Inspect the working tree and existing changes.
3. Read the applicable project instructions.
4. Identify the language, framework, package manager, build system, and repository conventions.
5. Discover the project’s verification and delivery workflow.

Look for guidance such as:

- `AGENTS.md`
- `CLAUDE.md`
- `CONTRIBUTING.md`
- `README.md`
- architecture and development documentation
- package manifests and task-runner configuration
- CI workflows
- specifications and decision records referenced by the task

Follow the applicable instruction hierarchy. Preserve unrelated existing changes and assume
they belong to the user unless proven otherwise.

## 3. Understand before writing

Before implementation:

- inspect the relevant code, tests, configuration, and history;
- trace the affected callers, interfaces, and data flows;
- find existing patterns and similar implementations;
- read documents and research referenced by the task;
- identify compatibility, security, migration, and failure-handling concerns;
- determine which tests, generated files, documentation, fixtures, or snapshots need updating.

Resolve ordinary implementation choices yourself.

Ask the user only when missing information would materially change product behaviour,
architecture, security, compatibility, cost, or the meaning of the requested task.

## 4. Build

Follow the project’s documented rules and established patterns.

General principles:

- Make the smallest complete change that satisfies the task.
- Avoid unrelated refactoring or cleanup.
- Prefer existing abstractions over parallel implementations.
- Keep files and functions focused.
- Avoid speculative infrastructure.
- Preserve backward compatibility unless the task explicitly changes it.
- Preserve unrelated user changes.
- Follow the project’s conventions for naming, comments, formatting, errors, and tests.
- Use the existing package manager and lockfile strategy.
- Add dependencies only when necessary and permitted by project policy.
- Update generated files through their authoritative generator.
- Include migrations or compatibility handling when required.
- Never expose credentials, secrets, or private data.

Project-specific instructions override these defaults.

## 5. Verify

Work through every requirement and acceptance criterion explicitly.

Discover the required verification commands from the project rather than assuming a particular
technology. Inspect:

- project instructions;
- package-manager scripts;
- task-runner configuration;
- CI workflows;
- existing tests;
- developer documentation.

Run the applicable:

- focused tests while iterating;
- full required test suite;
- build;
- linting;
- formatting checks;
- type checking;
- static analysis;
- integration or end-to-end checks.

Use only checks relevant to the project.

If no automated test exists for changed behaviour, add one when practical. Otherwise perform
the strongest deterministic validation available and explain the limitation.

If a required gate needs unavailable credentials, hardware, services, a graphical environment,
or manual interaction:

- run every available gate;
- state exactly what could not be run and why;
- never claim an unavailable gate passed.

Report failures honestly and include useful error output.

## 6. Review gate

Perform one structured review round before considering the task complete.

### Independent review

When subagents are available, use a fresh reviewer that did not implement the change. Give it:

- the assigned task;
- acceptance criteria verbatim;
- the complete diff, including new files;
- applicable project rules;
- verification results;
- relevant issue notes and referenced findings.

Ask it to review, in this order:

1. Correctness against every requirement and acceptance criterion.
2. Regressions, edge cases, and normal failure paths.
3. Security, compatibility, and data-integrity risks.
4. Violations of project conventions or architecture.
5. Unnecessary complexity, dependencies, or scope expansion.
6. Anything likely to cause future problems.

Require a verdict of `PASS` or `CHANGES NEEDED`. Each finding must include severity, file, and
location.

If subagents are unavailable, perform the same structured review yourself in a fresh pass.

### Fix and adjudicate

If changes are needed, use a separate fixer subagent when available. Otherwise fix them
directly.

Fix critical and major findings. Use judgement on minor findings and reject incorrect feedback
with a concrete explanation.

Then re-run the applicable verification.

Use one review round only:

- Findings fixed and verification passes → complete the task.
- Reviewer finding is incorrect → reject it and record why.
- Finding is valid but outside the assigned scope → report it or create a follow-up only when
  the user’s requested workflow includes doing so.
- Work remains incomplete → do not present it as done.

## 7. Finish the assigned task

If the task came from an issue or task system:

- record what was implemented;
- record what was verified;
- document important decisions or limitations;
- close the assigned task only when its acceptance criteria are satisfied.

Do not update, close, or create unrelated tasks unless the user requested that workflow.

If the project uses version control:

1. Inspect the final status and diff.
2. Ensure unrelated changes are excluded.
3. Follow the repository’s documented delivery workflow.
4. Commit when requested by the user or expected by the project workflow.
5. Push only when requested or when pushing is an established part of completing the task.
6. Never force-push or rewrite unrelated history without explicit authorization.

Do not introduce a tracker, version-control system, branching strategy, or delivery workflow
that the project does not already use.

## 8. Stop and hand off

Stop when the assigned task is complete. Do not automatically begin another task.

Also stop when genuinely blocked by:

- a decision only the user can make;
- missing credentials or permissions;
- an unavailable required dependency or service;
- a verification failure that cannot be safely resolved;
- required destructive action outside the task’s clear scope;
- a conflict with unrelated user work that cannot be preserved safely.

When stopping, report:

- what was implemented;
- what was verified;
- anything that could not be verified;
- files or areas materially changed;
- remaining limitations or follow-ups;
- the exact blocker and required user action, if blocked.

## 9. Guardrails

- Do not search a backlog or choose work automatically.
- Do not use a task system unless the user references or requests it.
- Do not start another task after completing the assigned one.
- Do not invent requirements or acceptance criteria.
- Do not expand the scope without a concrete reason.
- Do not overwrite or include unrelated user changes.
- Do not assume a language, framework, package manager, tracker, or version-control system.
- Do not claim checks passed unless they were actually run.
- Do not mark incomplete work as complete.
- Do not hide partial completion behind a successful build or commit.
- Do not commit or push merely because those capabilities exist.