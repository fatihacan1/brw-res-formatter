# Changelog

All notable changes to this project will be documented in this file.

The format is based on [Keep a Changelog](https://keepachangelog.com/en/1.1.0/),
and this project adheres to [Semantic Versioning](https://semver.org/spec/v2.0.0.html).

## [1.0.3] - 2026-07-06

### Fixed
- Included `AGENTS.md` in the published npm package.

## [1.0.2] - 2026-07-06

### Added
- Created workspace agent customization rules in `AGENTS.md` at the repository root for AI assistant standard configurations

## [1.0.1] - 2026-04-21

### Breaking Changes
- Response body now includes `status` field alongside `success`, `message`, and `data`
- `express` moved from `dependencies` to `peerDependencies` — your project must have Express installed
- Package entry point changed: import directly from `brw-res-formatter` (no more deep imports)

### Added
- Dual ESM/CJS module support via `exports` map
- Proper TypeScript generics for type-safe data payloads
- `ApiResponse<T>` and `ResponseOptions<T>` type exports
- Full test suite with Vitest (17 tests)
- Source maps and declaration maps
- `CHANGELOG.md` and `LICENSE` files
- `engines` field requiring Node.js >= 16
- `prepublishOnly` script to ensure build + test before publish

### Fixed
- `data || null` bug that converted falsy values (0, "", false) to null — now uses strict undefined check
- Duplicate `ResponseData` interface (was defined in both `file-formatter.ts` and `types/index.ts`)

### Removed
- Hand-written `src/file-formatter.js` (replaced by TypeScript build output)
- `express` from `dependencies` (now a peer dependency)

## [1.0.0] - Initial Release

### Added
- `sendResponse` function
- `sendErrorResponse` function  
- `sendSuccessResponse` function
- Basic TypeScript types
