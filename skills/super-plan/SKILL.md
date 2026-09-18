---
name: super-plan
description: Interview-driven, evidence-grounded planning for significant tasks (refactors, migrations, features, bugs). Produces a reviewed, self-contained plan file that a fresh session can execute phase by phase, plus a post-implementation audit stage. Use when the user asks to plan a task with this skill, or wants a plan solid enough to hand off.
---

Build a plan through six stages. Do not skip stages; do not reorder them. The output is a plan file good enough that an agent with no memory of the conversation can execute it.

## 1. Explore before asking

Enter plan mode. Launch exploration agents to map the territory FIRST: structure, sizes, dependency edges, coupling, existing patterns, test coverage. Demand file:line citations and facts, not verdicts. A fact that can be looked up must never become a question to the user.

## 2. Grill the decisions

Interview the user with the question tool, ONE decision at a time, in dependency order — most foundational first; never ask a question whose answer depends on an earlier undecided one. Each question offers 2-4 concrete options with honest trade-offs, and marks one "(Recommended)" with the reasoning visible. Questions that depend on unexplored facts wait for exploration results rather than being asked blind. Ambiguous terms the user used (e.g. "headless", "API") get pinned down explicitly — the first question is usually about what a key word actually means. Record every answer as a numbered locked decision. Always cover: scope boundary (what this task explicitly does NOT include), execution strategy (incremental vs big-bang), and the constraint that survives longest (portability, compatibility, naming).

## 3. Design with evidence

Hand the locked decisions to a Plan agent as non-negotiable constraints, plus an explicit list of open design questions it must resolve by reading real code and citing file:line evidence. Require it to flag: the riskiest step, any deviation from the locked decisions (with justification), and every claim it could not verify. When the design comes back, personally spot-check the most load-bearing claims before trusting them.

## 4. Write the plan, then attack it

Write the plan file with these sections: Context (why), Locked decisions (numbered, user-approved), Resolved design details (each with its evidence), Phases (each phase: goal, concrete file paths, expected config/manifest changes, and its own verification step — the build must be green and the app must run after EVERY phase), Definition of done, Verification (how to prove the whole thing works), Known unverified facts, and an empty Progress section.

The Definition of done is the quality floor that "compiles and tests pass" cannot see, checked as part of every phase's verification: guardrails (CI gates, check scripts, lint configs) are updated to the new structure — never deleted or weakened; no new lint-suppressing allows or global mutable state; code moving into reusable/shared locations carries no caller-specific policy with it. Add criteria specific to the task.

Write the complete draft directly to the target plan file before launching review. Then spawn a separate adversarial review agent, give it the plan file path, and have it try to break the saved plan against a checklist: full coverage (does every affected file have a stated destination), dependency-direction violations, per-phase feasibility (does each phase really build alone), hidden couplings, stale tooling paths (CI gates, scripts), and fresh-session executability. Findings come back severity-ranked with file:line evidence. Apply every required finding as a targeted edit to the existing plan file rather than regenerating the full plan. Re-read the edited sections and verify that every required finding is represented in the file; a finding acknowledged but not written into the plan is a finding lost.

## 5. Hand off for execution

The plan header must state: it is self-contained, phases run in order one at a time, cited line numbers were valid at planning time and must be re-located if code drifted, and the Progress section gets updated after each phase. Confirm the reviewed plan remains saved at the requested location, then give the user a kickoff prompt for the implementing session: working directory, "implement <plan path> one phase at a time, run each phase's verification before moving on, update Progress after each phase, report failures honestly."

## 6. Audit the result

Implementation finishing is not the finish line. After all phases are done, spawn a fresh agent to audit the implemented result against the locked decisions and the Definition of done — facts only, with file:line evidence, no verdicts. Judge the findings yourself and turn what matters into a small numbered hardening task list with concrete file paths, one task per concern, executed with the same green-after-every-task rule. When any agent claims a task is done, verify the claim independently: run the static checks yourself, read the changed seams, and state plainly which parts you could not verify (builds, runtime behavior) and how the user can.

## Principles

- Facts are looked up; decisions are the user's. Never invert this.
- One question per message. A recommendation with visible reasoning accompanies every question.
- No speculative abstractions: nothing enters the plan that today's code does not need; boundaries are merely chosen so tomorrow's needs fit naturally.
- Every phase ends green. A phase boundary is always a safe stopping point.
- Quality improvements discovered during a mechanical move go into the audit/hardening stage, not into the move itself — mixing redesign into a move raises the risk of both.
- "Done" claims — the design agent's, the reviewer's, or the implementer's — are verified, not trusted.
