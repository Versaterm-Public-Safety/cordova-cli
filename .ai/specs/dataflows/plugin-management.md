# Plugin Management Dataflow

## Overview

The `cordova plugin` command manages native plugins in a Cordova project, supporting add/remove/ls with variables, hooks suppression, and save behavior.

## Flow

```mermaid
sequenceDiagram
    participant User
    participant cli.js
    participant cordova-lib

    User->>cli.js: $ cordova plugin add cordova-plugin-camera --variable API_KEY=abc --save-exact
    cli.js->>cli.js: Parse: cmd="plugin", subcommand="add"
    cli.js->>cli.js: targets = ["cordova-plugin-camera"]
    cli.js->>cli.js: Parse --variable: cli_vars = {API_KEY: "abc"}
    cli.js->>cli.js: args.save = !args.nosave (default true)
    cli.js->>cli.js: args.production = !args.noprod (default true)
    cli.js->>cli.js: Fallback searchpath from configstore
    cli.js->>cli.js: Fallback save-exact from configstore
    cli.js->>cli.js: Build download_opts: {searchpath, noregistry, nohooks, cli_variables, link, save, save_exact, shrinkwrap, force, production}
    cli.js->>cordova-lib: cordova.plugin("add", ["cordova-plugin-camera"], download_opts)
    cordova-lib-->>cli.js: Promise resolved
    cli.js-->>User: Success
```

## download_opts Schema

| Field | Source | Default |
|-------|--------|---------|
| searchpath | `--searchpath` flag or configstore | undefined |
| noregistry | `--noregistry` flag | false |
| nohooks | `--nohooks` flag (array) | [] |
| cli_variables | `--variable KEY=VAL` (parsed) | {} |
| link | `--link` flag | false |
| save | `!--nosave` | true |
| save_exact | `--save-exact` flag or configstore | false |
| shrinkwrap | `--shrinkwrap` flag | false |
| force | `--force` flag | false |
| production | `!--noprod` | true |

## Key Source

- `src/cli.js` lines 306-348 (plugin/platform entity command handling)
- Variable parsing: lines 309-322
