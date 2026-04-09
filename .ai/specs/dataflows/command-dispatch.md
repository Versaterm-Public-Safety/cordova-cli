# Command Dispatch Dataflow

## Overview

The central routing logic in `src/cli.js` that parses CLI arguments and dispatches to the appropriate handler.

## Flow

```mermaid
flowchart TD
    A["User: $ cordova <args>"] --> B["bin/cordova: cli(process.argv)"]
    B --> C["nopt parses knownOpts + shortHands"]
    C --> D{Special pre-parse?}
    D -->|"--version / -v"| E["cmd = 'version'"]
    D -->|"no cmd / --help"| F["cmd = 'help'"]
    D -->|"config"| G["Handle config subcommands inline"]
    D -->|other| H["Continue to cli()"]

    H --> I["Setup logger level<br/>(silent > verbose > default)"]
    I --> J["Check/warn Node.js version"]
    J --> K["Extract unparsed args after --"]
    K --> L["Determine cmd from remain[0]"]

    L --> M{cmd?}
    M -->|"help / undefined"| N["printHelp(remain)"]
    M -->|"info"| O["info()"]
    M -->|"config"| P["return true (already handled)"]
    M -->|"create"| Q["cordovaCreate(dest, {id, name, template})"]
    M -->|"build/run/emulate/prepare/compile/clean"| R["cordova[cmd](opts)<br/>opts.platforms = undashed[1:]<br/>opts.options = args + unparsed argv"]
    M -->|"requirements"| S["cordova.requirements(platforms)<br/>→ format + display results"]
    M -->|"serve"| T["cordova.serve(port)"]
    M -->|"platform/plugin"| U["cordova[cmd](subcommand, targets, download_opts)<br/>Parse --variable, build download_opts"]
    M -->|unknown| V["throw CordovaError"]
```

## Key Source

- **Entry**: `bin/cordova` (lines 26-40)
- **Dispatcher**: `src/cli.js` (lines 77-350)
- **Arg parsing**: nopt with `knownOpts` (lines 40-68) and `shortHands` (lines 70-75)
