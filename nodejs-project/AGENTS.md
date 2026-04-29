## Purpose

This repository is a plain JavaScript / NodeJS back-end project.

Codex must help develop, refactor, debug, and review this project according to the rules below.

Primary stack:

- JavaScript
- NodeJS
- Express
- Axios
- CORS
- body-parser
- config.json
- main.js as the main entry file

## General Rules

- Use plain JavaScript.
- Do not convert the project to TypeScript unless explicitly requested.
- Do not introduce Babel, TypeScript, or heavy build tooling unless the project already uses it.
- Prefer simple, readable, debuggable code.
- Prefer small source files under `src/`.
- Do not put all logic into one file.
- Prefer `main.js` as the application entry point.
- Do not rename `main.js` to `index.js`.
- Do not create `index.js` as the main entry file unless the existing project already requires it.
- Keep changes focused on the requested task.
- Before editing code, read the relevant files and understand the current execution flow.
- Preserve existing API behavior unless the user asks to change it.
- Do not rewrite unrelated code.
- Do not introduce unnecessary dependencies.
- Use `npm` unless the repository clearly uses another package manager.
- Follow the existing CommonJS or ES Module style.
- For new projects, prefer CommonJS unless the user asks for ES Modules.

## Preferred JavaScript Style

- Use `const` by default.
- Use `let` only when reassignment is needed.
- Do not use `var`.
- Use `async/await` for asynchronous code.
- Avoid deeply nested logic.
- Prefer named functions for important logic.
- Avoid clever one-liners when they reduce readability.
- Handle errors explicitly.
- Avoid empty `catch` blocks.
- Avoid global mutable state unless necessary.
- Avoid circular dependencies.
- Keep public function names stable unless renaming is required.

## Preferred Project Structure

Use this structure when creating or reorganizing a NodeJS back-end project:

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
    utils/
  docs/
  scripts/
```
Rules:

- main.js starts the server
- main.js should stay small
- Express app setup should live in src/app.js
- Routes should live in src/routes/
- Controllers should live in src/controllers/
- Business logic should live in src/services/
- Middleware should live in src/middleware/
- Shared helpers should live in src/utils/
- Configuration loading should live in src/config/

## Preferred Packages

Prefer these packages when suitable:
- express
- axios
- cors
- body-parser
Do not replace them with alternatives unless there is a strong reason or the user asks for it.

## Configuration Rules

Use config.json for configurable values.

Examples:

server port
host
external API URLs
API timeout
feature flags
local paths

Example:
```json
{
  "CONNECTION_PORT": "3888",
  "Teamcenter": {
    "TC_URL": "http://ff22tc133",
    "TC_PORT": "7001",
    "TC_WEBTIER_NAME": "tc"
  }
}
```
Rules:

- Do not hardcode server port.
- Do not hardcode external URLs when they should be configurable.
- Validate important config values at startup.
- Fail early with a clear error if required config is missing.
- Do not store secrets in config.json.
- Use environment variables for tokens, passwords, private keys, cookies, and API secrets.

## Express Rules
- Use Express for HTTP APIs.
- Keep routes thin.
- Use controllers for request/response handling.
- Use services for business logic.
- Use middleware for reusable request processing.
- Use proper HTTP status codes.
- Validate request body, query, and params.
- Do not trust user input.
- Avoid blocking synchronous operations inside request handlers.
- Use consistent JSON responses.

Preferred success response:
```json
{
  success: true,
  data: {}
}
```
Preferred error response:
```json
{
  success: false,
  error: {
    message: "Human readable error message"
  }
}
```

## Axios Rules
- Put outbound HTTP calls in service files.
- Do not scatter axios calls across controllers.
- Use request timeouts.
- Handle axios errors clearly.
- Do not expose raw third-party errors directly to API clients unless safe.
- Return clean internal data from service functions.

## Error Handling
- Handle expected errors explicitly.
- Log useful technical details.
- Do not expose secrets or internal stack traces to API clients.
- If startup fails, stop the process with a clear message.
- Use centralized error middleware when suitable.

## Logging
- Use simple logging unless the project already uses a logging library.
- Log server startup information.
- Log important failures.
- Do not log passwords, tokens, cookies, private keys, or authorization headers.
- Do not spam logs.

## Security Rules
- Validate input.
- Sanitize file paths.
- Avoid arbitrary file read/write from user input.
- Avoid command execution from user input.
- Use safe CORS defaults.
- Be careful with uploaded files.
- Do not hardcode secrets.

## Testing and Validation
- Before finishing work:
- Check syntax.
- Check imports and exports.
- Check that changed files are connected correctly.
- Check route paths.
- Check config keys.
- Run available tests if the project has tests.
- Use scripts from package.json.
- Do not invent commands.

Useful commands to inspect:
```bash
npm install
npm start
npm run test
npm run lint
npm run build
```
Only run commands that exist and make sense for the project.

## Do Not Do
- Do not convert JavaScript to TypeScript.
- Do not rename main.js to index.js.
- Do not put all code into main.js.
- Do not hardcode configurable values.
- Do not add heavy frameworks.
- Do not add unnecessary dependencies.
- Do not rewrite unrelated code.
- Do not silently ignore errors.
