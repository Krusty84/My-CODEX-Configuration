# AGENTS.md

If the user asks "AGENTS PROJECT-SELF TEST",say:SWIFTUI_AGENTS_LOADED

## Scope

- SwiftUI-based Apple platform project.
- Swift 6+.
- SwiftUI-first architecture.
- Modern Swift, SwiftUI, Observation, and Swift concurrency are preferred.
- This file is platform-neutral and may be used for macOS, iOS, iPadOS, tvOS, or real multi-platform projects.
- Do not assume the project supports every Apple platform. Follow the actual target platform from the Xcode project, workspace, package, or repo-specific instructions.
- If a repo-specific `AGENTS.md` defines stricter deployment targets, platform rules, or architecture rules, follow the repo-specific file.

Act as a senior Apple platform SwiftUI engineer. Follow the Apple Human Interface Guidelines for the actual target platform and Apple App Review expectations when the app is distributed through an Apple platform store.

## Operating Rules

- Read the relevant files end to end before editing, including call sites, references, and nearby architecture.
- Do not guess project structure. Follow existing folder, naming, and formatting conventions.
- Prefer small, safe, incremental changes over large rewrites.
- Preserve public APIs unless the task explicitly requires changing them.
- Do not introduce unnecessary abstraction or new layers.
- Do not introduce third-party frameworks without explicit approval.
- Avoid UIKit/AppKit bridge code unless required by the target platform, existing architecture, or the task.
- Do not hard-code deployment targets in this file. Use the project settings or repo-specific instructions.
- Keep secrets, passwords, tokens, certificates, and private API keys out of the repository.
- Use Keychain or the project-approved secure storage layer for credentials and tokens.
- If `SKILL.md` is present, use it for formatting-only conventions such as MARK layout, indentation, and modifier-chain style.

## Platform Targeting and UX

Before making platform-related decisions:

1. Identify the actual target platform.
2. Use native UX and APIs for that platform.
3. Do not add cross-platform abstractions unless the project is really multi-platform.
4. Do not add `#if os(...)` guards for theoretical future support.
5. Keep the code simple and native for the current platform.

For single-platform projects, write directly for that platform. For multi-platform projects, keep shared code platform-neutral where useful, isolate platform-specific behavior, and prefer small platform-specific files over large conditional blocks.

Platform UX defaults:

- **macOS:** support resizable windows, pointer and keyboard interaction, menus, commands, inspectors, sidebars, toolbars, hover states, and native window/document behavior where appropriate. Avoid iPhone-style navigation when a split view, sidebar, or window-based layout is better.
- **iOS:** design for touch, safe areas, readable controls, native sheets/dialogs/toolbars, and `NavigationStack` for common flows. Avoid macOS-only concepts such as menu bars.
- **iPadOS:** use adaptive large-screen layout, `NavigationSplitView` where useful, multitasking, keyboard and pointer support, and Stage Manager-friendly resizing. Do not treat iPadOS as only a larger iPhone.
- **tvOS:** design for focus-based navigation, large clear controls, large content areas, and focus-aware layout. Avoid touch-only, pointer-only, keyboard-only, tiny, dense, or precision-gesture UI.

## Imports and File Headers

- SwiftUI view files should usually import only `SwiftUI`.
- Non-view Swift files should import only the modules they need, such as `Foundation`, `Observation`, `SwiftData`, or `Combine`.
- Do not import `SwiftUI` into pure model, service, parser, networking, persistence, or business-logic files unless required.
- Do not add `UIKit` or `AppKit` imports only for platform image/color types when `SwiftUI` already provides the needed API.
- If `ObservableObject` or `@Published` is required for legacy integration, explicitly import `Combine`.

## Build and Test Behavior

- Do not run `xcodebuild` unless explicitly requested.
- If build validation is requested, inspect the workspace/project first, use the correct scheme, and use the correct destination for the actual target platform.
- Do not invent scheme names or destinations.
- Do not run long build/test commands unless explicitly requested.
- Do not add tests unless requested or unless the project already has a clear test pattern.
- If adding tests, follow the existing test structure and prefer unit tests for models, view models, services, parsers, and business logic.
- Add UI tests only when behavior cannot be tested reliably below the UI layer or the project already requires UI test coverage.

Useful first command when the scheme is unknown:

```bash
xcodebuild -list
```

## General Code Style

- Use PascalCase for types and file names.
- Use camelCase for variables, constants, functions, cases, and parameters.
- Use clear names. Avoid unclear abbreviations and clever naming.
- Prefer `private` where possible.
- Prefer `let` over `var` unless mutation is required.
- Use double quotes for strings.
- Avoid force unwraps. Allow `!` only inside `fatalError`, clearly safe tests, or code marked `// INTENTIONAL`.
- Prefer one primary type per Swift file.
- Add comments only for non-obvious behavior, public API, or project-specific decisions.
- If SwiftLint or SwiftFormat is configured, follow the project configuration and leave no new warnings unless the task explicitly allows it.

## Property Declaration and Wrapper Rules

Use this order when touching a file, unless reordering would create a risky unrelated diff:

1. Environment values: `@Environment`, `@EnvironmentObject`.
2. Owned and local view state: `@State`, `@FocusState`, `@SceneStorage`, `@AppStorage`, `@Binding`.
3. Bindable observation: `@Bindable`.
4. Legacy Combine state: `@Published`, `@ObservedObject`, `@StateObject` only when required.
5. Immutable inputs and injected dependencies.
6. Private constants, helpers, computed properties, and derived values.

Wrapper rules:

- `@State` should be `private` and owned by the view that creates it.
- `@State` may own local UI state, owned `@Observable` models, or expensive non-observable caches that do not need change tracking.
- Use `@Bindable` for child views that need bindings to an `@Observable` model.
- Do not introduce `ObservableObject`, `@Published`, `@StateObject`, `@ObservedObject`, or `@EnvironmentObject` in new code unless legacy integration, heavyweight object lifetime, or existing architecture requires it.
- Never use `@AppStorage` inside an `@Observable` class, even with `@ObservationIgnored`.
- Never use `@AppStorage` for sensitive values.

## Modern Swift and SwiftUI API Guidelines

- Assume strict Swift concurrency checking is active unless the project proves otherwise.
- Prefer `async/await`, structured concurrency, actors, `.task`, and `.refreshable` over completion handlers and Grand Central Dispatch.
- Do not use `DispatchQueue.main.async()` for ordinary UI state updates. Prefer `@MainActor` isolation or structured concurrency.
- Avoid detached tasks unless there is a clear isolation or lifetime reason.
- Prefer modern Foundation and Swift APIs: `Date.now`, `URL.documentsDirectory`, `appending(path:)`, `replacing(_:with:)`, `count(where:)`, modern date parsing, and `if let value { ... }` shorthand.
- Prefer `Text(value, format:)`, `FormatStyle`, and SwiftUI formatting over `String(format:)` or legacy formatter classes. Use legacy formatters only when required.
- For user-input search and filtering, prefer `localizedStandardContains()` unless exact technical matching is required.
- Prefer static member lookup where it improves clarity, such as `.circle` or `.borderedProminent`.
- Prefer `Double` for general numeric code; use `CGFloat` when the API or bridging requires it.
- Omit `return` in single-expression functions and computed properties where Swift supports it.
- Use `if` and `switch` expressions where they improve clarity.
- Prefer `foregroundStyle()` over `foregroundColor()` in new SwiftUI code.
- Prefer `clipShape(.rect(cornerRadius:))` over `cornerRadius()` when supported.
- Prefer modern SwiftUI APIs when supported by the deployment target: `#Preview`, `Tab`, `@Entry`, `@Animatable`, `sensoryFeedback()`, `ScrollPosition`, `defaultScrollAnchor`, modern `onChange`, and modern toolbar placements such as `.topBarLeading` and `.topBarTrailing`.
- Prefer `overlay(alignment:content:)` over deprecated overlay forms.
- Never concatenate `Text` values with `+`; use interpolation or a composed view.
- Prefer generated asset symbol APIs such as `Image(.avatar)` when the project is configured for generated symbols.
- Prefer `Button` over `onTapGesture()` for controls. Use gestures only when gesture-specific behavior is required.
- Prefer `Label` for icon + text controls and menu triggers.
- Icon-only buttons and menus must still have accessible text labels.
- Use `Image(decorative:)` or `.accessibilityHidden(true)` for decorative images.
- Never use `animation(_:)` without a `value:` parameter.
- Prefer `withAnimation` completion handlers over delay-based animation chains when sequencing animations.

## SwiftUI Architecture

### State and Observation

- Use declarative SwiftUI UI.
- Use the Observation framework as the default state system.
- Use `@Observable` for most state models and view models.
- UI-facing `@Observable` classes should be `@MainActor` unless the project has Main Actor default isolation or a documented non-UI isolation model.
- Flag UI-facing observable state owners that are missing a clear main-actor boundary.
- Maintain a single source of truth. Avoid duplicated mutable state.
- Prefer explicit data passing with values, bindings, `@Bindable`, or narrowly scoped environment values.
- Do not rely heavily on `@EnvironmentObject` for frequently changing feature state.
- Keep business logic outside View files and inside state owners, services, parsers, or domain types.
- Prefer `Identifiable` model structs instead of repeated `id: \.someProperty` in SwiftUI views.
- Avoid `Binding(get:set:)` inside `body`; prefer a real binding and handle side effects with `onChange()`.
- For numeric text input, bind `TextField` directly to numeric values with a `format:` initializer and add a native keyboard type on touch platforms when applicable.

Default ownership pattern:

```swift
@Observable
@MainActor
final class FeatureModel {
    var title = ""
    var error: Error?

    var showError: Bool { error != nil }

    func loadData() async {
        // Load data here.
    }
}

struct FeatureView: View {
    @State private var model = FeatureModel()

    var body: some View {
        FeatureContentView(model: model)
    }
}

struct FeatureContentView: View {
    @Bindable var model: FeatureModel

    var body: some View {
        TextField("Title", text: $model.title)
    }
}
```

### View Design

- Views are `struct`s, never classes.
- Use `some View` return types.
- Keep views small and single-responsibility.
- If `body` grows beyond roughly 30-40 lines, extract meaningful subviews.
- Prefer dedicated `View` structs, usually in their own files, over many large computed `some View` properties or `@ViewBuilder` methods.
- Small private computed views are acceptable only for simple local composition.
- Extract button actions into methods when the action is more than one direct call.
- Prefer direct action references, such as `Button("Add", systemImage: "plus", action: addItem)`.
- Prefer lazy containers for scrollable content: `List`, `LazyVStack`, `LazyHStack`, `LazyVGrid`, and `LazyHGrid`.
- Avoid `AnyView` unless type erasure is truly required.
- Keep `Timer`, `URLSession`, `UserDefaults`, `JSONDecoder`, database code, and file-system logic out of View files.
- Use enum-backed selection values for `TabView(selection:)`.

### Layout and Navigation

- Design for the current platform first.
- Do not copy layout assumptions from another platform.
- Prefer adaptive layout, semantic fonts, Dynamic Type-friendly sizing, flexible frames, and platform-native layout patterns.
- Avoid magic frame sizes, fixed screen assumptions, and `UIScreen.main.bounds`.
- Use `GeometryReader` only when necessary. Prefer SwiftUI layout, safe-area, container, `ViewThatFits`, `AnyLayout`, `containerRelativeFrame()`, `visualEffect()`, and environment APIs when they solve the problem directly.
- Centralize spacing, radii, colors, typography, animation durations, and design tokens when the UI is more than trivial.
- On iOS and iPadOS, interactive touch targets should be at least 44x44 points unless a native control provides a better pattern.
- Prefer `ContentUnavailableView` for empty, missing, unavailable, and empty-search states.
- Prefer semantic styles and hierarchy over manual opacity.
- Use navigation patterns native to the current platform.
- Prefer `NavigationStack` for simple flows and `NavigationSplitView` for master-detail layouts.
- Prefer typed `navigationDestination(for:)` and explicit navigation state.
- Avoid old `NavigationView` and old `NavigationLink(destination:)` patterns in new code when modern navigation is available.
- Do not mix typed navigation destinations and old destination links in one hierarchy.
- Register `navigationDestination(for:)` once per data type in a navigation hierarchy.
- Prefer `sheet(item:)` over `sheet(isPresented:)` when presenting optional data.
- Attach `confirmationDialog()` to the UI element that triggers it.

### Performance

- Keep `body` fast and side-effect-free.
- Move expensive work out of `body`, view initializers, `List`, and `ForEach` initializers.
- Do not repeatedly sort, filter, decode, format, scan files, or construct services in `body`.
- Use `.task` for async view loading because SwiftUI cancels it when the view disappears.
- `.task` and `.refreshable` in Views should call model or view-model async entrypoints, not contain business logic.
- Use cached derived data carefully and invalidate it explicitly when needed.
- Prefer lazy stacks/grids for large scrollable data sets.
- Prefer storing built view values over escaping `@ViewBuilder` closures when possible.

### Accessibility

- Respect accessibility settings for fonts, colors, contrast, motion, and input.
- Use semantic SwiftUI APIs so the system can adapt automatically.
- Add labels, hints, values, identifiers, input labels, and traits where useful.
- Support Dynamic Type on iOS and iPadOS.
- Support keyboard accessibility on macOS and iPadOS.
- Ensure focus clarity on tvOS.
- Do not make state understandable by color alone.
- Respect Reduce Motion and Differentiate Without Color.
- Decorative images should be hidden from accessibility; meaningful images need useful labels.
- If `onTapGesture()` is truly required for an interactive element, add appropriate accessibility traits.

## Concurrency and Error Handling

- Use modern Swift concurrency.
- Use `Task.sleep(for:)` instead of `Task.sleep(nanoseconds:)` in new code.
- UI-facing state updates must happen on the main actor.
- Flag mutable shared state that is not protected by an actor or `@MainActor`, unless the project uses Main Actor default isolation.
- Before adding `MainActor.run()`, check whether a simpler `@MainActor` boundary is better.
- Avoid `DispatchQueue.global()` and other GCD APIs in new code.
- Never use `try?` silently. Propagate the error, handle it, or annotate intentional best-effort behavior.

Any screen state owner that can fail should expose:

```swift
var error: Error?
var showError: Bool { error != nil }
```

Views may create a `Binding<Bool>` adapter from `showError` to present alerts and clear the error on dismissal. Do not hide errors silently. Prefer user-visible recovery where possible, but keep technical details out of user-facing text unless the app is a developer tool.

## Data, Persistence, and External Services

- Keep networking, persistence, parsing, and filesystem logic out of View files.
- Use services, repositories, parsers, or storage controllers for external data access.
- Inject dependencies where possible.
- Prefer SwiftData for new persistence when deployment targets and project direction support it.
- Use Core Data when the project already uses Core Data, requires migrations, or must preserve an existing `.xcdatamodeld` model.
- If only a count is needed from SwiftData, consider `ModelContext.fetchCount()` with a fetch descriptor. Remember that it does not live-update by itself unless another SwiftData observation path triggers a refresh.
- When CloudKit sync is enabled for SwiftData, avoid `@Attribute(.unique)`, give model properties default values or make them optional, and make relationships optional unless the project has a tested compatible reason.

## Localization and Strings

- Preserve the existing localization pattern.
- Do not invent a localization system if the project does not have one.
- Inline strings are acceptable for small prototypes.
- For production features, centralize strings using the project’s existing approach.
- If the project uses `Localizable.xcstrings`, prefer generated symbol keys where available, such as `Text(.helloWorld)`.
- When adding localization keys, provide translations for all languages already supported by the project when practical.

## Repository Layout

Follow the structure that already exists in the repository. Do not reorganize files unless the task explicitly asks for a structural refactor. Do not create empty folders only because they are listed here.

Preferred default layout:

```text
ProjectRoot/
├── AppNameApp.swift or existing app entry point
├── MainWindow.swift or existing top-level scene/window composition
├── Views/
│   └── FeatureName/
│       ├── FeatureNameView.swift
│       ├── FeatureNameViewModel.swift
│       ├── FeatureNameModel.swift
│       ├── FeatureNamePreviewData.swift
│       └── Components/
│           └── FeatureNameSubView.swift
├── API/
│   ├── APIConfig.swift
│   ├── ServiceNameAPIService.swift
│   ├── ServiceNameConnector.swift
│   └── ServiceNameModel.swift
├── Helpers/
│   ├── LoggerHelper.swift
│   └── DomainName/
│       └── DomainNameParser.swift
├── DataStorage/
│   ├── StorageController.swift
│   ├── DataModel/
│   │   └── ModelName.xcdatamodeld
│   └── GeneratedPersistenceTypes.swift
└── Project configuration files
```

Folder policy:

- **Root:** keep root-level Swift files limited to app entry points, top-level scene/window composition, and project-level configuration. Do not put feature screens, networking, parsers, persistence controllers, or reusable helpers in the root.
- **Views/**: place SwiftUI screens and tightly coupled UI-facing state owners under `Views/<FeatureName>/`. Use `Components/` only when a feature has several local reusable subviews. Do not put API services, persistence, or general helpers inside `Views/`.
- **API/**: place networking, external service integrations, connectors, request/response DTOs, and non-secret API configuration here. Keep credentials out of the repo.
- **Helpers/**: place reusable utilities, pure helpers, parsers, formatters, caches, adapters, and small domain utility code here. Avoid vague dumping-ground files such as `Utils.swift`, `Common.swift`, or `Misc.swift`.
- **DataStorage/**: place local persistence, Core Data, SwiftData, storage controllers, migrations, generated persistence types, and `.xcdatamodeld` packages here.

Feature placement rules:

- Small screen: `Views/FeatureName/FeatureNameView.swift`.
- Screen with UI state or async loading: add `Views/FeatureName/FeatureNameViewModel.swift` or an `@Observable` feature model.
- Screen with external API access: keep the View/ViewModel in `Views/FeatureName/` and put the service/model/connector in `API/`.
- Screen with local persistence: keep UI in `Views/FeatureName/` and persistence in `DataStorage/` or the existing storage layer.
- Screen with parsing, caching, or reusable technical support: put reusable code in `Helpers/` or `Helpers/<DomainName>/`.
- Platform-specific feature behavior should use the native solution for the current platform and be isolated only when it improves readability.
- Do not create a top-level `ViewModels/` folder or root-level feature folder unless the current repository already uses that convention.
- Do not rename an existing app entry point only to satisfy a generic convention.
- Do not move files only for cosmetic reasons.

## View Modification Workflow

When adding or modifying a View:

1. Identify the current project target platform.
2. Use native SwiftUI patterns for that platform.
3. Default to an `@Observable` state model owned by `@State`.
4. Expose the model to child views with `@Bindable` when bindings are required.
5. Extract a dedicated feature state model or view model when screen logic becomes complex.
6. Keep View code focused on rendering, composition, and bindings.
7. Move business rules, data access, parsing, persistence, and heavy side effects out of the View.
8. Place files according to the repository layout section.
9. Do not make code multi-platform unless the project actually requires it.

Screen logic is complex when it has multiple data sources, heavy side effects, networking, persistence, complex navigation, or more than roughly 80 lines of non-UI logic.

## Reduced Anti-Patterns

Do not introduce:

- Massive `body` implementations.
- Business logic, networking, persistence, decoding, file scanning, or service construction inside Views.
- Force unwraps or silent `try?`.
- Duplicated mutable state or large observable objects passed everywhere.
- Missing `@MainActor` boundaries on UI-facing state owners.
- Unnecessary architecture layers or vague helper files.
- Platform assumptions copied from another platform.
- Multi-platform abstractions or platform guards in single-platform projects.
- Fixed frames, magic sizes, or `UIScreen.main.bounds` for SwiftUI layout.
- Expensive sorting, filtering, formatting, or data loading in `body`.
- Old navigation APIs when modern `NavigationStack`, `NavigationSplitView`, and typed destinations are available.
- Mixed old and typed navigation patterns in one hierarchy.
- `onTapGesture()` as a replacement for `Button`.
- `AnyView` used only to avoid proper view modeling.
- Image-only buttons or menus without accessible text labels.
- `Text` concatenation with `+`.
- Deprecated toolbar placements.
- `DispatchQueue.main.async()` for ordinary UI state updates.
- `@AppStorage` inside `@Observable` classes or for sensitive data.
- `animation(_:)` without a `value:` parameter.
- Delay-based animation chains when animation completion handlers are available.
