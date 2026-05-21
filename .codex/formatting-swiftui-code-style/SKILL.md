---
name: formatting-swiftui-code-style
description: Use when the task is to apply SwiftUI formatting conventions, MARK comment organization, modifier-chain readability, preview style, or safe editing/improvement/optimization of existing Swift code logic. Do not use for broad repository architecture, platform targeting, build/test commands, folder layout policy, or project-level rules handled by AGENTS.md.
---

If this skill is used, say:SWIFTUI_STYLE_LOGIC_SKILL_LOADED

## Goal

Apply reusable SwiftUI code-style rules and protect existing behavior when editing, improving, or optimizing Swift code logic.

This skill is for local code editing discipline. Project behavior, platform targeting, repository layout, state-management policy, build behavior, and anti-patterns belong in `AGENTS.md`.

## Inputs

- Current Swift files being edited
- Direct callers, references, and related types
- Existing project formatting style
- Existing `AGENTS.md` instructions
- User request and any stated behavior change
- Existing tests, previews, or validation commands if relevant

## Required sections

When relevant, apply these rule groups:

- Code logic editing restrictions
- MARK comments
- Spacing and indentation
- SwiftUI modifier chains
- SwiftUI previews
- Output summary rules

## Steps

1. Read the full file being edited before changing it.
2. Read direct callers, call sites, bindings, persistence references, and API references before changing logic.
3. Decide whether the task is formatting-only, behavior-changing, logic improvement, or optimization.
4. Preserve runtime behavior by default.
5. Keep the diff small and directly related to the user request.
6. Separate formatting cleanup from behavior changes where practical.
7. Apply the project’s existing naming, indentation, spacing, and preview style first.
8. Use the rules below only when they fit the existing project style.
9. Make any intentional behavior change explicit in the final summary.
10. Call out uncertainty when behavior cannot be confirmed from the available code.

## Code logic editing restrictions

Use these rules when changing, improving, or optimizing existing code logic.

- Treat behavior preservation as the default. A style, formatting, or cleanup task must not change runtime behavior.
- Do not change business rules, validation rules, navigation flow, persistence behavior, networking behavior, error handling, sorting, filtering, authorization, timing, or platform behavior unless the task explicitly requires it.
- Before editing logic, read the full function/type and its direct callers or call sites. Do not optimize code that is not understood.
- Prefer the smallest logic change that solves the requested problem. Avoid broad rewrites, speculative refactors, and unrelated cleanup.
- Do not combine behavior changes with formatting-only changes in the same edit unless unavoidable. Keep the behavioral diff easy to review.
- Preserve public APIs, stored property names, Codable keys, persistence model fields, user defaults keys, localization keys, asset names, and notification names unless the task explicitly asks to rename or migrate them.
- Do not remove apparently redundant checks, guards, delays, retries, cache invalidation, logging, fallback paths, or compatibility paths unless their purpose is confirmed from context.
- Do not replace explicit error handling with `try?`, forced unwraps, silent returns, or broad `catch {}` blocks.
- Do not introduce caching, memoization, debouncing, throttling, parallel execution, detached tasks, or lazy loading unless correctness, invalidation, cancellation, and actor isolation are clear.
- Do not change `@MainActor`, actor isolation, async task lifetime, cancellation behavior, or thread-safety boundaries only for convenience.
- Do not optimize by making code less readable unless there is a measured or obvious performance problem. Readability wins over micro-optimization.
- Do not change numeric precision, date/time handling, locale behavior, unit conversion, ordering stability, equality semantics, or identifier generation without an explicit reason.
- Do not introduce new dependencies, frameworks, global state, singletons, or hidden shared mutable state for an optimization.
- When improving logic, keep inputs, outputs, side effects, and failure paths explicit.
- When a behavior change is intentional, make it visible in the final summary and mention the reason.

## MARK comments

Use `// MARK: -` comments to separate logical sections inside larger files.

Recommended order for SwiftUI View files:

```swift
// MARK: - Properties

// MARK: - Body

// MARK: - Subviews

// MARK: - Actions

// MARK: - Helpers

// MARK: - Preview
```

Recommended order for state model, ViewModel, service, and helper files:

```swift
// MARK: - Properties

// MARK: - Lifecycle

// MARK: - Public Methods

// MARK: - Private Methods

// MARK: - Helpers
```

Rules:

- Add one empty line before every `// MARK: -`.
- Add one empty line after every `// MARK: -`.
- Do not add `// MARK: -` before single functions, tiny computed properties, or every small item.
- Do not overuse MARK comments.
- For short files, no MARK comments are needed.

## Spacing and indentation

- Use 4 spaces.
- Do not use tabs.
- Keep exactly one empty line at EOF.
- Add one empty line before `var body: some View`.
- Keep modifier chains readable.
- Prefer one modifier per line when a chain becomes long.
- Keep related short modifiers grouped only when readability improves.

Example:

```swift
Text(title)
    .font(.headline)
    .foregroundStyle(.primary)
    .padding()
```

## SwiftUI modifier chains

Prefer clear SwiftUI modifier chains over dense formatting.

Good:

```swift
Button("Add", systemImage: "plus", action: addItem)
    .buttonStyle(.borderedProminent)
    .disabled(isSaving)
```

Rules:

- Keep modifier chains easy to scan.
- Put long or state-dependent modifiers on separate lines.
- Extract subviews or helper properties when a modifier chain hides control flow.
- Do not use formatting tricks to hide complex logic inside modifiers.

## SwiftUI previews

- Put SwiftUI previews at the bottom of the View file.
- Prefer `#Preview` for modern code.
- Use `PreviewProvider` only when the deployment target or existing project style requires it.
- Keep previews fast and reliable.
- Use design-time data and preview-specific containers for complex state.
- Add only useful previews for important states on the actual target platform.

## Output rules

- Summarize what changed in simple engineering terms.
- Separate style-only changes from behavior changes.
- Mention any intentional behavior change.
- Mention validation performed, or state clearly when no validation was run.
- Mention uncertainty when behavior could not be fully verified from the available code.
- Do not claim performance improvement unless it is measured, obvious from algorithmic complexity, or directly requested and justified.

## Do not

- Do not change runtime behavior during formatting-only work.
- Do not rewrite code broadly when a local edit is enough.
- Do not remove safeguards without understanding why they exist.
- Do not silence errors.
- Do not hide side effects inside View bodies, computed properties, or modifier chains.
- Do not change persistence schemas, Codable keys, localization keys, user defaults keys, or asset names without an explicit migration reason.
- Do not introduce concurrency, caching, global state, or new dependencies as an optimization without clear correctness guarantees.
- Do not overuse MARK comments.
- Do not claim clean architecture, performance, or safety guarantees unless the code supports the claim.
