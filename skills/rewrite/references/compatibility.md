# Compatibility checks

Read only the sections relevant to the rewrite. Turn applicable concerns into observed requirements and tests, not a mandatory catalog of new features. The examples identify boundaries to investigate; they do not prescribe a stack.

## Shared language and runtime boundaries

| Boundary | Questions to resolve using source evidence |
| --- | --- |
| Values | Are missing, null, empty, zero, and false distinct? Are defaults evaluated eagerly? Do copies preserve identity or alias mutable state? |
| Numbers | What are the ranges, overflow rules, decimal precision, rounding, division, and large-integer serialization rules? |
| Text | What are the encoding, normalization, indexing unit, malformed-input policy, locale, and comparison rules? |
| Collections | Do order, duplicate keys, equality, hashing, and mutation during iteration affect output? |
| Errors | Which failures are returned, thrown, retried, cancelled, or fatal? What cleanup and externally visible effects occur before failure? |
| Concurrency | What ordering is promised? Who owns mutable state? Can callbacks reenter, outlive a view/request, or run after cancellation? |
| Resources | What releases files, sockets, observers, tasks, buffers, and foreign handles on success, failure, shutdown, and transfer? |
| Build modes | Do assertions, optimization, feature flags, generated code, or conditional compilation change side effects or checks? |
| Environment | Do timezone, paths, filename case, permissions, signals, locale, architecture, or endianness affect behavior? |

## Desktop, mobile, and UI rewrites

Map user journeys and state transitions separately from widgets. A screenshot proves appearance at one moment, not navigation, focus, interaction, persistence, or recovery. Use interaction checks and accessibility inspection alongside visual comparison. Native conventions may differ intentionally; record those differences against the user requirement.

Inspect relevant boundaries:

- Startup, multiple windows/scenes, navigation/back behavior, unsaved work, session restoration, and shutdown.
- Focus, keyboard shortcuts, menus, text input and composition, selection, drag/drop, clipboard, accessibility labels, and assistive navigation.
- Local files, database locations, document associations, deep links, keychain/secure storage, permissions, and sandbox scope.
- Network loss, offline edits, synchronization conflicts, background tasks, suspension, process termination, and resume.
- Notifications, tray/menu-bar behavior, native modules, web content, OS integrations, and hardware capabilities.
- Bundle/package identity, signing configuration, entitlements, installer, updater, app-store constraints, and existing-user data discovery.

### Electron → native desktop

Inventory renderer, main-process, preload, and IPC responsibilities. Map privileged operations, window management, local storage, and integrations to native boundaries. Preserve essential IPC behavior through the new architecture without assuming IPC itself must remain. Exercise opening existing documents, saving, quitting, relaunching, and recovering state. Account for users' old storage location and update path.

### Native mobile → cross-platform mobile

Inventory platform services and background behavior before treating this as a screen translation. Verify navigation, secure storage, notification registration, deep links, lifecycle cancellation, native-module availability, and accessibility on every required target. A source app that only supports one OS does not imply that the user requested additional platforms.

### Native desktop → another language

A language choice does not settle the UI toolkit or platform scope. Resolve those when material. Separate domain logic from OS services, UI thread coordination, foreign interfaces, packaging, and signing. Prove one native integration and one persistent workflow before scaling domain translation.

## Backends, services, and workers

Treat HTTP/RPC behavior, jobs, and persisted state as separate compatibility surfaces:

- Routing, methods, redirects, status codes, headers, cookies, content types, error envelopes, pagination, and wire formats.
- Authentication and authorization decisions, session lifetime, password-hash verification, token handling, tenant isolation, and request limits.
- Database schema, transaction boundaries, isolation, constraints, timestamp precision, numeric representation, and migrations.
- Retry policies, idempotency keys, ordering, delivery semantics, deduplication, timeouts, and partial external failure.
- Configuration precedence, secrets integration, connection pools, health/readiness checks, shutdown draining, logs, and operational metrics used by existing systems.

### PHP → Go, or analogous service rewrites

Investigate coercion and serialization using real payloads, especially empty objects/arrays, nulls, numeric strings, and money. Inspect state previously isolated per request before moving it into long-lived processes or concurrent workers. Compare authorization, SQL effects, and retry behavior alongside response bodies. Framework middleware and defaults are behavior even when there is little application code for them.

## Libraries, CLIs, runtimes, and low-level components

- Public API signatures, calling conventions, ABI layout, symbol visibility, callbacks, foreign ownership, and exception boundaries.
- CLI flags, stdin/stdout/stderr separation, output formats, exit codes, signals, terminal behavior, and configuration precedence.
- File/wire formats, parser limits, invalid input, streaming, backpressure, cancellation, and partial reads/writes.
- Package metadata, import resolution, supported architectures, native dependencies, and downstream consumer builds.

Test through public consumers where possible. Internal unit tests may survive a port while missing a changed ABI, exit code, or package layout. Separate documented malformed-input handling from undefined behavior in the old implementation.

## Existing data and cutover

Identify every state store the replacement must read, including files, caches that contain irreplaceable state, preferences, credentials, databases, and queued work. Record format versions and which implementation can read and write each one.

Use a disposable, representative snapshot to test upgrade, rerun/idempotency, interruption, partial conversion, and recovery. For coexistence, define one writer or an explicit synchronization protocol; do not invent casual dual writes. Track writes made after cutover when assessing rollback. If reverse migration is impossible, specify the backup/restore or forward-recovery procedure and its data-loss implications before changing live state.

## Verification record

A useful evidence entry contains requirement ID, source observation, target check, source/target revision, command or manual procedure, fixture, platform/build mode, result, and log/artifact location. Mark intentional deviations with their rationale and the user's decision when required. Keep unavailable checks visible in the final readiness assessment.
