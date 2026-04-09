# Architecture

## System Overview

Apache Cordova CLI is a **thin CLI wrapper** around `cordova-lib`. It provides command-line argument parsing and user interaction, then delegates all heavy lifting (platform management, plugin management, builds, etc.) to the underlying library.

```
┌─────────────────────────────────────────────────┐
│  User (Terminal)                                │
│    $ cordova build android --release            │
└──────────────────┬──────────────────────────────┘
                   │
┌──────────────────▼──────────────────────────────┐
│  bin/cordova (entry point)                      │
│  - Invokes src/cli.js                           │
│  - Catches unhandled promise rejections         │
└──────────────────┬──────────────────────────────┘
                   │
┌──────────────────▼──────────────────────────────┐
│  src/cli.js (command dispatcher)                │
│  - Parses args with nopt                        │
│  - Routes to local handlers or cordova-lib      │
│  - Manages logging verbosity                    │
└──────────────────┬──────────────────────────────┘
                   │
        ┌──────────┼──────────┐
        ▼          ▼          ▼
  ┌──────────┐ ┌────────┐ ┌────────────────────┐
  │ Local    │ │ Local  │ │ cordova-lib        │
  │ handlers │ │ config │ │ (external package) │
  │ help,    │ │ store  │ │ build, run, plugin,│
  │ info,    │ │ (CRUD) │ │ platform, prepare, │
  │ create   │ │        │ │ compile, emulate,  │
  │          │ │        │ │ clean, serve,      │
  │          │ │        │ │ requirements       │
  └──────────┘ └────────┘ └────────────────────┘
```

## CLI Command Routing

- **Framework**: Node.js CLI with nopt for argument parsing
- **Routing**: Command-based dispatch in `src/cli.js` — the first non-flag argument determines the command
- **Authentication**: N/A (local CLI tool)
- **Authorization**: N/A
- **Data access pattern**: Delegates to cordova-lib which manages the Cordova project on disk

### Command Categories

| Category | Commands | Handler |
|----------|----------|---------|
| Local | `help`, `version` | `src/help.js`, inline |
| Local | `info` | `src/info.js` |
| Local | `config` (get/set/delete/edit/ls) | configstore |
| Local | `create` | cordova-create package |
| Platform ops | `build`, `run`, `emulate`, `prepare`, `compile`, `clean` | `cordova-lib` |
| Entity mgmt | `platform` (add/remove/ls) | `cordova-lib` |
| Entity mgmt | `plugin` (add/remove/ls) | `cordova-lib` |
| Diagnostics | `requirements` | `cordova-lib` |
| Server | `serve` | `cordova-lib` |

## Data Model (Core Entities)

| Entity | Purpose | Key Fields |
|--------|---------|------------|
| CLI Options | Parsed command-line arguments | verbose, silent, version, help, debug, release, target, device, emulator |
| Platform Opts | Options passed to platform commands | platforms[], options{}, nohooks[], searchpath |
| Download Opts | Options for plugin/platform add | searchpath, noregistry, cli_variables, link, save, save_exact, shrinkwrap, force, production |
| Config Store | Persistent user configuration | Arbitrary key-value pairs via configstore |

## Frontend Patterns

N/A — This is a CLI tool with no frontend/UI.

## Infrastructure

- **Hosting**: npm registry (published as `cordova` package)
- **CI/CD**: GitHub Actions — Node CI workflow with matrix testing
- **Database**: N/A (file-system based Cordova projects)
- **Containerization**: None

## Key Architecture Decisions

- **Decision**: Thin CLI wrapper over cordova-lib
  - **Rationale**: Separates CLI concerns (arg parsing, help text, logging) from core logic, allowing cordova-lib to be used programmatically without the CLI
  - **Trade-off**: Two packages to maintain, but enables library consumers and avoids duplicate event systems

- **Decision**: Re-export cordova-lib from cordova.js
  - **Rationale**: Prevents downstream consumers from accidentally installing a second copy of cordova-lib with separate event instances
  - **Trade-off**: Tight coupling between CLI and lib versions

- **Decision**: nopt for argument parsing
  - **Rationale**: Simple, proven option parser; supports double-dash pass-through for platform-specific args
  - **Trade-off**: Less ergonomic than modern alternatives (yargs, commander) but sufficient for the command set
