---
name: rewrite
description: Plan and execute software rewrites, ports, and replatforming across languages, frameworks, runtimes, and operating systems while preserving required behavior and data. Use for replacing an existing app, backend, library, CLI, or subsystem with a new implementation, including native-to-cross-platform and web-to-native migrations. Not for prose rewriting, routine refactoring, or dependency upgrades without an implementation replacement.
---

# Rewrite

Replace an existing implementation with one that meets the user's target requirements and has evidence of compatibility. Preserve required behavior; adapt implementation and platform conventions deliberately. A rewrite request authorizes implementation, not merely a plan. If the user requests planning or assessment only, stop at that deliverable.

This workflow is model, provider, and tool agnostic. Use the available editor, shell, build tools, test runners, and review capabilities. No particular assistant, API, orchestrator, or other skill is required. Work sequentially when delegation is unavailable or unauthorized.

## 1. Establish the rewrite contract

Inspect repository instructions, source entry points, dependency manifests, tests, release configuration, and existing documentation before proposing architecture. Identify:

- Source implementation and revision; target language, framework, runtime, and supported platforms.
- Scope: whole product or named subsystems; required features and explicit exclusions.
- Motivation and measurable success: compatibility, resource use, reliability, maintainability, platform integration, or another stated outcome.
- Constraints: external consumers, deployment model, data formats, minimum OS versions, offline use, dependencies, schedule, and available test environments.

Infer answers from the request and repository. Ask only about unresolved choices that materially affect the result, such as the target UI toolkit or whether a public API may change. Continue independent discovery while waiting. Do not silently choose a new product scope or drop a platform because the target makes it inconvenient.

Create a durable rewrite record in the project's documentation convention, for example `docs/rewrite/`. For a small subsystem, combine these records into one document; expand only when useful:

| Record | Contents |
| --- | --- |
| Plan | Scope, source revision, strategy, dependencies, acceptance gates, and delivery boundary |
| Behavior inventory | Stable requirement IDs, source evidence, target location, verification, and status |
| Porting guide | Source-to-target patterns, semantic differences, boundary contracts, and intentional deviations |
| Work log | Units, dependencies, ownership, failures, review findings, evidence, and next actions |

Treat the old implementation as evidence, not an infallible specification. Classify observable behavior as required compatibility, intentional change, known defect, or unresolved. Do not reproduce a vulnerability merely for parity; document a safe behavior and resolve any external compatibility impact. Keep unrelated feature work and optional cleanup outside the rewrite.

## 2. Capture the baseline

Preserve an identifiable source revision and, where practical, a runnable reference artifact. Record how to build and run it, including toolchain, configuration, fixtures, and environment. If the original cannot run, record why, use source and documented contracts, and label the resulting verification gaps.

Inventory externally visible behavior before dividing implementation work. Include error and recovery paths, persisted state, integrations, installation, and operation—not just screens, endpoints, or exported symbols. Read the relevant sections of [Compatibility checks](references/compatibility.md) for the kind of software being rewritten.

Run the relevant existing checks against the source and record failures, skips, and actual discovery counts. Reuse behavioral tests where they can run against either implementation. When tests depend on the old framework, preserve their scenarios and assertions through target-specific drivers or replacement tests; record the mapping. If coverage is weak, add characterization tests around consequential behavior before replacing it.

For differential checks, use identical inputs and isolated copies of state. Compare outputs, errors, persisted changes, and observable side effects. Normalize only documented nondeterminism, such as generated IDs or timestamps; never normalize away meaningful ordering or content differences. Do not replay real requests into systems that send messages, charge money, or mutate production data.

Measure the metrics relevant to the rewrite's stated goal on representative workloads. Record the configuration and baseline; make acceptance thresholds explicit instead of promising that a new stack will be faster.

## 3. Choose the migration shape

Choose based on coupling, interoperability, operational continuity, and reversibility:

| Shape | Appropriate when | Plan for |
| --- | --- | --- |
| Incremental replacement | Stable seams allow old and new parts to coexist | Adapters, version compatibility, state ownership, and removal of temporary bridges |
| Parallel full implementation | Components are tightly coupled or coexistence costs more than a parallel build | A stable reference, ongoing source changes, integration milestones, and a coordinated switch |
| Hybrid | Some domains have useful seams and others require replacement together | Explicit boundaries and acceptance criteria for each part |

Do not assume a full rewrite means translating every file before anything runs. Organize by dependency-aware, testable units and integrate early. Avoid inventing a module per source file or reproducing boundaries that the target cannot support.

Use close translation for compatible domain logic when it improves traceability. Reimplement platform-dependent shells through target-native facilities while preserving required journeys and contracts. Changing from a web UI to native UI may require a new view hierarchy; changing from request-scoped to long-lived server execution may require a new state model. Record why each necessary architectural difference exists.

Specify how bug fixes and features arriving in the source during the rewrite will be tracked and reconciled. A moving source branch must not silently invalidate the baseline.

## 4. Resolve semantic risks and prove a slice

Write concrete mappings from patterns found in this repository, with small source/target examples and verification where ambiguity matters. Cover only relevant areas: value representation, ownership, concurrency, errors, serialization, I/O, UI state, and foreign or native interfaces. Check current primary documentation when target behavior or library support is uncertain.

For long-lived resources, record who creates, owns, transfers, cancels, and releases them. Include asynchronous completion and failure paths. For stateful boundaries, record transaction scope, retry behavior, ordering, and the source of truth. Do not infer matching semantics from matching names or syntax.

Implement a small representative slice that crosses the riskiest boundaries—for example input → domain logic → storage → output, including an error path and restart or recovery where relevant. Compile or validate, package as needed, and actually run it in the target environment. A static mockup, empty endpoint, or successful type check does not prove the migration works.

Compare it with the baseline, review it, and correct the porting guide before scaling. If it disproves the chosen strategy, revise the affected decision and try a narrower experiment. Do not multiply a failed pattern across the codebase.

## 5. Execute from a durable work queue

For each unit, record its requirement IDs, source paths, target paths, dependencies, acceptance checks, and status. Distinguish pending, implementing, awaiting verification, verified, and blocked; written code is not completed behavior.

Repeat:

1. Read the unit's source, callers, contracts, and applicable mappings.
2. Implement the smallest coherent unit without unrelated redesign.
3. Run targeted validation and execute its behavioral checks.
4. Review the changes against the source and rewrite contract; verify and fix findings.
5. Integrate and rerun affected boundary checks; update the inventory and evidence.

Turn build diagnostics and runtime or test failures into reproducible work items with command, revision, environment, expected result, actual result, and logs. Group cascading failures by root cause rather than dispatching every diagnostic separately. Recheck after shared dependencies change so workers do not fix obsolete failures.

Reject fake progress: required behavior cannot be replaced with constants, no-ops, swallowed errors, disabled paths, unimplemented stubs, reduced limits, or weakened tests to make checks pass. Temporary scaffolding must be tracked and excluded from completion claims. Legitimate test or contract changes need an explained behavior decision and equivalent evidence.

When a defect reveals a faulty mapping or workflow, correct that rule and audit already migrated uses of the same pattern. Add a regression check when it meaningfully protects the behavior. Repeated retries without new evidence are not progress: preserve the failure, change the hypothesis or strategy, and continue unaffected work. Surface a concrete blocker when missing access, tooling, or a product decision prevents further progress.

### Review and optional delegation

Use independent review contexts when available and authorized. Give reviewers the diff, original implementation, contract, and relevant tests without the implementer's persuasive narrative. Ask for concrete failures with file locations, triggers, impact, and evidence. For consequential units, use complementary behavioral and target-semantics reviews. Reviewers report; the implementer resolves verified findings and reruns checks.

With one agent, perform separate passes against those two concerns after implementation and explicitly report that review was not independent. Do not claim several role prompts in one context are several reviewers.

If delegating, assign disjoint write ownership and explicit shared-interface contracts. Coordinate edits to manifests, lockfiles, generated files, schemas, and common APIs. Use isolated workspaces where practical; otherwise serialize conflicting writes. One coordinator owns integration and shared build resources. Workers must not reset, stash, clean, or overwrite one another's work. Respect actual CPU, memory, disk, and test-service limits rather than maximizing worker count.

## 6. Verify the replacement as a product

Use layered evidence: target validation/build, startup, integrated workflows, behavioral compatibility, relevant failure and concurrency cases, and the actual release configuration. Run the required supported-platform matrix; unavailable devices, operating systems, credentials, or SDKs remain named gaps, never inferred passes.

Check that the intended tests executed against the replacement. Inspect discovery, skips, conditional compilation, feature flags, and harness configuration. Passing commands or equal test counts alone do not establish equivalent coverage. Preserve the scenarios and assertions that enforce the contract.

Exercise real installation and upgrade paths where in scope. Rehearse migration of representative existing data on disposable copies, including interruption and recovery. Verify rollback against data written by the new implementation: reverting an executable is insufficient if the old version cannot read the new state.

Compare relevant performance and resource metrics under comparable release configurations and workloads. Investigate regressions against the agreed thresholds. Use stress, fuzz, leak, or fault-injection testing where the component's risks justify them, with resource limits and isolated services.

## 7. Prepare cutover and finish the authorized scope

Reconcile source changes since the baseline. Audit every in-scope requirement against target code and verification evidence. Resolve remaining scaffolding and review findings. If a required capability is unsupported, mark the rewrite incomplete or obtain an explicit scope decision; do not redefine success silently.

Prepare a proportionate cutover procedure: deployment or distribution artifact, configuration, data transition, compatibility window, health signals, rollback trigger, and recovery steps. Use staged rollout or shadow comparison when applicable and already authorized. Shadow execution must not duplicate real side effects.

Distinguish implementation completion, verification readiness, and release status. Perform deployment, publication, destructive cleanup, or production migration only within existing authorization. Keep the source and recovery path until the cutover's acceptance conditions are satisfied. Do not create approval gates for routine reversible implementation work.

At handoff, report:

- What was replaced and the deliberate behavior or architecture differences.
- Checks actually run, their results, platform/configuration coverage, and unresolved limitations.
- Reproducible build/run instructions and the location of compatibility and migration records.
- Cutover/rollback readiness, pending actions, and deferred cleanup.

If interrupted, leave enough state for another session to resume: source and target revisions, verified units, failing commands, unresolved decisions, and the next useful action. Do not label a partial port complete.

## Inspiration

Inspired by [Rewriting Bun in Rust](https://bun.com/blog/bun-in-rust): prepare translation rules, test a small port, use independent adversarial review, preserve behavioral checks, and repair the process behind recurring errors. The cross-platform contract, strategy choices, data migration, and delivery guidance here generalize the workflow beyond that project's circumstances; its language choices, model, scale, and schedule are not requirements.
