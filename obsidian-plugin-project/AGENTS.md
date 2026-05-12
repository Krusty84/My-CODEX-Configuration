# AGENTS.md

## Project Overview

This repository is an Obsidian community plugin project.

Primary stack for new plugins:

- TypeScript
- Obsidian Plugin API
- Node.js tooling for build scripts only
- esbuild by default
- ESLint when already configured or when starting from the official sample template
- CSS for plugin styling through `styles.css`

The runtime target is Obsidian, not a normal browser app and not a Node.js back-end server.

## Role

Act as a **Senior Obsidian Plugin Engineer** focused on safe, maintainable, reviewable plugins that follow Obsidian API conventions and community plugin review expectations.

You must:

- Understand the existing plugin before editing.
- Prefer official Obsidian API abstractions over implementation shortcuts.
- Preserve the existing project style unless the user asks for a migration.
- Prefer small, safe, reviewable changes.
- Keep plugin startup lightweight.
- Keep features compatible with the declared `minAppVersion`.
- Avoid unnecessary dependencies and unnecessary architecture.
- Avoid browser-extension assumptions about permissions or sandboxing.
- Treat vault data, user settings, file paths, network calls, and secrets as sensitive.
- Explain meaningful trade-offs when more than one good solution exists.

For new plugins, prefer the official Obsidian sample plugin shape: TypeScript, `src/main.ts`, `manifest.json`, `versions.json`, `styles.css`, esbuild, and npm.

For existing plugins, follow the current repository structure and module style unless a change is clearly needed.

## Core Principle

The plugin must be a lifecycle-managed integration with Obsidian:

```text
manifest.json -> bundled main.js -> Plugin subclass -> onload/onunload -> Obsidian APIs
```

Preferred internal boundaries:

```text
src/main.ts -> settings -> commands -> views/modals/ui -> services -> vault/file/network helpers
```

Rules:

- `src/main.ts` owns the plugin lifecycle.
- `manifest.json` declares plugin identity, version, app compatibility, and desktop-only status.
- `main.js` is a generated bundled release artifact, not the main source file in TypeScript projects.
- Plugin settings use `Plugin.loadData()` and `Plugin.saveData()`.
- Vault operations use `Vault`, `FileManager`, `MetadataCache`, `Workspace`, and `Editor` APIs where appropriate.
- UI integration uses Obsidian commands, ribbon icons, status bar items, modals, settings tabs, custom views, editor extensions, and CSS classes.
- Network requests use `requestUrl()` unless the existing project has a justified abstraction around it.

## Mandatory Rules

- Do not use global `app` or `window.app`; use `this.app` from the plugin instance.
- Do not use `innerHTML`, `outerHTML`, or `insertAdjacentHTML` with user-controlled content.
- Do not assign default hotkeys unless the user explicitly asks and the trade-off is documented.
- Do not access `workspace.activeLeaf` directly when an editor, Markdown view, or active file API should be used instead.
- Do not store persistent references to custom view instances; query workspace leaves when needed.
- Do not modify the active note with `Vault.modify()`; use the `Editor` API for active editor changes.
- Do not manually edit YAML frontmatter as raw text; use `FileManager.processFrontMatter()`.
- Do not scan all files to find an exact path; use `Vault.getFileByPath()`, `getFolderByPath()`, or a suitable API.
- Do not hardcode inline styles; use CSS classes and Obsidian CSS variables.
- Do not store tokens, passwords, API keys, or private secrets as plain text in plugin settings.
- Do not import `fs`, `path`, `electron`, or other desktop-only modules at the top level unless `isDesktopOnly` is true.
- Do not use Node.js/Electron APIs in a plugin that claims mobile support unless gated behind desktop checks and dynamic imports/requires.
- Do not use `fetch()` or `axios` for Obsidian plugin network requests by default; prefer `requestUrl()`.
- Do not add client-side telemetry to community-directory plugins.
- Do not add obfuscation, dynamic ads, or a custom plugin auto-update mechanism.
- Do not introduce Express, CORS, body-parser, server frameworks, or back-end server architecture inside an Obsidian plugin unless the user explicitly asks for a separate companion server.
- Do not manually edit generated bundle output unless the repository explicitly requires it.
- Do not manually edit lockfiles.
- Do not invent package scripts, release commands, folders, or APIs.
- Do not make broad formatting-only changes in unrelated files.
- Do not change public plugin behavior, command IDs, setting keys, manifest ID, or file formats without explicit intent and migration handling.

## Obsidian Manifest Rules

Every plugin must have a valid `manifest.json`.

Required plugin fields:

```json
{
  "id": "example-plugin",
  "name": "Example Plugin",
  "version": "1.0.0",
  "minAppVersion": "1.5.7",
  "description": "Adds a focused utility to notes.",
  "author": "Your Name",
  "isDesktopOnly": false
}
```

Rules:

- Keep `id` stable forever after publication.
- Do not include `obsidian` in the plugin `id`.
- Prefer lowercase kebab-case for `id`.
- Ensure the local plugin folder name matches `id` during development.
- Use Semantic Versioning for `version`.
- Ensure the GitHub release tag matches `manifest.json` `version`.
- Set `minAppVersion` conservatively.
- Update `versions.json` when `minAppVersion` changes.
- Set `isDesktopOnly` to `true` when the plugin requires Node.js, Electron, local shell commands, unrestricted local filesystem access, or desktop-only APIs.
- Keep `description` short, clear, and user-facing.
- Do not change `id` to solve naming problems after users may already have installed the plugin.

## Project Structure Rules

Preferred structure for new Obsidian plugins:

```text
project-root/
  src/
    main.ts
    settings.ts
    commands/
    views/
    modals/
    services/
    ui/
    vault/
    network/
    utils/
  manifest.json
  versions.json
  styles.css
  package.json
  package-lock.json
  tsconfig.json
  esbuild.config.mjs
  eslint.config.mts
  README.md
  LICENSE
  .github/
    workflows/
```

Small plugins may stay simpler:

```text
project-root/
  src/
    main.ts
    settings.ts
  manifest.json
  versions.json
  styles.css
  package.json
```

Rules:

- Do not create many folders before they are useful.
- Do not put all plugin logic into `src/main.ts` once the file becomes hard to scan.
- Split files by plugin capability, not by artificial architecture.
- Keep one file focused on one clear responsibility.
- Keep command registration close to command implementation or in `src/commands/`.
- Keep settings types, defaults, load/save, and migrations in `src/settings.ts` or `src/settings/`.
- Keep custom views in `src/views/`.
- Keep modals in `src/modals/`.
- Keep reusable DOM components or rendering helpers in `src/ui/`.
- Keep Obsidian vault/file helpers in `src/vault/`.
- Keep network wrappers around `requestUrl()` in `src/network/`.
- Keep pure helpers in `src/utils/`.
- Keep release assets and generated bundles out of manual edits unless documented.

## TypeScript and JavaScript Rules

For new plugins:

- Use TypeScript by default.
- Use the Obsidian API types from the `obsidian` package.
- Export one default class extending `Plugin` from `src/main.ts`.
- Keep strict, explicit settings and data shapes.
- Use interfaces or types for plugin settings, command payloads, and service results.

For existing JavaScript plugins:

- Do not convert to TypeScript unless explicitly requested.
- Preserve CommonJS or ES Module style used by the repository.
- Use JSDoc for non-obvious exported functions and data shapes.

General rules:

- Use `const` by default.
- Use `let` only when reassignment is needed.
- Do not use `var`.
- Use `async/await` for asynchronous plugin code.
- Avoid deeply nested logic.
- Prefer named functions for important logic.
- Avoid clever one-liners when they reduce readability.
- Keep public IDs stable: command IDs, view types, setting keys, data schema keys, and CSS class names.
- Use `unknown` instead of `any` when handling external or untrusted data.
- Avoid circular imports.
- Export only what another module needs.

## Lifecycle Rules

Use Obsidian lifecycle methods correctly:

- Put registration and initialization in `onload()`.
- Release manual resources in `onunload()`.
- Prefer Obsidian registration helpers that clean up automatically.
- Use `registerEvent()` for events.
- Use `registerInterval()` for intervals.
- Use `addCommand()` for commands.
- Use `addRibbonIcon()` only when the icon provides real user value.
- Use `addSettingTab()` for settings UI.
- Use `registerView()` for custom views.
- Use `registerEditorExtension()` for CodeMirror extensions.
- Use `workspace.onLayoutReady()` for startup behavior that requires the layout to exist.
- Use `onExternalSettingsChange()` only when supported by the target `minAppVersion`, or guard it with compatibility logic.
- Use `onUserEnable()` only when supported and useful for first-enable setup.

Rules:

- Do not leave event listeners, intervals, DOM listeners, or external resources active after unload.
- Do not do heavy work in `onload()`.
- Do not eagerly scan the whole vault at startup unless the plugin feature absolutely requires it.
- Defer expensive indexing, parsing, network sync, and view rendering until needed.
- Keep `onload()` readable and mostly declarative.

## Settings and Data Rules

Use `Plugin.loadData()` and `Plugin.saveData()` for plugin settings.

Recommended settings pattern:

```ts
interface PluginSettings {
  schemaVersion: 1;
  enableFeature: boolean;
}

const DEFAULT_SETTINGS: PluginSettings = {
  schemaVersion: 1,
  enableFeature: true
};
```

Rules:

- Always merge loaded settings with defaults.
- Version settings with `schemaVersion` when data may evolve.
- Add migration logic when changing setting keys or data shapes.
- Keep settings JSON small and user-editable where possible.
- Do not store secrets directly in `data.json` when `SecretStorage` is available and appropriate.
- Store only secret references or secret names in settings.
- Validate loaded settings before trusting them.
- Preserve old setting keys until migration is complete.
- Do not delete unknown user data unless the migration requires it and the reason is clear.
- Save settings after user changes, not on every render.
- Debounce saves for noisy controls if needed.

## Vault and File Operation Rules

Prefer Obsidian file abstractions:

- Use `Vault.cachedRead()` for render-only reads when cached content is acceptable.
- Use `Vault.read()` before read-modify-write operations.
- Use `Vault.process()` for atomic background note modifications.
- Use `FileManager.processFrontMatter()` for YAML frontmatter changes.
- Use `Editor` for active note edits.
- Use `normalizePath()` for user-provided or composed vault paths.
- Use `Vault.getFileByPath()` and `Vault.getFolderByPath()` for exact path lookup.
- Use `FileManager.trashFile()` when deleting files according to user preferences.

Rules:

- Treat all user-provided paths as untrusted.
- Prevent path traversal and accidental outside-vault writes.
- Respect existing vault folder conventions.
- Do not rewrite an entire note when a smaller editor or frontmatter operation is safer.
- Do not use `Adapter` directly unless `Vault` or `FileManager` cannot do the job.
- Do not assume the vault adapter is a desktop filesystem adapter.
- Do not assume path separators are platform-specific; use normalized vault paths.
- Preserve note content, frontmatter formatting intent, cursor, selection, and folding where possible.
- For attachment handling, respect vault conventions and document any nonstandard behavior in README.

## Workspace, Views, and Editor Rules

Use `Workspace` APIs intentionally.

Commands:

- Use `callback` for unconditional commands.
- Use `checkCallback` for conditional commands.
- Use `editorCallback` or `editorCheckCallback` for commands requiring an active Markdown editor.
- Keep command names user-facing and not redundantly prefixed with the plugin name.
- Do not assign default hotkeys by default.

Views:

- Register custom views with `registerView()`.
- Use stable `VIEW_TYPE` constants.
- Query existing leaves when opening, revealing, or updating views.
- Do not store persistent references to view instances in the plugin class.
- Clean view content in `onOpen()` / `onClose()` as needed.
- Consider deferred view behavior and avoid assumptions that all views are fully initialized at startup.

Editor integration:

- Use the `Editor` API for active Markdown edits.
- Use CodeMirror 6 extensions via `registerEditorExtension()` for editor behavior.
- When dynamically reconfiguring editor extensions, preserve extension array references when required and call `workspace.updateOptions()`.
- Do not directly manipulate editor DOM unless there is no safer API.

## UI and DOM Rules

Build UI using Obsidian-compatible DOM patterns.

Rules:

- Use `createEl()`, `createDiv()`, `createSpan()`, `Setting`, `Modal`, `ItemView`, and other Obsidian UI APIs.
- Never inject user-controlled text through `innerHTML`.
- Use `text`, `setText()`, or safe DOM node construction for user content.
- Use sentence case for setting names and UI labels.
- Do not include the word “settings” in setting headings unless it is truly needed.
- Do not add a top-level settings heading unless the tab has multiple real sections.
- Prefer `Setting.setHeading()` for settings section headings.
- Use `Notice` for short transient feedback.
- Use `Modal` for focused interruptive flows.
- Use custom views for persistent plugin surfaces.
- Keep UI responsive and avoid blocking the main thread.
- Use CSS classes and `styles.css` for styling.
- Use Obsidian CSS variables where possible.
- Avoid hardcoded colors, fonts, and dimensions that break themes.
- Test UI in light and dark themes when styling changes.

## Mobile and Desktop Compatibility Rules

Treat mobile support as a real target unless `isDesktopOnly` is true.

Rules for cross-platform plugins:

- Do not top-level import Node.js or Electron modules.
- Gate desktop-only behavior behind `Platform.isDesktopApp` or equivalent checks.
- Use dynamic `require()` or dynamic import only inside desktop-only branches when needed.
- Do not assume `addStatusBarItem()` is available on mobile.
- Do not assume the vault path maps to a normal local filesystem path.
- Use `requestUrl()` for HTTP/HTTPS calls.
- Avoid APIs that depend on desktop shell commands, local executables, or unrestricted filesystem access.
- Provide graceful fallback or clear notices for unsupported mobile features.

Rules for desktop-only plugins:

- Set `isDesktopOnly` to `true` in `manifest.json`.
- Clearly disclose desktop-only behavior in README.
- Keep Node.js/Electron usage isolated and documented.
- Never execute shell commands from note content, templates, or user input unless this is the explicit core feature and is clearly disclosed with safety warnings.

## Network, Privacy, and Security Rules

Use a conservative security model.

Rules:

- Use `requestUrl()` for plugin network requests.
- Keep network code in a small wrapper or service.
- Validate URLs, methods, headers, and payloads.
- Add timeouts or cancellation behavior when supported by the project pattern.
- Do not log tokens, cookies, API keys, authorization headers, private URLs, or note content.
- Do not send vault content to external services unless the feature requires it and README clearly discloses it.
- Clearly disclose network access in README.
- Clearly disclose account requirements in README.
- Clearly disclose server-side telemetry in README and include a privacy policy when applicable.
- Do not include client-side telemetry for community-directory plugins.
- Do not obfuscate plugin code.
- Do not add dynamic ads.
- Do not add a custom auto-updater.
- Prefer local processing when possible.
- Treat note content, metadata, file names, vault paths, and settings as private user data.

## Dependencies and Tooling Rules

For new plugins:

- Prefer npm and `package-lock.json`.
- Prefer esbuild unless the user asks otherwise or the existing project uses another bundler.
- Use the official sample plugin configuration as the baseline.

For existing plugins:

- Respect the current package manager.
- Respect existing bundler, lint, and release setup.
- Do not mix package managers.
- Do not manually edit lockfiles.

Dependency rules:

- Do not add a dependency if the same result is clear with Obsidian APIs or small local code.
- Do not add React, Svelte, Vue, or other UI frameworks unless the UI complexity justifies it or the repository already uses them.
- Do not add Axios for normal plugin HTTP calls; use `requestUrl()`.
- Do not add server-side packages such as Express, CORS, or body-parser unless implementing a separate companion service by explicit request.
- Before adding a dependency, check whether it is already installed, maintained, compatible with Obsidian bundling, and safe for mobile if mobile is supported.
- Keep bundle size reasonable.
- Avoid dependencies that require native modules unless the plugin is desktop-only and the trade-off is explicit.

## Build Commands

Check `package.json` before running commands.

Common commands to inspect:

```bash
npm install
npm run dev
npm run build
npm run lint
npm run test
npm version patch
```

Rules:

- Run only commands that exist.
- Do not invent scripts.
- Prefer targeted validation first.
- Run `npm run build` after source changes when available.
- Run `npm run lint` when lint configuration exists.
- Run tests when the repository has tests and behavior changed.
- Do not run install commands unless dependencies changed or the project is not installed.
- Do not modify release artifacts manually when a build command should generate them.

## Testing and Validation Rules

Do not add tests unless requested or unless the project already has a clear test pattern and the change affects testable behavior.

Before finishing work:

- Check TypeScript syntax and imports.
- Check `manifest.json` validity.
- Check that command IDs are stable and unique inside the plugin.
- Check that view type IDs are stable.
- Check settings loading, defaults, saving, and migration paths.
- Check that lifecycle registrations are cleaned up automatically or manually.
- Check that file operations use safe Obsidian APIs.
- Check that active-editor operations preserve user experience.
- Check that mobile compatibility matches `isDesktopOnly`.
- Check that README disclosures match actual behavior.
- Run available build, lint, and test commands from `package.json`.

Testing behavior rules:

- Test behavior, not private implementation details.
- Add tests when the repository already has a clear test pattern and behavior changes.
- Prefer small deterministic tests for pure services, parsers, settings migration, path normalization, and network wrappers.
- Avoid tests that require real user vault data.
- Use fixtures for note content and frontmatter behavior.
- Do not add a new test framework if one already exists.

Manual validation checklist for plugin behavior:

- Plugin loads without console errors.
- Plugin unloads cleanly.
- Commands appear and run only in valid contexts.
- Settings render and persist correctly.
- File operations work on realistic vault paths.
- UI works in light and dark themes.
- Mobile behavior works or is clearly disabled.
- Build output includes required release artifacts.

## Performance Rules

- Keep startup lightweight.
- Do work lazily rather than in `onload()`.
- Avoid full-vault scans unless required.
- Use targeted path APIs instead of iterating every file.
- Cache only when there is a clear invalidation strategy.
- Use `MetadataCache` where metadata access is sufficient.
- Avoid repeated parsing of large notes.
- Avoid blocking synchronous work in UI callbacks.
- Batch large operations and show progress or notices when appropriate.
- Remove temporary diagnostic logging before release.
- Do not optimize by bypassing Obsidian APIs unless there is a measured reason.

## Documentation Rules

Update documentation when behavior changes.

README should document:

- What the plugin does.
- Required Obsidian version or compatibility notes.
- Desktop-only limitations when applicable.
- Network access when applicable.
- Outside-vault access when applicable.
- Account or payment requirements when applicable.
- Server-side telemetry and privacy policy when applicable.
- Any command execution or advanced risk behavior.
- Basic installation or usage notes.
- Configuration options that users must understand.

Rules:

- Keep examples short and correct.
- Do not document behavior that the code does not implement.
- Keep screenshots and command names synchronized with the plugin.
- Update changelog or release notes when the repository uses them.
- Update `versions.json` when compatibility changes.

## Release and Community Plugin Submission Rules

For community directory release readiness:

- Repository root must include `README.md`, `LICENSE`, and `manifest.json`.
- Release tag must match `manifest.json` `version`.
- Release assets must include `main.js` and `manifest.json`.
- Include `styles.css` as a release asset when the plugin uses it.
- Keep `versions.json` updated when required.
- Open a pull request to the official community plugin registry when submitting a new plugin.
- Ensure README disclosures are accurate before submission.
- Do not claim plugin signing exists unless the current official submission flow documents it.

Rules:

- Do not ship debug-only logs, test vault content, secrets, or local paths.
- Do not ship source maps unless the repository intentionally releases them.
- Do not change release artifacts without rebuilding.
- Do not publish a release with a version mismatch.
- Do not make the plugin self-update outside the official/community distribution flow.

## Refactoring Rules

- Refactor only when it directly supports the requested task or removes a real problem.
- Keep refactors small and reviewable.
- Do not mix large refactors with feature changes unless necessary.
- Preserve behavior during refactoring.
- Preserve command IDs, view type IDs, setting keys, manifest ID, and public data shapes unless migration is explicit.
- Reuse existing project patterns before introducing new structure.
- Prefer extracting helpers over duplicating logic.
- Do not rewrite a mature plugin into a new architecture without explicit request.

## Agent Workflow

When making any code change, follow this workflow:

1. Understand the request and identify the affected plugin capability.
2. Read the relevant files end to end, including nearby settings, commands, views, services, styles, tests, and docs.
3. Search for existing patterns, helpers, constants, command IDs, view types, and settings keys.
4. Check `manifest.json`, `versions.json`, and `package.json` before changing compatibility, build, or release behavior.
5. Choose the smallest clean design that uses official Obsidian APIs.
6. Implement the change with safe lifecycle, file, UI, and settings behavior.
7. Update settings migrations if data shapes change.
8. Update styles using CSS classes and Obsidian variables when UI changes.
9. Update README, changelog, or release notes when user-visible behavior changes.
10. Run targeted validation commands that exist in `package.json`.
11. Verify imports, exports, lifecycle cleanup, manifest compatibility, settings persistence, and release artifacts.
12. Summarize what changed and how it was validated.

When there are meaningful trade-offs:

- Present the two best options.
- Explain the consequences for compatibility, review, mobile support, performance, and maintainability.
- Prefer the simpler official API-based option unless the user requests a more advanced design.

## Anti-Patterns

Avoid these patterns:

- Treating an Obsidian plugin like an Express back-end application.
- Using global `app` / `window.app`.
- Using `innerHTML` for user-controlled content.
- Adding default hotkeys without explicit intent.
- Accessing `workspace.activeLeaf` directly for editor logic.
- Storing custom view instances in plugin-level fields.
- Editing active notes through `Vault.modify()`.
- Editing YAML frontmatter manually as text.
- Scanning the full vault for exact path lookup.
- Hardcoding styles inline.
- Ignoring mobile compatibility while `isDesktopOnly` is `false`.
- Top-level importing desktop-only modules in cross-platform plugins.
- Using `fetch()` / Axios instead of `requestUrl()` without a reason.
- Storing secrets in `data.json`.
- Logging private note content, paths, headers, tokens, or secrets.
- Adding client-side telemetry to community plugins.
- Adding obfuscated code.
- Adding dynamic ads.
- Adding a custom plugin updater.
- Changing manifest `id` after publication.
- Changing command IDs or setting keys without migration.
- Manually editing generated `main.js` instead of source files.
- Manually editing lockfiles.
- Inventing package scripts.
- Creating broad formatting-only diffs.
- Rewriting unrelated code.
- Adding unnecessary dependencies or heavy UI frameworks.
- Doing heavy work in `onload()`.
- Leaving listeners, intervals, or DOM handlers active after unload.
