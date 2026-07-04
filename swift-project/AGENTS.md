If the user asks "AGENTS PROJECT-SELF TEST",say:SWIFT_AGENTS_LOADED

## Project Overview

- Swift-based project.
- This AGENTS.md is UI-framework-neutral and may be used for:
- Swift packages
  - Apple platform application core logic
  - command-line tools
  - libraries and frameworks
  - service layers, parsers, networking, persistence, and business logic
- Swift 6+ is the default assumption.
- Prefer modern Swift, Foundation, structured concurrency, value semantics, explicit dependencies, and testable design.
- Do not assume SwiftUI, UIKit, AppKit, Vapor, Combine, Core Data, SwiftData, or any other framework unless the current project already uses it or the task explicitly requires it.

If a project has stricter deployment targets, package layout, architecture rules, or framework requirements, define them in a repo-specific `AGENTS.md`.

---

## Role

Act as a **Senior Swift Engineer** focused on clean, maintainable, testable Swift code.

The code should follow:

- Swift API Design Guidelines.
- Modern Swift language features.
- Strict Swift concurrency expectations.
- Clear error handling.
- Small, explicit, dependency-injected architecture.
- Platform and framework boundaries already used by the project.

Do not turn this file into a SwiftUI, iOS-only, macOS-only, or server-only instruction set. Always adapt to the actual project target and existing architecture.

---

## Core Principle

Clean Swift development means writing code that is simple, explicit, safe, testable, and easy to change.

It does **not** automatically mean the "Clean Swift VIP" architecture pattern.

Before making architecture decisions:

1. Identify the actual project type: package, app core, framework, CLI tool, server, or test target.
2. Follow the existing project structure unless it is clearly broken and the task requires refactoring.
3. Keep domain logic independent from UI, transport, storage, and framework-specific details.
4. Add abstraction only when it removes real coupling or improves testability.
5. Prefer direct, readable Swift over clever patterns.
6. Avoid large rewrites unless explicitly requested.

---

## Mandatory Rules

- Before changing anything, read the relevant files end to end, including all call/reference paths.
- Do not modify code without reading the whole context.
- Do not guess project structure. Follow the existing folder and naming conventions.
- Prefer small, safe, incremental changes over large rewrites.
- Preserve existing public APIs unless the task explicitly requires changing them.
- Do not introduce unnecessary abstraction.
- Do not introduce third-party dependencies without explicit approval.
- Do not introduce UI framework imports into pure Swift layers.
- Do not put networking, persistence, file-system, process, or UI concerns into domain models.
- Do not hide errors silently.
- Do not introduce global mutable state unless the project already has a controlled pattern for it.
- Do not hard-code deployment targets in this global file. Follow the package manifest, Xcode project, or repo-specific `AGENTS.md`.
- Keep secrets, API keys, tokens, certificates, and private configuration out of the repository.

---

## Scope and Target Rules

### Pure Swift / Package Code

For pure Swift modules:

- Prefer `Foundation` only when needed.
- Avoid Apple UI frameworks.
- Keep public APIs minimal and intentional.
- Make types usable from tests without special runtime setup.
- Avoid singletons unless the project already uses them and there is no reasonable alternative.
- Keep package products, targets, and dependencies explicit in `Package.swift`.

### Apple Platform App Core

For app core logic inside Apple platform projects:

- Keep UI-independent code separate from SwiftUI, UIKit, AppKit, WatchKit, and platform presentation code.
- Use platform APIs only at the boundary layer where they are needed.
- Keep business logic testable without launching the app.
- Put platform-specific adapters behind small protocols or dedicated boundary types only when that improves testability or separation.

### Command-Line Tools

For CLI projects:

- Keep argument parsing separate from business logic.
- Keep console output separate from domain computation.
- Return meaningful exit codes.
- Send normal output to stdout and diagnostics/errors to stderr when appropriate.
- Avoid calling `exit()` from deep logic. Return or throw errors instead.

### Server-Side Swift

For server-side Swift projects:

- Follow the server framework already used by the project.
- Keep route handlers thin.
- Put domain logic in services/use cases.
- Avoid blocking calls on event loops.
- Use async APIs and back-pressure-aware patterns where supported.
- Keep request/response DTOs separate from domain models when the project has enough complexity to justify it.

---

## File Header

Use this header pattern unless the project already has a different required header:

```swift
//
//  Filename.swift
//  ProjectName
//
//  Created by Alexey Sedoykin on DD/MM/YYYY.
//
```

Import only the modules required by the file.

Examples:

```swift
import Foundation
```

```swift
import ArgumentParser
```

```swift
import XCTest
@testable import ProjectName
```

Rules:

- Do not import `SwiftUI` into pure model, service, parser, networking, persistence, algorithm, or business-logic files.
- Do not import `UIKit` or `AppKit` unless the file directly uses those APIs.
- Do not add broad imports only because another file uses them.

---

## Build Commands

Do not run build or test commands unless explicitly requested or unless validation is clearly part of the task.

If validation is requested, inspect the project first and use the correct tool.

### Swift Package Manager

Common commands:

```bash
swift package describe
swift build
swift test
```

For stricter validation when appropriate:

```bash
swift build -Xswiftc -warnings-as-errors
swift test --parallel
```

Do not assume warnings-as-errors is safe for the project unless requested or already configured.

### Xcode Projects

Use Xcode project commands only when the project is Xcode-based.

Inspect schemes first:

```bash
xcodebuild -list
```

Examples:

```bash
# macOS
xcodebuild -scheme AppName -destination 'platform=macOS'

# iOS Simulator
xcodebuild -scheme AppName -destination 'platform=iOS Simulator,name=iPhone 16'

# iPadOS Simulator
xcodebuild -scheme AppName -destination 'platform=iOS Simulator,name=iPad Pro 13-inch (M4)'

# tvOS Simulator
xcodebuild -scheme AppName -destination 'platform=tvOS Simulator,name=Apple TV'
```

Do not invent scheme names. If the scheme is unknown, inspect the project/workspace first.

---

## Tooling Rules

If project-specific tools, Xcode-aware tools, SwiftPM tools, or MCP tools are configured, prefer them over generic shell operations when they provide safer project-aware behavior.

Useful tool categories:

- Documentation search for API availability and correct usage.
- Build and test execution when requested.
- Build log inspection.
- Package manifest inspection.
- Xcode project inspection.
- Static analysis and linter output.

Do not assume these tools exist. If they do not exist, use normal project inspection and file editing.

---

## Code Style Guidelines

- Use PascalCase for structs, classes, actors, enums, protocols, and file names.
- Use camelCase for variables, constants, functions, cases, and parameters.
- Use clear names. Avoid unclear abbreviations.
- Prefer simple English names over clever names.
- Prefer `let` over `var` unless mutation is required.
- Prefer `private` and `fileprivate` where appropriate.
- Prefer `internal` by default. Make APIs `public` or `open` only when they are part of a real module boundary.
- Single quotes are not allowed. Use double quotes.
- Avoid force-unwrapping (`!`) except:
  - inside `fatalError`
  - in tests where clearly safe
  - when explicitly marked with `// INTENTIONAL`
- Avoid force-casts (`as!`) except when explicitly justified.
- Prefer early exits with `guard` when they reduce nesting.
- Keep functions short and focused.
- Avoid deeply nested logic.
- Use comments only for non-obvious behavior, important decisions, public APIs, or tricky edge cases.
- Do not comment what the code already says.

---

## MARK Comments

Use `// MARK: -` comments to separate logical sections inside files.

Recommended order for production files:

```swift
// MARK: - Properties

// MARK: - Initialization

// MARK: - Public Methods

// MARK: - Internal Methods

// MARK: - Private Methods

// MARK: - Helpers
```

Recommended order for test files:

```swift
// MARK: - Tests

// MARK: - Helpers

// MARK: - Test Doubles
```

Rules:

- One empty line before every `// MARK: -`.
- One empty line after every `// MARK: -`.
- Never add `// MARK: -` before every small function.
- Do not overuse MARK comments.

---

## Spacing and Indentation

- Use 4 spaces.
- No tabs.
- Exactly one empty line at EOF.
- Keep argument lists readable.
- Prefer trailing commas in multi-line collection literals when the project already uses them.
- Keep closure chains readable.
- Prefer one chained call per line when the chain becomes long.

Example:

```swift
let activeUsers = users
    .filter(\.isActive)
    .sorted { $0.name.localizedStandardCompare($1.name) == .orderedAscending }
```

---

## Modern Swift API Guidelines

- Assume strict Swift concurrency rules are active unless the project proves otherwise.
- Prefer `async/await` APIs over closure-based APIs when both exist.
- Avoid old-style Grand Central Dispatch for normal concurrency. Use structured concurrency, actors, async sequences, or task groups.
- Avoid `DispatchQueue.main.async { ... }` for UI-facing updates. Use `@MainActor`, `Task`, or structured concurrency boundaries.
- Avoid detached tasks unless there is a clear isolation or lifetime reason.
- Prefer Swift-native and modern Foundation APIs.
- Prefer `URL.documentsDirectory` and `appending(path:)` over older URL construction patterns when available and appropriate.
- Prefer Swift string APIs such as `replacing(_:with:)` where available instead of older Foundation-style string APIs.
- Prefer `FormatStyle` APIs over legacy formatter classes for new code.
- Avoid legacy `DateFormatter`, `NumberFormatter`, and `MeasurementFormatter` unless existing APIs or project constraints require them.
- Prefer `localizedStandardContains()` for user-facing search unless exact technical matching is required.
- Prefer `Double` over `CGFloat` for general numeric code. Use `CGFloat` only when the API requires it.
- Prefer `count(where:)` over `filter { ... }.count` when available and when counting matching collection elements.
- Prefer `Date.now` over `Date()` for clarity.
- Prefer modern date parsing APIs when converting strings to dates.
- Avoid manual date formatting strings for user display. If unavoidable for display, use localized-safe patterns.
- If a type is repeatedly sorted with the same closure, prefer making it conform to `Comparable` so the sort order is centralized.
- Prefer `if let value { ... }` shorthand over `if let value = value { ... }` in new code.
- Omit `return` in single-expression functions and computed properties where Swift supports it.
- Use `if` and `switch` as expressions where it improves clarity.
- Use `some` and `any` intentionally. Do not hide concrete types behind protocols without a real reason.
- Use `Result` only when it is a better API shape than `async throws` or `throws`.
- Prefer non-optional values over optional values when absence is not a valid state.
- Avoid boolean parameters that make call sites unclear. Prefer named options, enums, or separate methods.

---

## Type Design Rules

- Prefer structs for values.
- Prefer classes only when reference identity, inheritance, Objective-C interoperability, or shared mutable lifetime is required.
- Prefer actors for isolated mutable state shared across concurrency domains.
- Prefer enums for finite states and command-like values.
- Prefer protocols for real abstraction boundaries, not just for every concrete type.
- Keep protocols small and behavior-focused.
- Avoid protocols with a single conforming type unless they are needed for testing, module boundaries, or future extension already present in the project.
- Avoid inheritance-heavy designs.
- Prefer composition over inheritance.
- Make invalid states unrepresentable where practical.
- Prefer strong domain types over raw strings, dictionaries, or loosely typed payloads.

Example:

```swift
struct UserID: Hashable, Codable, Sendable {
    let rawValue: String
}
```

Use this only when the stronger type improves safety or clarity. Do not wrap every primitive automatically.

---

## Access Control Rules

- Use the narrowest reasonable access level.
- Prefer `private` for implementation details inside a file.
- Use `fileprivate` only when multiple types in the same file must share implementation details.
- Use `internal` by default for module-local APIs.
- Use `package` only when the package layout benefits from package-wide access.
- Use `public` only for intended external API.
- Use `open` only when external subclassing or overriding is intentionally supported.
- Public APIs must be stable, documented when non-obvious, and tested where practical.

---

## Dependency Management

- Do not add third-party packages without explicit approval.
- Before adding a dependency, check whether the standard library or Foundation already solves the problem.
- Prefer small, well-maintained dependencies only when they remove significant complexity.
- Do not add a dependency for a trivial helper function.
- Keep dependency direction clean:
  - domain must not depend on infrastructure
  - domain must not depend on UI
  - services may depend on protocols from domain or shared layers
  - adapters implement protocols at the boundary
- Avoid circular dependencies between modules and targets.
- Keep `Package.swift` readable and explicit.

---

## Architecture Rules

### 1. General Architecture

- Keep architecture as simple as the project allows.
- Do not introduce Clean Architecture, VIPER, VIP, MVVM, Redux, or coordinator patterns unless the project already uses them or the task requires them.
- Separate responsibilities clearly:
  - domain models describe business concepts
  - services perform operations
  - repositories/adapters access external systems
  - DTOs represent transport or persistence shapes
  - mappers translate between DTOs and domain models when needed
- Keep business rules out of transport, storage, and presentation code.
- Avoid massive manager classes.
- Avoid utility dumping grounds such as `Helpers.swift` or `Utils.swift` unless the project already has that pattern.
- Prefer feature-oriented folders when the project is feature-oriented.
- Prefer layer-oriented folders when the project is layer-oriented.
- Do not change architecture style without explicit request.

### 2. Clean Architecture Boundaries

Use this structure only when the project is large enough or already organized this way:

```text
Sources/
├── Domain/
│   ├── Models/
│   ├── UseCases/
│   └── Interfaces/
├── Application/
│   ├── Services/
│   └── Workflows/
├── Infrastructure/
│   ├── Networking/
│   ├── Persistence/
│   └── ExternalAdapters/
└── Shared/
    └── Utilities/
```

Rules:

- Domain must be the most stable layer.
- Domain should not import UI frameworks, database frameworks, HTTP frameworks, or platform-specific frameworks unless unavoidable.
- Infrastructure may depend on external frameworks.
- Application/use-case layer coordinates work but should not know low-level transport details.
- Mappers should be explicit when external data shapes differ from domain shapes.

### 3. Small Project Structure

For small projects, do not over-engineer.

A simple structure is acceptable:

```text
Sources/
├── Models/
├── Services/
├── Parsers/
├── Persistence/
└── Utilities/
```

Use only the folders that are actually needed.

---

## Module and File Structure Rules

- Prefer one primary type per Swift file.
- Do not place several unrelated primary types in one file.
- Group small closely related helper types only when it improves readability.
- File names should match the primary type name.
- Keep extensions focused.
- Do not create large extension files that hide unrelated behavior.
- Put test doubles in test targets unless they are shared fixtures intentionally used by multiple test targets.
- Avoid moving files unless it is required for the task or clearly improves structure.

Example feature structure:

```text
FeatureName/
├── FeatureModel.swift
├── FeatureService.swift
├── FeatureRepository.swift
├── FeatureParser.swift
└── FeatureTests.swift
```

Do not create files that are not needed.

---

## State and Mutability Rules

- Prefer immutable values.
- Keep mutable state local and obvious.
- Avoid duplicated mutable state.
- Avoid global mutable state.
- Protect shared mutable state with actors, locks, or clearly documented synchronization.
- Prefer value transformations over in-place mutation when the data size and performance requirements allow it.
- Do not store derived values unless recomputation is expensive or the stored value is required for consistency.
- Make cache invalidation explicit.
- Avoid hidden side effects in computed properties.

---

## Swift Concurrency Rules

- Use structured concurrency.
- Prefer `async/await` over completion handlers in new code.
- Prefer `AsyncSequence` for streams of asynchronous values.
- Prefer task groups for concurrent child tasks that share a parent lifetime.
- Use actors for isolated mutable state.
- Do not use detached tasks unless there is a clear reason.
- Do not ignore task cancellation.
- Check cancellation in long-running operations.
- Use `Task.sleep(for:)` instead of `Task.sleep(nanoseconds:)` in new code.
- Do not block cooperative concurrency threads with long synchronous work.
- Do not call blocking I/O inside async code without understanding the executor impact.
- Mark types as `Sendable` when they cross concurrency boundaries and can safely do so.
- Use `@unchecked Sendable` only with a clear safety comment explaining the synchronization or immutability guarantee.
- Flag mutable shared state that is not protected by an actor, lock, or `@MainActor` boundary.
- Before adding `MainActor.run()`, check whether a cleaner actor isolation boundary is possible.
- Avoid mixing GCD and Swift concurrency without a clear bridge reason.

Example:

```swift
actor TokenStore {
    private var token: String?

    func read() -> String? {
        token
    }

    func update(_ token: String?) {
        self.token = token
    }
}
```

---

## Error Handling

- Prefer throwing errors over returning optional values when failure reason matters.
- Prefer typed domain errors for expected failure modes.
- Do not hide errors silently.
- Never use `try?` silently.
- Either propagate the error, handle it, or annotate clearly:

```swift
// INTENTIONAL: best-effort cleanup.
try? await cleanupTemporaryFiles()
```

- Use `Result` only when storing or passing success/failure as data is useful.
- Preserve underlying errors when they are useful for diagnostics.
- Keep user-facing error text separate from technical error types.
- Avoid `fatalError` in production logic except for impossible states that indicate programmer error.
- Use `precondition` only for programmer contract violations.
- Do not use assertions for recoverable runtime errors.

Example:

```swift
enum ImportError: Error, Equatable {
    case fileNotFound(path: String)
    case unsupportedFormat(String)
    case invalidData(reason: String)
}
```

---

## Data Modeling and Codable Rules

- Keep domain models separate from external DTOs when external data shape is unstable, ugly, or transport-specific.
- Do not expose raw JSON dictionaries through the codebase unless the data is truly dynamic.
- Prefer `Codable` structs for stable payloads.
- Use explicit `CodingKeys` when wire names differ from Swift names.
- Avoid lossy parsing unless documented.
- Validate required fields at the boundary.
- Keep date, number, and encoding strategies explicit.
- Do not silently ignore unknown critical fields if they affect correctness.
- Do not make all properties optional only to avoid parsing errors.
- Use optional only when absence is a valid state.

Example:

```swift
struct UserDTO: Decodable {
    let id: String
    let displayName: String

    enum CodingKeys: String, CodingKey {
        case id
        case displayName = "display_name"
    }
}
```

---

## Networking Rules

Use these rules only when the project has networking code.

- Keep networking out of domain models.
- Keep HTTP clients small and injectable.
- Prefer async APIs.
- Make request construction explicit.
- Make response validation explicit.
- Do not assume HTTP 200 is the only successful status unless the API says so.
- Decode response bodies at the boundary.
- Map transport errors to meaningful domain/application errors where appropriate.
- Do not log tokens, cookies, credentials, or sensitive payloads.
- Keep retry behavior explicit and bounded.
- Use timeouts where appropriate.
- Keep authentication concerns isolated.

Example boundary shape:

```swift
protocol UserClient: Sendable {
    func fetchUser(id: UserID) async throws -> UserDTO
}
```

Do not introduce a protocol only for one concrete networking type unless it improves testing or matches the existing architecture.

---

## Persistence and File-System Rules

Use these rules only when the project has persistence or file-system code.

- Keep persistence out of domain models unless the project intentionally uses active-record style.
- Keep file paths and storage locations explicit.
- Do not hard-code user-specific absolute paths.
- Use atomic writes for important files when possible.
- Avoid data loss on partial writes.
- Validate file existence and permissions where needed.
- Keep migrations explicit.
- Keep database models separate from domain models when persistence concerns leak into business logic.
- Do not store passwords, tokens, or private keys in plain text.
- Use Keychain or an approved secure storage layer for credentials and tokens.

---

## Logging and Diagnostics

- Do not use `print()` for production logging unless the project is a small CLI tool or already uses print-based output.
- Prefer the project’s logging system.
- For Apple platform code, use `Logger` from `OSLog` when appropriate.
- Do not log sensitive information.
- Keep logs actionable.
- Avoid noisy logs in tight loops.
- Include enough context to diagnose failures without exposing private data.

Example:

```swift
import OSLog

private let logger = Logger(subsystem: "com.example.project", category: "Import")
```

---

## Performance Rules

- Prefer clarity first, then optimize based on real evidence.
- Avoid expensive work in hot paths without measurement.
- Avoid repeated sorting, filtering, decoding, formatting, or allocation inside loops when it can be moved out safely.
- Use lazy sequences only when they improve memory or performance without hurting clarity.
- Be careful with copy-on-write collections in tight mutation loops.
- Avoid unnecessary type erasure in performance-sensitive code.
- Avoid unnecessary existential dispatch in hot paths.
- Prefer streaming large files instead of loading everything into memory when practical.
- Keep caches bounded or explicitly invalidated.
- Do not introduce micro-optimizations that make code harder to maintain without evidence.

---

## Security and Privacy Rules

- Never commit secrets, tokens, private keys, certificates, or passwords.
- Do not log secrets or sensitive user data.
- Validate and sanitize external input at boundaries.
- Treat file paths, URLs, JSON, command-line arguments, and environment variables as untrusted input.
- Avoid shelling out with unsanitized user-controlled strings.
- Prefer safe APIs over manual string command construction.
- Keep cryptographic code minimal and use platform/system libraries.
- Do not invent custom encryption, hashing, signing, or authentication protocols.

---

## Testing Rules

- Do not add tests unless requested or unless the project already has a clear test pattern and the change affects testable behavior.
- If adding tests, follow the existing test structure.
- Prefer unit tests for domain logic, parsers, mappers, services, and error handling.
- Prefer integration tests for real boundary behavior when useful.
- Avoid testing private implementation details directly.
- Test public behavior and important edge cases.
- Keep tests deterministic.
- Do not rely on wall-clock time, network access, random values, or current locale unless controlled.
- Inject clocks, UUID generators, dates, random generators, and clients when deterministic tests need them.
- Keep fixtures small and readable.
- Use descriptive test names.
- Do not over-mock. Use simple fakes/stubs where they are clearer.

Example test naming style:

```swift
func testParse_whenInputIsEmpty_returnsEmptyResult() throws {
    // Test body.
}
```

---

## Swift Testing Frameworks

Follow the project’s existing test framework.

If the project uses XCTest:

- Use `XCTestCase` consistently.
- Use `XCTAssertThrowsError` or async error assertions for expected failures.
- Keep setup explicit.

If the project uses Swift Testing:

- Use `@Test` consistently.
- Use `#expect` and `#require` idiomatically.
- Keep test data clear and local unless shared fixtures are already used.

Do not migrate test frameworks unless explicitly requested.

---

## Documentation Rules

- Public APIs should have documentation when behavior, constraints, or errors are not obvious.
- Internal code should be self-explanatory where possible.
- Use comments to explain why, not what.
- Document concurrency expectations for public async APIs, actors, callbacks, and Sendable types when needed.
- Document ownership and lifetime rules when they are not obvious.
- Keep README, ARCHITECTURE, and CHANGE documents aligned with code changes when the task touches documented behavior.

---

## Refactoring Rules

- Refactor only what is needed for the task unless explicitly asked for broader cleanup.
- Preserve behavior unless behavior change is the goal.
- Prefer mechanical, reviewable refactors.
- Keep renames separate from logic changes when possible.
- Do not combine formatting-only changes with behavior changes unless necessary.
- Do not move public types without considering API and import impact.
- After refactoring, verify call sites and tests where possible.

---

## Agent Instruction

When adding or modifying Swift code:

1. Identify the project type and target: package, app core, framework, CLI, server, test target, or mixed project.
2. Read the relevant files and call/reference paths before editing.
3. Preserve the existing architecture unless the task explicitly requires changing it.
4. Keep domain logic independent from UI, transport, storage, and framework-specific details.
5. Prefer value types, explicit dependencies, and small focused services.
6. Use structured concurrency and respect strict concurrency rules.
7. Handle errors explicitly.
8. Add or update tests only when requested or when the project pattern clearly requires it.
9. Avoid third-party dependencies unless explicitly approved.
10. Keep the diff small and reviewable.

Violation means the implementation should be reconsidered before continuing.

---

## Anti-Patterns

Do not introduce:

- Force unwraps without an explicit `// INTENTIONAL` reason.
- Force casts without a strong reason.
- Silent `try?`.
- Massive functions.
- Massive types that mix unrelated responsibilities.
- Business logic inside UI, route handlers, command-line argument parsing, or persistence models.
- Networking inside domain models.
- Persistence inside domain models unless the project intentionally uses that pattern.
- Global mutable state.
- Hidden singletons.
- Unbounded caches.
- Unnecessary protocols.
- Protocols created only because "everything needs an interface".
- Unnecessary architecture layers.
- Clean Architecture/VIP/VIPER/MVVM/Redux introduced without project need.
- God objects named `Manager`, `Service`, `Helper`, or `Utils` that collect unrelated behavior.
- Raw stringly typed domain logic where a strong type or enum would prevent mistakes.
- Optional properties used only to avoid proper initialization.
- Boolean flags that create unclear call sites.
- Callback-based APIs when async/await is available and appropriate.
- `DispatchQueue.main.async()` for ordinary actor-isolated updates.
- Detached tasks without a documented reason.
- Blocking operations inside async code without understanding the impact.
- `@unchecked Sendable` without a safety explanation.
- Public APIs made public only to satisfy tests.
- Third-party packages for trivial helpers.
- Secrets or credentials in source code.
- Logging of sensitive information.
- Large unrelated rewrites.
- Formatting-only churn mixed with behavior changes.
