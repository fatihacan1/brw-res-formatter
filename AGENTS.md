# AGENTS.md — brw-res-formatter

## Project overview
TypeScript library. Standardizes Express.js API responses into one shape.
Peer dependency: `express >= 4.0.0`. No runtime dependencies bundled.
Dual build: CJS + ESM (see `tsconfig.cjs.json` / `tsconfig.esm.json`).

## Build & test commands
- Install: `npm install`
- Test: `npm test` (vitest, see `vitest.config.ts`)
- Build: `npm run build`
- Run all tests before opening a PR. Do not skip failing tests to "fix later".

## Non-negotiable conventions
- **Never** have route handlers call `res.json()` or `res.send()` directly.
  Always go through this package:
  - `sendSuccessResponse(res, statusCode?, message?, data?)` for 2xx
  - `sendErrorResponse(res, statusCode?, message?)` for 4xx/5xx
  - `sendResponse(res, statusCode, success, message, data?)` only when you
    need full manual control (rare — prefer the two wrappers above).
- Every response body must match `ApiResponse<T>`:
  `{ success: boolean; status: number; message: string; data: T | null }`.
  Do not add extra top-level fields (e.g. no `error` key, no `meta` key)
  without updating the `ApiResponse` type first.
- Defaults: `sendErrorResponse` → `500` / `"Server error"`.
  `sendSuccessResponse` → `200` / `"Operation successful"` / `data: null`.
  Don't hardcode these strings elsewhere — rely on the defaults.
- `message` must be human-readable and specific (e.g.
  `"Validation failed: email is required"`), not generic filler like `"Error"`.
- Global error-handling middleware (4-arg Express middleware) must call
  `sendErrorResponse`, using `err.statusCode` when present, falling back to `500`.
  In production, never leak `err.message` to the client — see README's
  "Global Error Handler" example for the exact pattern.
- Types: import `ApiResponse` / `ResponseOptions` with `import type { ... }`,
  never as a value import.

## Boundaries
- Don't add new runtime dependencies. This package is intentionally
  zero-dependency (Express is peer-only).
- Don't break CJS or ESM builds — changes must work under both
  `tsconfig.cjs.json` and `tsconfig.esm.json`.
- Don't change the public response shape (`ApiResponse<T>`) without also
  updating README examples and bumping semver appropriately.

## PR checklist
1. `npm test` passes (20+ tests currently cover all three functions).
2. `npm run build` succeeds for both module targets.
3. README updated if the public API changed.
