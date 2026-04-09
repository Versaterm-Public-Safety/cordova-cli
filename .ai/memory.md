# Apache Cordova CLI

> Command-line interface tool for building, deploying, and managing Apache Cordova hybrid mobile applications.

## Tech Stack

**CLI** (`src/`, `bin/`):
- **Language**: JavaScript (Node.js, CommonJS modules)
- **Runtime**: Node.js >= 18.0.0
- **CLI argument parsing**: nopt
- **Configuration storage**: configstore
- **Process execution**: execa
- **System info**: systeminformation
- **Semantic versioning**: semver
- **Core library**: cordova-lib (delegates all platform/plugin operations)
- **Project scaffolding**: cordova-create
- **Logging**: cordova-common CordovaLogger

**Testing** (`spec/`):
- **Framework**: Jasmine
- **Coverage**: nyc (Istanbul)
- **Mocking**: rewire (for module-internal variable replacement)

**Infrastructure**:
- **CI/CD**: GitHub Actions (Node CI) — matrix: Node 18.x/20.x/22.x on ubuntu/windows/macos
- **Security scanning**: GitHub CodeQL (javascript, security-and-quality)
- **Coverage reporting**: Codecov
- **Linting**: ESLint with `@cordova/eslint-config/node`
- **License**: Apache License 2.0 (Apache Software Foundation)
- **Package registry**: npm (published as `cordova`)

## Commands

```bash
# Run all tests (lint + coverage)
npm test

# Run linting only
npm run lint

# Run tests with coverage
npm run cover

# Clean install and test (CI)
npm cit
```

## Directory Structure

```
cordova-cli/
├── bin/
│   ├── cordova              # Unix entry point (#!/usr/bin/env node)
│   └── cordova.cmd          # Windows entry point
├── src/
│   ├── cli.js               # Main CLI logic: arg parsing, command dispatch
│   ├── help.js              # Help text loader (reads doc/ files)
│   └── info.js              # System/project info collector
├── cordova.js               # Public API re-export of cordova-lib
├── spec/
│   ├── cli.spec.js          # CLI unit tests
│   ├── help.spec.js         # Help unit tests
│   └── support/jasmine.json # Jasmine configuration
├── doc/                     # Help text files (.md/.txt) per command
├── scripts/                 # Bash completion, git hooks
├── .github/workflows/       # CI configuration
└── package.json
```

## Architecture Patterns

- **Thin CLI wrapper**: cordova-cli is a thin argument-parsing layer over `cordova-lib`
- **Command dispatch**: `src/cli.js` parses args with `nopt`, then delegates to `cordova-lib` methods (`cordova.build`, `cordova.plugin`, `cordova.platform`, etc.)
- **Special commands handled locally**: `help` (reads doc files), `info` (system introspection), `config` (configstore CRUD), `create` (cordova-create), `version`
- **Platform commands**: `build`, `run`, `emulate`, `prepare`, `compile`, `clean` — all forwarded to `cordova-lib` with platform names and options
- **Entity commands**: `platform`, `plugin` — subcommand pattern (`add`, `remove`, `ls`)
- **Event-based logging**: Uses `cordova-lib` events system with CordovaLogger subscriber

## Conventions

- **Commits**: Conventional commits (`feat:`, `fix:`, `chore:`)
- **Plans**: Save to `.ai/plans/YYYY-MM-DD-<feature-name>.md`
- **Code style**: ESLint with `@cordova/eslint-config/node`, single quotes, CommonJS require
- **Testing**: Jasmine specs in `spec/`, rewire for internal mocking, spies on cordova-lib methods
- **Licensing**: All source files require Apache 2.0 license header
