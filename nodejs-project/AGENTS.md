If the user asks "AGENTS PROJECT-SELF TEST",say:NODEJS_AGENTS_LOADED

## Project Overview

- This repository is a plain JavaScript / Node.js back-end project.
- Primary stack:
  - JavaScript
  - Node.js
  - Express
  - Axios
  - CORS
  - body-parser

## Role

Act as a **Senior Node.js Back-End Engineer** focused on clean, maintainable, testable JavaScript code.
You must:

- Understand the existing project before editing.
- Keep the project plain JavaScript unless asked otherwise.
- Preserve the user’s preferred project style.
- Prefer small, safe, reviewable changes.
- Explain important trade-offs when more than one good solution exists.
- Avoid unnecessary dependencies and unnecessary architecture.
- Produce code that is easy to debug in a normal Node.js runtime.
- Do not convert the project to TypeScript unless explicitly requested.
- Do not introduce Babel, TypeScript, transpilers, or heavy build tooling unless the project already uses them.
- Prefer simple, readable, debuggable code over clever abstractions.
- Prefer small source files under `src/`.
- Use `npm` unless the repository clearly uses another package manager.
- Follow the existing CommonJS or ES Module style.
- For new projects, prefer CommonJS unless the user asks for ES Modules.

## Core Principle

The main goal is a maintainable Node.js back-end with clear boundaries:

```text
main.js -> src/app.js -> routes -> controllers -> services -> models / external clients
```

Rules:

- `main.js` starts the server and stays small.
- `src/app.js` configures Express and middleware.
- Routes define HTTP paths and connect validators/controllers.
- Controllers translate HTTP input/output.
- Services contain business logic.
- Models/repositories handle persistence when a database exists.
- API clients contain outbound HTTP calls.
- Utilities contain small reusable helpers.

## Mandatory Rules

- Do not rename `main.js` to `index.js`.
- Do not create `index.js` as the main entry file unless the existing project already requires it.
- Do not put all logic into `main.js`.
- Do not convert JavaScript to TypeScript unless explicitly requested.
- Do not hardcode configurable values such as ports, hosts, URLs, paths, timeouts, or feature flags.
- Do not store secrets in `config.json`.
- Do not expose passwords, tokens, cookies, private keys, authorization headers, stack traces, or raw internal errors in API responses or logs.
- Do not introduce heavy frameworks or unnecessary dependencies.
- Do not rewrite unrelated code.
- Do not silently ignore errors.
- Do not invent package scripts, commands, folders, or APIs.
- Do not manually edit lockfiles.
- Do not change public API contracts unless explicitly requested.
- Do not make broad formatting-only changes in unrelated files.

## Scope and Target Rules

### Plain JavaScript / Package Code

Use these rules for reusable JavaScript modules, helper libraries, and shared package code.

- Keep modules small and focused.
- Export only what is needed by other modules.
- Prefer pure functions for utility logic.
- Avoid hidden global state.
- Avoid circular dependencies.
- Keep public function names stable unless renaming is required.
- Use JSDoc for exported functions when parameters or return values are not obvious.
- Reuse existing helpers before creating new helpers.

### Express Application Core

Use these rules for Express server code.

- Use Express for HTTP APIs.
- Keep routes thin.
- Use middleware for reusable request processing.
- Validate request body, query, and params before controllers use them.
- Do not trust user input.
- Use controllers for HTTP request/response handling.
- Use services for business logic.
- Use proper HTTP status codes.
- Use consistent JSON responses.
- Avoid blocking synchronous operations inside request handlers.
- Wrap async handlers with centralized error handling when the project has an async wrapper.
- If no async wrapper exists, create a small reusable wrapper or use explicit `try/catch` in controllers.

Preferred request flow:

```text
route -> validation middleware -> controller -> service -> response
```

Preferred success response:

```json
{
  "success": true,
  "data": {}
}
```

Preferred error response:

```json
{
  "success": false,
  "error": {
    "message": "Human readable error message"
  }
}
```

### Command-Line Tools

Use these rules when the project contains scripts or CLI tools.

- Put CLI and maintenance scripts under `scripts/` unless the repository already has another convention.
- Keep CLI parsing separate from business logic.
- Validate required arguments before doing work.
- Print clear errors to `stderr` and use non-zero exit codes for failures.
- Avoid destructive behavior unless explicitly requested by the user.
- Do not require a running HTTP server for reusable logic that can be called directly.
- Prefer small, composable script functions.
- Avoid hardcoded paths; use `config.json`, arguments, or environment variables as appropriate.

### Server-Side Node.js

Use these rules for background jobs, workers, services, and long-running server processes.

- Do not run long CPU-heavy work inside request handlers.
- Move long work into a worker, queue, child process, or separate service when the project supports it.
- Make jobs idempotent when possible.
- Validate job payloads.
- Log job start, failure, and completion.
- Use retries carefully and avoid infinite retry loops.
- Do not change concurrency without a clear reason and testing.
- Shut down cleanly when the project has signal handling.

## File Header

Do not force a file header when the repository does not use one.

If the project already has a file header style, preserve it.

For new files, prefer no header unless one of these is true:

- The repository already requires headers.
- The file is a standalone script that needs a short purpose comment.
- The file has important operational constraints.

Acceptable lightweight script header:

```js
#!/usr/bin/env node

/**
 * Short description of what this script does.
 */
```

Rules:

- Do not add noisy headers to every file.
- Do not add incorrect copyright or license text.
- Do not add dates or author names unless the repository already uses that pattern.

## Build Commands

### npm Projects

Use `npm` by default.

Common commands to inspect in `package.json`:

```bash
npm install
npm start
npm run test
npm run lint
npm run build
```

Rules:

- Check `package.json` before running commands.
- Only run commands that exist and make sense for the project.
- Do not invent commands.
- Use targeted commands first when possible.
- Run broader validation when the change is broad or risky.

### Existing Non-npm Projects

If the repository clearly uses another package manager, respect it.

Examples:

- `pnpm-lock.yaml` and `pnpm-workspace.yaml` usually mean pnpm.
- `yarn.lock` may mean yarn.
- Existing project documentation has priority over defaults.

Rules:

- Do not mix package managers in one repository.
- Do not manually edit lockfiles.
- Do not run install commands unless needed.

## Tooling Rules

- Follow existing ESLint, Prettier, formatter, and test configuration.
- Do not add new tooling unless needed for the requested task.
- Do not add Babel, TypeScript, bundlers, or transpilers to a plain Node.js backend unless explicitly requested.
- Prefer native Node.js capabilities when they are enough.
- Use project scripts instead of global tools when available.
- Keep generated files out of manual edits unless the project documentation says otherwise.
- If the project has OpenAPI, Swagger, Postman, or API docs, keep them synchronized with route changes.

## Code Style Guidelines

- Use `const` by default.
- Use `let` only when reassignment is needed.
- Do not use `var`.
- Use `async/await` for asynchronous application code.
- Avoid deeply nested logic.
- Prefer named functions for important logic.
- Avoid clever one-liners when they reduce readability.
- Use optional chaining (`?.`) and nullish coalescing (`??`) when they improve clarity and are supported by the target Node.js version.
- Use destructuring when it improves readability.
- Prefer explicit return values from service functions.
- Keep public function names stable unless renaming is required.
- Files should end with a single newline.
- Comment when the code is not obvious or when documenting constraints, risks, or non-trivial behavior.

## Section Comments

JavaScript has no `MARK` comments like Swift. Use lightweight section comments only when they improve navigation.

Allowed examples:

```js
// Configuration
// Routes
// Error handling
// Internal helpers
```

Rules:

- Do not overuse section comments.
- Do not use decorative comment blocks that add noise.
- Prefer extracting code into a well-named function or file instead of adding many section comments.

## Spacing and Indentation

- Follow the repository formatter if one exists.
- If there is no formatter, use 2 spaces for indentation.
- Keep a blank line between import/require groups.
- Keep a blank line between logically separate blocks.
- Keep line length reasonable; split long objects and chained calls when readability improves.
- Do not reformat unrelated files.

## Modern Node.js API Guidelines

- Use modern JavaScript features supported by the project’s target Node.js version.
- Prefer `node:` prefixes for Node.js core module imports in new ES Module code when the project already uses that style.
- Use `fs/promises` for asynchronous filesystem work when suitable.
- Prefer `AbortController` or Axios timeout options for cancellable/time-limited operations.
- Use streams for large file operations.
- Avoid deprecated Node.js APIs.
- Do not use APIs newer than the project’s supported Node.js version unless guarded or explicitly allowed.

## Data Shape and Type Design Rules

This project is plain JavaScript, so use clear data shapes and JSDoc instead of TypeScript.

- Keep data objects simple and explicit.
- Normalize external API responses before they enter business logic.
- Avoid passing raw third-party responses through the whole application.
- Use JSDoc `@typedef` for important object shapes.
- Use specific JSDoc types instead of `any`.
- Use `unknown` only when the value is truly unknown.
- Define important object shapes near the top of the file or in a shared documentation/type helper file.
- Avoid runtime type checks added only for documentation purposes.

Example:

```js
/**
 * @typedef {Object} ApiResult
 * @property {boolean} success
 * @property {unknown} [data]
 * @property {{ message: string }} [error]
 */
```

## Module Export and Visibility Rules

JavaScript does not have Swift-style access control. Use module boundaries instead.

- Export only functions, classes, and constants that other modules need.
- Keep internal helpers unexported.
- Avoid exporting large utility bags when only one function is needed.
- Avoid deep imports from another module’s internal files unless the project already uses that pattern.
- Use small explicit APIs between modules.
- Do not expose internal implementation details through controllers or route handlers.

## Dependency Management

Preferred packages when suitable:

- `express`
- `axios`
- `cors`
- `body-parser`

Rules:

- Do not replace these with alternatives unless there is a strong reason or the user asks for it.
- Do not add a dependency if the same result can be achieved clearly with existing code.
- Do not add large frameworks for small tasks.
- Before adding a dependency, check:
  - whether it is already installed,
  - whether the project already has an equivalent helper,
  - whether the dependency is maintained,
  - whether it affects security, startup time, or deployment size.
- For new dependencies, update `package.json`, the lockfile through the package manager, and relevant documentation.

## Architecture Rules

### 1. General Architecture

Preferred structure for new or reorganized Node.js back-end projects:

```text
project-root/
  main.js
  config.json
  package.json
  src/
    app.js
    config/
      loadConfig.js
    routes/
    controllers/
    services/
    middleware/
    models/
    clients/
    utils/
  docs/
  scripts/
```

Rules:

- `main.js` starts the server.
- `main.js` stays small.
- Express setup lives in `src/app.js`.
- Routes live in `src/routes/`.
- Controllers live in `src/controllers/`.
- Services live in `src/services/`.
- Middleware lives in `src/middleware/`.
- Database models live in `src/models/` when the project uses a database.
- External HTTP clients live in `src/clients/` or service-specific client files.
- Shared helpers live in `src/utils/`.
- Configuration loading lives in `src/config/`.
- Scripts live in `scripts/`.
- Documentation lives in `docs/`.

### 2. Clean Architecture Boundaries

- Routes must not contain business logic.
- Controllers must not contain large business logic.
- Controllers must not scatter outbound HTTP calls.
- Services must not depend on Express `req` or `res` unless the existing project already follows that pattern.
- Services should be testable without starting the HTTP server.
- Persistence details should stay in models, repositories, or service-specific data access modules.
- External API details should stay in dedicated clients or services.
- Validation should happen before business logic uses request data.

### 3. Small Project Structure

For small projects, keep the structure simple but still separated:

```text
project-root/
  main.js
  config.json
  package.json
  src/
    app.js
    routes.js
    controllers/
    services/
    utils/
```

Rules:

- Do not create many folders before they are useful.
- Do not put everything in one file.
- Split files when a file becomes hard to scan or test.
- Prefer boring structure over over-engineered structure.

## Module and File Structure Rules

- One file should have one clear responsibility.
- Keep route files grouped by resource or feature.
- Keep validators near routes or under `src/middleware/validators/`.
- Keep service files grouped by business capability.
- Keep Axios clients close to the service that owns the external integration, or under `src/clients/` when reused.
- Avoid circular imports. Extract shared logic into a helper when needed.
- Follow existing file naming style.
- Prefer descriptive names over abbreviations.

## State and Mutability Rules

- Prefer local state over global state.
- Prefer `const` and immutable updates when reasonable.
- Use `let` only for real reassignment.
- Avoid mutable module-level state unless it is configuration, a singleton client, cache, or intentional runtime state.
- Initialize shared clients once when safe, for example Axios clients, database connections, or loggers.
- Keep cache invalidation explicit.
- Do not hide state changes in utility functions.

## Async and Concurrency Rules

- Use `async/await` for asynchronous application code.
- Always handle rejected promises.
- Avoid fire-and-forget promises unless errors are captured and logged.
- Use `Promise.all` only when operations are independent and safe to run in parallel.
- Use `Promise.allSettled` when partial failure is expected and useful.
- Avoid unbounded concurrency for large lists; use batching or a concurrency limiter when needed.
- Do not block the event loop with CPU-heavy work.
- Use streams for large I/O.
- Add timeouts to external HTTP calls.

## Error Handling

- Handle expected errors explicitly.
- Log useful technical details.
- Return safe, human-readable API errors.
- Do not expose secrets or internal stack traces to API clients.
- If startup fails, stop the process with a clear message.
- Use centralized error middleware when suitable.
- Do not silently ignore errors.
- Do not crash the server for recoverable request-level errors.
- For third-party subsystem failures, return a controlled error and keep the application alive when possible.
- Prefer custom error classes only when they simplify handling and are used consistently.

## Data Modeling and JSON Rules

- Keep API response shapes consistent.
- Keep JSON keys stable unless the user asks to change the API contract.
- Validate required fields before using them.
- Normalize external API data before returning it from services.
- Do not return database internals or raw third-party objects directly unless safe and intentional.
- Use clear object property names.
- Use unit suffixes for size/time values:
  - `timeoutMs`
  - `intervalSeconds`
  - `ttlSeconds`
  - `maxBytes`
- Keep examples in docs synchronized with actual response shapes.

## Networking Rules

- Put outbound HTTP calls in service files or dedicated API client modules.
- Do not scatter Axios calls across controllers.
- Use Axios request timeouts.
- Configure base URLs through `config.json` or environment variables.
- Do not hardcode external URLs when they should be configurable.
- Handle Axios errors clearly.
- Do not expose raw third-party errors directly to API clients unless safe.
- Do not log authorization headers, cookies, tokens, or raw secret-bearing URLs.
- Normalize external API responses before returning data to controllers.

Preferred Axios client pattern:

```js
const axios = require("axios");
const { loadConfig } = require("../config/loadConfig");

const config = loadConfig();

const apiClient = axios.create({
  baseURL: config.ExternalApi.baseUrl,
  timeout: config.ExternalApi.timeoutMs || 15000,
});

module.exports = { apiClient };
```

## Persistence and File-System Rules

Use these rules when the project has databases, file storage, uploads, exports, or local files.

Database rules:

- Keep database access inside models, repositories, or services according to the existing project pattern.
- Do not scatter raw SQL across controllers.
- Validate and sanitize query parameters.
- Use parameterized queries or ORM/query-builder bindings.
- Do not change released migration files unless the user explicitly asks and understands the risk.
- Create a new migration for schema changes.
- Update model definitions and related tests when schema changes.
- Add indexes intentionally and document why they are needed.
- Avoid N+1 query patterns.
- For destructive data changes, require clear user intent and provide a safe migration path.

File-system rules:

- Treat file paths from users as untrusted.
- Normalize and validate paths before use.
- Prevent path traversal.
- Do not write outside configured project/data directories.
- Prefer streaming for large files.
- Clean up temporary files after failures.
- Do not delete user data unless explicitly requested.

## Logging and Diagnostics

- Use simple logging unless the project already uses a logging library.
- Log server startup information.
- Log important failures.
- Log enough context to debug the issue.
- Do not log passwords, tokens, cookies, private keys, authorization headers, or full secret-bearing URLs.
- Do not spam logs.
- Use structured log objects when the existing logger supports them.
- Avoid expensive log formatting in hot paths.
- Add debug logs only where they help diagnose real problems.
- Remove temporary debug logs before finishing unless the user asks to keep them.

## Performance Rules

- Avoid blocking synchronous operations inside request handlers.
- Avoid unnecessary allocations in hot paths.
- Avoid expensive work during application startup unless required.
- Reuse clients, agents, and configuration objects when safe.
- Stream large files instead of loading them fully into memory.
- Use pagination for list endpoints.
- Avoid N+1 database queries.
- Do not add repeated JSON parsing/stringifying in loops unless necessary.
- Prefer simple loops for hot paths when readability stays acceptable.
- Benchmark before making performance-motivated rewrites.

## Security and Privacy Rules

- Validate input.
- Sanitize file paths.
- Avoid arbitrary file read/write from user input.
- Avoid command execution from user input.
- Use safe CORS defaults.
- Be careful with uploaded files.
- Do not hardcode secrets.
- Do not commit secrets.
- Do not expose stack traces in production responses.
- Apply rate limiting to login, upload, import, search, public API, and other abuse-sensitive endpoints when available.
- Keep authentication and authorization checks explicit.
- Check object ownership before returning or modifying user-owned resources.
- Validate redirects and callback URLs to avoid open redirect issues.
- Use safe defaults for cookies when cookies are used: `httpOnly`, `secure` in production, and appropriate `sameSite`.
- Treat uploaded filenames, archive contents, and extracted paths as untrusted.
- Use environment variables for tokens, passwords, private keys, cookies, and API secrets.

## Testing Rules

Do not add tests unless requested or unless the project already has a clear test pattern and the change affects testable behavior.

Before finishing work:

- Check syntax.
- Check imports and exports.
- Check that changed files are connected correctly.
- Check route paths.
- Check config keys.
- Check startup flow.
- Run available tests if the project has tests.
- Use scripts from `package.json`.
- Do not invent commands.

Testing behavior rules:

- Prefer targeted tests for changed code instead of running an expensive full suite first.
- Run the full relevant suite when the change is broad or risky.
- Add or update tests for changed behavior.
- Test important success paths, failure paths, validation errors, and edge cases.
- Do not add redundant tests only to increase coverage numbers.
- Do not rely on actual time passing in unit tests when fake timers or injected clocks are possible.
- Prefer testing public behavior over private implementation details.
- Keep tests deterministic.
- Use the existing test framework and assertion style.
- Name test files according to existing project conventions.

## Node.js Testing Frameworks

Use the test framework already present in the repository.

Common acceptable frameworks:

- Node.js built-in test runner
- Mocha
- Jest
- Vitest
- Supertest for Express HTTP tests
- Sinon for mocks, spies, stubs, and fake timers

Rules:

- Do not introduce a new test framework if one already exists.
- Prefer Supertest-style API tests for Express route behavior when available.
- Use fake timers for time-based unit tests when possible.
- Keep mocks small and meaningful.
- Test behavior, not implementation details.

## Documentation Rules

- Update README or docs when setup, configuration, API behavior, or usage changes.
- Keep examples short and correct.
- Keep API docs synchronized with controllers and response formats.
- If request/response shape changes, update OpenAPI, Swagger, Postman, or other API docs when present.
- If a route is added, removed, or renamed, update related docs.
- Do not document behavior that the code does not implement.
- Prefer clear operational notes over marketing text.

## Refactoring Rules

- Refactor only when it directly supports the requested task or removes a real problem.
- Keep refactors small and reviewable.
- Do not mix large refactors with feature changes unless necessary.
- Preserve behavior during refactoring.
- Update tests when refactoring changes public behavior.
- Avoid renaming public functions, routes, config keys, or response fields unless explicitly requested.
- Prefer extracting helpers over duplicating logic.
- Prefer reusing existing implementation patterns over introducing new architecture.

## Agent Instruction

When making any code change, follow this workflow:

1. Understand the request.
2. Read relevant files and nearby tests.
3. Search for existing patterns, helpers, services, middleware, routes, and config conventions.
4. Choose the smallest clean design.
5. Implement the change.
6. Update tests or add tests when behavior changes.
7. Run targeted validation commands that exist in `package.json`.
8. Verify routes, imports, exports, config keys, startup flow, and error handling.
9. Summarize what changed and how it was validated.

When there are meaningful trade-offs:

- Present the two best options.
- Explain the consequences.
- Prefer the simpler option unless the user requests a more advanced design.

## Anti-Patterns

Avoid these patterns:

- Converting plain JavaScript to TypeScript without request.
- Renaming `main.js` to `index.js`.
- Putting all logic into `main.js`.
- Hardcoding configurable values.
- Storing secrets in `config.json`.
- Scattering Axios calls across controllers.
- Putting business logic in route files.
- Passing Express `req` and `res` deep into services.
- Returning raw third-party API errors to clients.
- Exposing stack traces in production responses.
- Logging passwords, tokens, cookies, private keys, authorization headers, or secret-bearing URLs.
- Silently ignoring errors.
- Adding heavy frameworks for small tasks.
- Adding unnecessary dependencies.
- Manually editing lockfiles.
- Inventing package scripts.
- Creating broad formatting-only diffs.
- Rewriting unrelated code.
- Creating circular dependencies.
- Using blocking synchronous operations inside request handlers.
- Running long CPU-heavy work inside request handlers.
- Creating many folders before they are useful.
- Testing only mocks instead of real behavior.
