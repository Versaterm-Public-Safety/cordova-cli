# Project Status

## Working Features

- Project creation (`cordova create`) with template support
- Platform management (`cordova platform add/remove/ls`)
- Plugin management (`cordova plugin add/remove/ls`) with `--variable`, `--save-exact`, `--noprod`
- Build/run/emulate/prepare/compile/clean commands with platform pass-through args
- Requirements checking per platform
- Config store (get/set/delete/edit/ls) via configstore
- Help system (per-command doc files)
- System info collection (`cordova info`)
- Version display with prerelease warnings
- Node.js version deprecation warnings
- Verbose/silent logging modes
- Bash completion script

## Known Issues

- Node deprecation range check uses hardcoded `'<10'` which is below the `>=18` engine requirement — effectively dead code
- `NODE_VERSION_REQUIREMENT` is set to `false`, disabling the unsupported-version warning entirely
- Config subcommands are handled before nopt parsing (pre-dispatch), duplicating some logic

## In Progress

- AI-native development structure setup (`.ai/`, `.claude/`, `CLAUDE.md`)

## Recently Completed

- 2026-04-08: AI-native development structure initialized and specs populated
