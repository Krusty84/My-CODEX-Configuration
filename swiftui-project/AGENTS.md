# AGENTS.md

## Project Overview

- SwiftUI-based Apple platform project.
- This AGENTS.md is platform-neutral and may be used in different projects targeting:
  - macOS
  - iOS
  - iPadOS
  - tvOS
- The current project may target one platform or several platforms.
- Do not assume that the code must run on all Apple platforms.
- Follow the actual target platform of the current project.
- Swift 6+
- SwiftUI-first architecture.

If a project has stricter deployment targets, platform list, or architecture rules, define them in a repo-specific `AGENTS.md`.

---

## Role

Act as a **Senior Apple Platform SwiftUI Engineer**.

The code should follow:

- Apple Human Interface Guidelines for the actual target platform.
- Apple App Review expectations when the project is distributed through an Apple platform store.
- Modern Swift, SwiftUI, Observation, and Swift concurrency practices.

Do not turn this file into an iPhone-only instruction set. Always adapt rules to the actual project target: macOS, iOS, iPadOS, tvOS, or a real multi-platform combination.

---

## Core Principle

This file defines common SwiftUI development rules that apply to Apple-platform projects.

It does not mean that every project must support macOS, iOS, iPadOS, and tvOS together.

Before making platform-related decisions:

1. Identify the actual project target platform.
2. Follow the UX and API expectations of that platform.
3. Do not add cross-platform abstractions unless the project is really multi-platform.
4. Do not add platform guards only for theoretical future support.
5. Keep the code simple and native for the current platform.

---

## Mandatory Rules

- Before changing anything, read the relevant files end to end, including all call/reference paths.
- Do not modify code without reading the whole context.
- Do not guess project structure. Follow the existing folder and naming conventions.
- Prefer small, safe, incremental changes over large rewrites.
- Preserve existing public APIs unless the task explicitly requires changing them.
- Do not introduce unnecessary abstraction.
- Do not make code multi-platform unless the project already requires it or the task explicitly asks for it.
- Use platform-specific APIs when they are the correct native solution for the current target platform.
- Do not introduce third-party frameworks without explicit approval.
- Avoid UIKit/AppKit bridge code unless it is required by the target platform, required by existing architecture, or explicitly requested.
- Do not hard-code deployment targets in this global file. Follow the project deployment target from the Xcode project or repo-specific `AGENTS.md`.

---

## Platform Target Rules

### Single-Platform Projects

If the project targets only one platform, write code for that platform.

Examples:

- macOS-only project may use macOS-specific SwiftUI patterns and AppKit integration when needed.
- iOS-only project may use iOS-specific SwiftUI and UIKit integration when needed.
- iPadOS project may use iPadOS-specific layout and interaction patterns.
- tvOS-only project may use tvOS focus behavior and tvOS-specific UI assumptions.

Do not add `#if os(...)` guards only because other Apple platforms exist.

### Multi-Platform Projects

If the project explicitly targets more than one platform:

- Keep shared code platform-neutral where reasonable.
- Isolate platform-specific behavior.
- Use platform checks only where platform behavior really differs.
- Prefer small platform-specific files over large conditional blocks.

Example structure:

```text
FeatureName/
├── FeatureView.swift
├── FeatureViewModel.swift
├── FeaturePlatform+macOS.swift
├── FeaturePlatform+iOS.swift
└── FeaturePlatform+tvOS.swift
```

or:

```text
Shared/
macOS/
iOS/
tvOS/
```

Use platform checks only when required:

```swift
#if os(macOS)
import AppKit
#elseif os(iOS)
import UIKit
#elseif os(tvOS)
import UIKit
#endif
```

---

## Platform-Specific UX Rules

Use the section that matches the current project target.

### macOS

- Support resizable windows.
- Do not assume full-screen layout.
- Prefer keyboard shortcuts where useful.
- Use menus, commands, inspectors, sidebars, and toolbars when they fit the task.
- Support pointer-based interaction.
- Use hover effects when useful.
- Avoid iPhone-style navigation if a sidebar or split view is more natural.
- Prefer native macOS window, menu, and document behavior where applicable.

### iOS

- Design for touch-first interaction.
- Respect safe areas.
- Support portrait and landscape where reasonable.
- Avoid macOS-only concepts such as menu bars.
- Prefer `NavigationStack` for common navigation flows.
- Use sheets, confirmation dialogs, and toolbars in a native iOS style.
- Keep controls large enough for touch interaction.

### iPadOS

- Support larger layouts than iPhone.
- Prefer adaptive layouts.
- Use `NavigationSplitView` when the UI benefits from master-detail structure.
- Consider keyboard, pointer, and multitasking support.
- Do not treat iPadOS as only a larger iPhone.
- Support split screen and stage manager layouts where appropriate.

### tvOS

- Design for focus-based navigation.
- Use `@FocusState` and focus-aware layout when needed.
- Avoid touch-only interactions.
- Avoid tiny controls and dense layouts.
- Make selectable elements visually clear.
- Prefer simple navigation and large content areas.
- Do not assume keyboard or pointer input.
- Avoid UI that requires precise gestures.

---

## File Header

### SwiftUI View Files

Use this exact header pattern:

```swift
//
//  Filename.swift
//  ProjectName
//
//  Created by Alexey Sedoykin on DD/MM/YYYY.
//

import SwiftUI
```

### Non-View Swift Files

Use the same comment header, but import only the required modules.

Example:

```swift
//
//  FeatureViewModel.swift
//  ProjectName
//
//  Created by Alexey Sedoykin on DD/MM/YYYY.
//

import Foundation
import Observation
```

Do not import `SwiftUI` into pure model, service, parser, networking, persistence, or business-logic files unless it is really needed.

---

## Build Commands

In general, do not run `xcodebuild` commands unless explicitly requested.

If build validation is requested:

- Use the correct scheme.
- Use the correct destination for the current project platform.
- Do not invent scheme names.
- If the scheme is unknown, inspect the project/workspace first.

Examples:

```bash
xcodebuild -list
```

Platform-specific build commands must match the current project target:

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

Do not run long build/test commands unless explicitly requested.

---

## Xcode Tooling Rules

If Xcode-specific tools or MCP tools are configured, prefer them over generic file or shell tools when working with Xcode projects.

Useful tool categories:

- Documentation search: verify API availability and correct usage before writing code that depends on newer Apple APIs.
- Build project: build only when requested or when validation is explicitly part of the task.
- Build log inspection: use build logs to fix compile errors and warnings.
- Preview rendering: visually verify SwiftUI views when preview tooling is available.
- Issue navigator inspection: check Xcode-visible issues when available.
- Xcode-aware read/write/update operations: prefer them for project files and Xcode-managed files when available.

Do not assume these tools exist. If they do not exist, use normal project inspection and file editing.

---

## Code Style Guidelines

- Use PascalCase for structs, classes, enums, protocols, and file names.
- Use camelCase for variables, constants, functions, cases, and parameters.
- Use clear names. Avoid unclear abbreviations.
- Prefer simple English names over clever names.
- Use `private` where possible.
- Prefer `let` over `var` unless mutation is required.
- Single quotes are not allowed. Use double quotes.
- No force-unwrapping (`!`) except:
  - inside `fatalError`
  - in tests where clearly safe
  - when explicitly marked with `// INTENTIONAL`

---

## Modern Swift API Guidelines

- Assume strict Swift concurrency rules are active unless the project proves otherwise.
- Prefer `async/await` APIs over closure-based APIs when both exist.
- Never use old-style Grand Central Dispatch for UI updates, such as `DispatchQueue.main.async { ... }`. Use `@MainActor`, `Task`, or structured concurrency instead.
- Avoid detached tasks unless there is a clear isolation or lifetime reason.
- Prefer Swift-native and modern Foundation APIs.
- Prefer `URL.documentsDirectory` and `appending(path:)` over older URL construction patterns when available.
- Prefer Swift string APIs such as `replacing(_:with:)` where available instead of older Foundation-style string APIs.
- Do not use C-style number formatting in SwiftUI text, such as `String(format:)`, unless there is no native alternative. Prefer `Text(value, format:)` and `FormatStyle`.
- Avoid legacy `Formatter` subclasses such as `DateFormatter`, `NumberFormatter`, and `MeasurementFormatter` in new code. Prefer modern `FormatStyle` APIs.
- Use legacy formatter classes only when an existing API or project constraint requires them.
- For user-input text filtering and search, prefer `localizedStandardContains()` over plain `contains()` unless exact technical matching is required.
- Prefer static member lookup where possible, for example `.circle` instead of `Circle()` and `.borderedProminent` instead of `BorderedProminentButtonStyle()`.
- Prefer `Double` over `CGFloat` for general numeric code. Use `CGFloat` when the API requires it, or when optional / `inout` bridging makes it necessary.
- Prefer `count(where:)` over `filter { ... }.count` when counting matching collection elements.
- Prefer `Date.now` over `Date()` for clarity.
- Prefer modern date parsing APIs, such as `Date(input, strategy: .iso8601)`, when converting strings to dates.
- Avoid manual date formatting strings for user display. If manual formatting is unavoidable for display, use `y` rather than `yyyy` so localized year behavior remains correct. This does not apply to fixed API exchange formats.
- When dealing with names of people, prefer `PersonNameComponents` and modern formatting over manual string interpolation.
- If a type is repeatedly sorted with the same closure, prefer making it conform to `Comparable` so the sort order is centralized.
- Prefer `if let value { ... }` shorthand over `if let value = value { ... }` in new code.
- Omit `return` in single-expression functions and computed properties where Swift supports it.
- Use `if` and `switch` as expressions where it improves clarity.
- When `import SwiftUI` is already present, do not add `import UIKit` or `import AppKit` only for platform image/color types unless the file directly uses APIs that require the explicit import.
- Use `sensoryFeedback()` for haptics when available instead of older UIKit haptic generators.
- Use the `@Entry` macro for custom `EnvironmentValues`, `FocusValues`, `Transaction`, and `ContainerValues` keys when the deployment target supports it.
- Prefer `overlay(alignment:content:)` over deprecated `overlay(_:alignment:)`.
- Use `.topBarLeading` and `.topBarTrailing` instead of deprecated `.navigationBarLeading` and `.navigationBarTrailing` toolbar placements.
- Prefer automatic grammar agreement for supported languages when interpolating quantities, for example `Text("^[\(people) person](inflect: true)")`.
- When the deployment target supports it, fill and stroke shapes with chained modifiers instead of adding an overlay only for the stroke.
- When referencing asset catalog images, prefer the generated symbol asset API when the project is configured for generated symbols, for example `Image(.avatar)` instead of `Image("avatar")`.
- When targeting iOS 26 or later and a native SwiftUI web view is sufficient, prefer `WebView` from WebKit over a hand-wrapped `WKWebView` in `UIViewRepresentable`.
- Never concatenate `Text` values with `+`; use interpolation or a dedicated composed view instead.
- If `ObservableObject` is absolutely required, such as for a Combine-based debouncer, explicitly import `Combine`.

## MARK Comments

Use `// MARK: -` comments to separate logical sections inside files.

Recommended order for SwiftUI View files:

```swift
// MARK: - Properties

// MARK: - Body

// MARK: - Subviews

// MARK: - Actions

// MARK: - Helpers

// MARK: - Preview
```

Recommended order for ViewModel/state files:

```swift
// MARK: - Properties

// MARK: - Lifecycle

// MARK: - Public Methods

// MARK: - Private Methods

// MARK: - Helpers
```

Rules:

- One empty line before every `// MARK: -`.
- One empty line after every `// MARK: -`.
- Never add `// MARK: -` before single functions, tiny computed properties, or every small item.
- Do not overuse MARK comments.

---

## Spacing and Indentation

- Use 4 spaces.
- No tabs.
- One empty line before `var body: some View`.
- Exactly one empty line at EOF.
- Keep modifier chains readable.
- Prefer one modifier per line when a chain becomes long.

Example:

```swift
Text(title)
    .font(.headline)
    .foregroundStyle(.primary)
    .padding()
```

---

## Property Declaration Order

Declare properties in this order, from top to bottom:

1. `@Environment`, `@EnvironmentObject`
2. `@State`, `@FocusState`, `@SceneStorage`, `@AppStorage`, `@Binding`
3. `@Bindable`
4. `@Published` inside `ObservableObject` classes only
5. `@ObservedObject`, `@StateObject` for legacy or heavyweight cases only
6. Immutable `let` inputs / injected dependencies
7. `private let` constants and publishers
8. Computed formatters and helpers
9. Derived / computed `var` properties

When modifying a file, reorder properties to match this sequence unless doing so would create a risky unrelated diff.

Additional property-wrapper rules:

- `@State` should be `private` and owned only by the view that creates it.
- `@State` may also be used as a cache for expensive non-observable class instances, such as `CIContext`, when no change tracking is required.
- Never use `@AppStorage` inside an `@Observable` class, even with `@ObservationIgnored`; it will not correctly trigger view updates.
- Never use `@AppStorage` for usernames, passwords, tokens, or other sensitive data. Use Keychain or the project’s secure storage layer.

---

## Software Architecture Rules

### 1. Architecture and State Management

- Use declarative SwiftUI UI.
- Do not mix SwiftUI with UIKit/AppKit unless it is required by the current platform or existing project architecture.
- Use the Observation framework as the primary state management solution.
- Use `@Observable` as the default choice for most state models and view models.
- Prefer `@State` and `@Bindable` over `@StateObject` and `@ObservedObject` in new code.
- Reserve `@StateObject` for heavyweight objects that must survive view recreation.
- Maintain a single source of truth for application state.
- Avoid duplicated mutable state.
- Prefer explicit data passing with values, bindings, `@Bindable`, or narrowly scoped environment values.
- Do not rely heavily on `@EnvironmentObject` for frequently changing feature state.
- Apply `@MainActor` to UI-facing state owners and UI-interacting code.
- `@Observable` classes should be `@MainActor` unless the project has Main Actor default actor isolation or a clearly documented non-UI isolation model.
- Flag any UI-facing `@Observable` class that is missing `@MainActor`.
- Do not introduce `ObservableObject`, `@Published`, `@StateObject`, `@ObservedObject`, or `@EnvironmentObject` in new code unless required by legacy integration, heavyweight object lifetime, or existing architecture.
- Keep business logic outside View files.
- Prefer making model structs conform to `Identifiable` instead of repeatedly using `id: \.someProperty` in SwiftUI views.
- Avoid creating `Binding(get:set:)` inside `body`. Prefer a real binding from `@State`, `@Binding`, `@Bindable`, or similar, then use `onChange()` for side effects.
- For numeric text input, bind `TextField` directly to an `Int`, `Double`, or other numeric value using a `format:` initializer. Add the correct keyboard type for touch platforms, such as `.numberPad` or `.decimalPad`, when applicable.

Good default pattern:

```swift
@Observable
@MainActor
final class FeatureModel {
    var title = ""
    var error: Error?

    var showError: Bool {
        error != nil
    }

    func loadData() async {
        // Load data here.
    }
}
```

View ownership pattern:

```swift
struct FeatureView: View {
    @State private var model = FeatureModel()

    var body: some View {
        FeatureContentView(model: model)
    }
}
```

Child binding pattern:

```swift
struct FeatureContentView: View {
    @Bindable var model: FeatureModel

    var body: some View {
        TextField("Title", text: $model.title)
    }
}
```

---

### 2. View Design and Structure

- Views are `struct`s, never classes.
- Use `some View` return type everywhere.
- Keep views small and single-responsibility.
- If `body` exceeds 30-40 lines, refactor into smaller subviews.
- Avoid complex conditional logic directly in `body`.
- Strongly prefer extracting meaningful UI parts into dedicated `View` structs, usually in their own files, instead of growing many computed `some View` properties or `@ViewBuilder` methods.
- Prefer lazy containers for scrollable content:
  - `LazyVStack`
  - `LazyHStack`
  - `LazyVGrid`
  - `LazyHGrid`
  - `List`
- Keep View files focused on rendering, composition, and bindings.
- Prefer extracting meaningful subviews into separate `View` structs instead of building large views from many computed `some View` properties.
- Small private computed views or `@ViewBuilder` helpers are acceptable only for simple local composition that clearly belongs with `body` and would still keep `body` at an acceptable size if inlined.
- Extract button actions into methods when the action contains more than a direct single call.
- If a button action can be passed directly, prefer `Button("Label", systemImage: "plus", action: myAction)` over wrapping `myAction()` inside a closure.
- Prefer `TextField` with `axis: .vertical` over `TextEditor` unless a full-screen or rich editing experience is required. Use `lineLimit(5...)` or similar when a minimum height is needed.
- Avoid `AnyView` unless type erasure is truly required.
- Never put `Timer`, `URLSession`, `UserDefaults`, `JSONDecoder`, database code, or file-system logic directly inside a View file.
- `@State` may own local UI state and owned `@Observable` models.
- `@State` must not duplicate external source-of-truth data.
- When using `TabView(selection:)`, use an enum-backed selection value instead of an integer or string.

---

### 3. Layout Rules

- Design for the current platform first.
- Do not copy layout assumptions from another platform.
- Do not hardcode one screen size unless the platform and project require it.
- Prefer adaptive layout when the platform needs it.
- Use platform-native layout patterns.
- Prefer flexible frames over fixed frames unless the content is guaranteed to fit across device sizes, Dynamic Type sizes, and window sizes.
- Use `GeometryReader` only when necessary. Prefer newer alternatives such as `containerRelativeFrame()`, `visualEffect()`, `ViewThatFits`, layout containers, or environment values when they solve the problem more directly.
- Use `ViewThatFits`, `AnyLayout`, `NavigationStack`, and `NavigationSplitView` when they fit the current platform and feature.
- Avoid magic frame sizes.
- Never use `UIScreen.main.bounds` to determine available layout space. Use SwiftUI layout, geometry, safe-area, and container APIs instead.
- Do not force specific font sizes unless there is a clear design reason. Prefer semantic fonts and Dynamic Type-friendly sizing where applicable.
- Avoid `.caption2` for ordinary content. Use `.caption` carefully because it is already small on many platforms.
- Centralize spacing, corner radii, font choices, colors, animation durations, and design tokens in a shared constants namespace when the project has more than trivial UI.
- Do not introduce multi-platform layout logic unless the project is actually multi-platform.
- On iOS and iPadOS, interactive touch targets must be at least 44x44 points unless the platform design gives a better native control.
- Prefer `Label` over an `HStack` when placing an icon and text side by side.
- Prefer `ContentUnavailableView` for empty, missing, or unavailable content states.
- When using `searchable()`, prefer `ContentUnavailableView.search` for empty search results.
- In `Form`, wrap controls such as `Slider` in `LabeledContent` so labels and controls align correctly.
- Prefer system hierarchical styles, such as secondary and tertiary styles, over manual opacity.
- Do not specify `.continuous` for `RoundedRectangle` unless the code needs to emphasize or override the rounding style; it is already the default.

Example of project-specific platform tuning:

```swift
private let cardCornerRadius: CGFloat = 12
```

Example only for real multi-platform projects:

```swift
#if os(tvOS)
private let cardCornerRadius: CGFloat = 24
#else
private let cardCornerRadius: CGFloat = 12
#endif
```

---

### 4. Navigation Rules

- Use navigation patterns that are native for the current platform.
- Prefer `NavigationStack` for simple flows.
- Prefer `navigationDestination(for:)` for typed navigation destinations.
- Prefer `NavigationSplitView` for master-detail interfaces where appropriate.
- Avoid navigation patterns copied from another platform.
- Avoid old `NavigationView` in new code unless required for compatibility with the project deployment target.
- Prefer typed `navigationDestination(for:)` over old `NavigationLink(destination:)` patterns.
- Never mix `navigationDestination(for:)` and old `NavigationLink(destination:)` in the same navigation hierarchy.
- Register `navigationDestination(for:)` once per data type in a navigation hierarchy; flag duplicates.
- Keep navigation state explicit and testable where possible.
- Do not push platform-specific navigation behavior into unrelated business logic.
- Attach `confirmationDialog()` to the UI element that triggers it so platform presentation animations originate from the correct source.
- If an alert only has a single no-op “OK” dismissal button, omit the button block.
- Prefer `sheet(item:)` over `sheet(isPresented:)` when presenting optional data.
- When `sheet(item:)` presents a view whose only initializer argument is the item, prefer `sheet(item: $item, content: DetailView.init)`.

---

### 5. Performance Best Practices

- Ensure `body` computes quickly.
- Move expensive operations out of `body`.
- Keep view initializers small and simple. Move non-trivial work to `.task`, a model, or a service.
- Prefer `.task` over `onAppear()` for async work because SwiftUI cancels the task when the view disappears.
- Use cached computed properties, async loading, or dedicated models.
- Do not perform formatting-heavy work repeatedly in `body`.
- Avoid repeatedly creating formatters, decoders, services, or expensive derived collections in `body`.
- Prefer `Text(value, format:)` and `FormatStyle` over stored formatter properties when possible.
- Do not decode JSON, fetch network data, or scan files in `body`.
- Avoid expensive inline transforms in `List` or `ForEach` initializers, such as repeated `items.filter { ... }` or `items.sorted { ... }`.
- Prefer deriving transformed data from the source of truth using `let` values or model-level computed properties. Cache derived collections in `@State` only when explicit invalidation prevents stale UI.
- When toggling modifier values, prefer ternary values over `if` / `else` view branching where this preserves structural identity and avoids unnecessary platform view recreation.
- For large data sets in `ScrollView`, use lazy stacks or lazy grids; flag eager stacks with many children.
- If a `ScrollView` has an opaque, static, solid background, consider `scrollContentBackground(.visible)` to improve scroll-edge rendering efficiency where appropriate.
- Avoid storing escaping `@ViewBuilder` closures on views when possible. Prefer storing the built view value.

---

### 6. Previews and Development Workflow

- Adopt Preview-Driven Development.
- Keep SwiftUI previews at the bottom of each view file.
- Prefer `#Preview` for modern code.
- If `PreviewProvider` is required for compatibility, name it `Preview`.
- Create dedicated preview containers for complex state management.
- Use design-time implementations and preview-specific data.
- Keep previews fast and reliable.
- Add previews that are useful for the current platform.

Example:

```swift
#Preview("Default") {
    FeatureView()
}

#Preview("Long Content") {
    FeatureView()
}
```

For platform-specific projects, preview the important states for that platform.

---

### 7. Code Quality and Modifiers

- Extract repeated styling into custom `ViewModifier`s or view extensions.
- Define constants for spacing, corner radii, animation durations, and design tokens.
- Avoid magic numbers.
- Avoid hardcoded user-visible strings in complex projects.
- Centralize reusable strings when the project already has a localization or constants system.
- Keep modifier chains readable.
- Prefer semantic SwiftUI styles over manual styling where possible.
- Use `.privacySensitive()` for sensitive information.
- Use `foregroundStyle()` instead of `foregroundColor()` in new SwiftUI code.
- Use `clipShape(.rect(cornerRadius:))` instead of `cornerRadius()` in new SwiftUI code when the deployment target supports it.
- Use the modern `Tab` API instead of `tabItem()` when the deployment target supports it.
- Do not use the one-parameter `onChange()` modifier in new code. Use the zero-parameter or two-parameter variant.
- Prefer `Button` over `onTapGesture()` for interactive controls. Use `onTapGesture()` only when tap location, tap count, or non-control gesture behavior is required.
- If a button uses an SF Symbol or image, include an accessible text label, for example `Button("Add", systemImage: "plus", action: addItem)`.
- Prefer `ImageRenderer` over `UIGraphicsImageRenderer` when rendering SwiftUI views to images.
- Prefer `bold()` over `fontWeight(.bold)` for simple bold text. Use `fontWeight(_:)` only when a specific weight is intentional.
- When creating `ForEach` from an `enumerated()` sequence, avoid unnecessary `Array(...)` wrapping when the project deployment target supports direct sequence iteration.
- Hide scroll indicators with `.scrollIndicators(.hidden)` instead of older initializer flags when available.
- Prefer modern ScrollView positioning APIs such as `ScrollPosition` and `defaultScrollAnchor` when available. Avoid `ScrollViewReader` for new code unless it is the best compatible option.
- Avoid UIKit/AppKit colors inside SwiftUI styling unless bridging is required. Prefer semantic SwiftUI colors, asset catalog colors, and styles.
- Prefer `Label` for icon + text controls and menu triggers.
- If a `Menu` is triggered by an icon, still provide a text label, for example `Menu("Options", systemImage: "ellipsis.circle") { ... }`. Use `.labelStyle(.iconOnly)` only when the visual design must show the icon only.
- Prefer system empty-state and search-empty-state components before custom designs.
- Use `Image(decorative:)` or `.accessibilityHidden(true)` for decorative images. Add meaningful labels for non-decorative images.
- Prefer the `@Animatable` macro over manual `animatableData` when the deployment target supports it. Mark non-animated properties with `@AnimatableIgnored`.
- Never use `animation(_:)` without a `value:` parameter. Use `.animation(_:value:)`.
- Chain animations using the `completion` closure of `withAnimation()` instead of delay-based sequencing.

---

### 8. Accessibility Rules

Always consider accessibility for the current platform.

Respect the user’s accessibility settings for fonts, colors, contrast, motion, and input. Prefer semantic SwiftUI APIs so the system can adapt automatically.

Required where applicable:

- `.accessibilityLabel`
- `.accessibilityHint`
- `.accessibilityValue`
- `.accessibilityIdentifier` for UI testing when useful
- Dynamic Type support on iOS/iPadOS
- keyboard accessibility on macOS/iPadOS
- focus clarity on tvOS
- sufficient tappable/clickable/focusable area
- `.accessibilityInputLabels()` for controls whose visible labels are complex, abbreviated, or frequently changing

Do not create controls that are only understandable by color.

Additional accessibility rules:

- Support Dynamic Type on iOS and iPadOS.
- Avoid fixed font sizes. If a custom font size is required, use `@ScaledMetric` for older targets or `.font(.body.scaled(by:))` where the deployment target supports it.
- Respect Reduce Motion. Replace large motion-based animations with opacity or simpler transitions when Reduce Motion is enabled.
- If color communicates state, also provide a non-color difference such as an icon, stroke, pattern, text, or shape when `accessibilityDifferentiateWithoutColor` is enabled.
- Flag images with unclear VoiceOver output. Decorative images should be hidden from accessibility; meaningful images need labels.
- Icon-only buttons must still have accessible text labels. Use `.labelStyle(.iconOnly)` only to keep the visual icon-only appearance.
- If `onTapGesture()` is truly required, add appropriate accessibility traits such as `.accessibilityAddTraits(.isButton)`.

---

### 9. Swift Concurrency Rules

- Use modern Swift concurrency.
- Never use `DispatchQueue.main.async()` for normal UI state updates. Prefer `@MainActor` isolation or structured concurrency.
- Prefer `async/await` over completion handlers.
- Use `.task`, `.refreshable`, and async model methods.
- Use `Task.sleep(for:)` instead of `Task.sleep(nanoseconds:)` in new code.
- `.task` and `.refreshable` in Views must call model/ViewModel async entrypoints.
- Do not embed business logic directly inside View `.task`.
- Mark async entrypoints clearly.

Example:

```swift
func loadData() async {
    do {
        items = try await service.fetchItems()
    } catch {
        self.error = error
    }
}
```

Rules:

- Never use `try?` silently.
- Either propagate the error, handle it, or annotate clearly:

```swift
// INTENTIONAL: best-effort cleanup.
try? await cleanupTemporaryFiles()
```

- Do not use detached tasks unless there is a clear reason.
- UI-facing state updates must happen on the main actor.
- Flag mutable shared state that is not protected by an actor or `@MainActor`, unless the project is configured for Main Actor default isolation.
- Assume strict concurrency checking. Flag likely `@Sendable` violations and data races.
- Before adding `MainActor.run()`, check whether the project already uses Main Actor default isolation or a simpler `@MainActor` boundary.
- Avoid `DispatchQueue.global()` and other Grand Central Dispatch APIs in new code. Use actors, tasks, and structured concurrency.

---

### 10. Data and Persistence

- Prefer SwiftData for new projects when deployment targets support it.
- Use Core Data only when required by existing architecture, migrations, or compatibility.
- Keep persistence out of View files.
- Keep networking out of View files.
- Keep file-system logic out of View files.
- Use services/repositories for external data access.
- Inject dependencies where possible.
- If only a count is needed from SwiftData, consider `ModelContext.fetchCount()` with a fetch descriptor. Remember that it does not live-update by itself unless another SwiftData observation path triggers a refresh.

SwiftData + CloudKit rules, when CloudKit sync is enabled:

- Do not use `@Attribute(.unique)`.
- Model properties should have default values or be optional.
- Relationships should be optional unless the project has a tested CloudKit-compatible reason not to do so.

---

## Feature Structure Rules

When adding or modifying a feature, prefer this structure:

```text
FeatureName/
├── FeatureView.swift
├── FeatureModel.swift
├── FeatureViewModel.swift
├── FeatureService.swift
└── FeaturePreviewData.swift
```

Use only the files that are actually needed.

Additional structure rules:

- Prefer one primary type per Swift file.
- Do not place several unrelated structs, classes, enums, or protocols in one file.
- Flag files that contain multiple primary type definitions unless the project intentionally groups small closely related helper types.
- Keep secrets, API keys, tokens, certificates, and private configuration out of the repository.
- Use Keychain or an approved secure storage layer for credentials and tokens; never use `@AppStorage` for sensitive values.
- Add comments and documentation comments only when they explain non-obvious behavior, public API, or project-specific decisions.
- If SwiftLint is configured, leave the project with zero SwiftLint warnings or errors unless the task explicitly allows otherwise.

Rules:

1. Small/simple screen:
   - `FeatureView.swift`
   - optional `FeatureModel.swift`

2. Complex screen:
   - `FeatureView.swift`
   - `FeatureViewModel.swift`
   - optional service/repository files

3. Platform-specific feature behavior:
   - use the native solution for the current platform
   - isolate platform-specific code when it improves readability
   - avoid cross-platform structure unless the project is multi-platform

Do not create unnecessary layers.

---

## Agent Instruction

When adding or modifying a View:

1. Identify the current project target platform.
2. Use native SwiftUI patterns for that platform.
3. Default to an `@Observable` state model owned by `@State`.
4. Expose that model to child views with `@Bindable` when bindings are required.
5. If screen logic becomes complex, extract a dedicated `FeatureViewModel`.
6. Screen logic is complex when it has:
   - multiple data sources
   - heavy side effects
   - networking
   - persistence
   - complex navigation
   - more than 80 lines of non-UI logic
7. Keep View code focused on rendering, composition, and bindings.
8. Move business rules to the state owner.
9. Use a feature folder that contains related files.
10. Do not make the code multi-platform unless the project actually requires it.

Violation means automatic refactor is required.

---

## Error Handling

- Any screen state owner that can fail must expose:

```swift
var error: Error?
var showError: Bool
```

Example:

```swift
var showError: Bool {
    error != nil
}
```

- Views should create `Binding<Bool>` adapters from `showError` in the View layer.
- Do not hide errors silently.
- Prefer user-visible recovery where possible.
- Keep technical error details out of user-facing text unless the app is a developer tool.

Example:

```swift
private var errorBinding: Binding<Bool> {
    Binding(
        get: { model.showError },
        set: { isPresented in
            if !isPresented {
                model.error = nil
            }
        }
    )
}
```

---

## Localization and Strings

- Do not scatter user-visible strings across large features.
- For small prototypes, inline strings are acceptable.
- For production features, centralize strings using the project’s existing localization approach.
- Do not invent a localization system if the project does not have one.
- Preserve existing localization patterns.
- If the project uses `Localizable.xcstrings`, prefer symbol keys for user-facing strings, for example `Text(.helloWorld)`, following the project generation pattern.
- When adding new localization keys, offer translations for all languages already supported by the project when this is practical.

---

## Testing Rules

- Do not add tests unless requested or unless the project already has a clear test pattern.
- If adding tests, follow the existing test structure.
- Prefer unit tests for core application logic.
- Add UI tests only when the behavior cannot be tested reliably below the UI layer or when the project already requires UI test coverage.
- Test business logic in models, view models, services, and parsers.
- Do not rely on SwiftUI View tests for logic that can be tested outside the View.
- Keep UI logic simple enough that most behavior can be tested without rendering UI.
- Test platform-specific behavior only for the current target platform unless the project is multi-platform.

---

## Anti-Patterns

Do not introduce:

- Massive `body` implementations
- Business logic inside Views
- Networking inside Views
- Persistence inside Views
- Force unwraps
- Silent `try?`
- Large observable objects passed everywhere
- Duplicated mutable state
- Platform-specific assumptions copied from another platform
- Multi-platform abstractions in single-platform projects
- Platform guards that are not needed
- Magic numbers
- Unclear abbreviations
- Unnecessary architecture layers
- Old `NavigationView` in new code when `NavigationStack` or `NavigationSplitView` is available
- `foregroundColor()` in new SwiftUI code when `foregroundStyle()` is available
- `cornerRadius()` in new SwiftUI code when `clipShape(.rect(cornerRadius:))` is available
- `String(format:)` for SwiftUI number display when `Text(value, format:)` or `FormatStyle` works
- Legacy formatter classes in new code when `FormatStyle` works
- `DispatchQueue.main.async()` for ordinary UI state updates
- `Task.sleep(nanoseconds:)` in new code
- `UIScreen.main.bounds` for SwiftUI layout
- `onTapGesture()` used as a replacement for `Button`
- `AnyView` used only to avoid proper view modeling
- `GeometryReader` when a modern layout API would be simpler
- Fixed frames that break Dynamic Type, resizing, or platform adaptation
- Image-only buttons or menus without accessible text labels
- `Text` concatenation with `+`
- Deprecated toolbar placements such as `.navigationBarLeading` and `.navigationBarTrailing`
- Old `NavigationLink(destination:)` patterns where typed `navigationDestination(for:)` is appropriate
- Mixed `navigationDestination(for:)` and old `NavigationLink(destination:)` in one navigation hierarchy
- `Binding(get:set:)` created inline in complex `body` code
- `@AppStorage` inside `@Observable` classes
- `@AppStorage` for sensitive data
- Expensive sorting, filtering, decoding, formatting, or service construction in `body`
- Non-trivial work in View initializers
- Escaping `@ViewBuilder` closures stored in views when storing the built content is possible
- `animation(_:)` without a `value:` parameter
- Delay-based animation chains where `withAnimation` completion is available
