# Config Management Dataflow

## Overview

The `cordova config` command provides CRUD operations on persistent user configuration stored via the `configstore` package.

## Flow

```mermaid
flowchart TD
    A["$ cordova config <subcommand> [key] [value]"] --> B{subcommand?}

    B -->|get| C["conf.get(key)"]
    C --> C1{value defined?}
    C1 -->|yes| C2["emit log: value.toString()"]
    C1 -->|no| C3["emit log: 'undefined'"]

    B -->|set| D{value provided?}
    D -->|yes| D1["conf.set(key, value)"]
    D -->|no| D2["conf.set(key, true)"]

    B -->|delete| E["conf.delete(key)"]

    B -->|edit| F["editor(conf.path, callback)"]
    F --> F1["Opens config JSON in system editor"]

    B -->|ls/list| G["logger.results(JSON.stringify(conf.all, null, 4))"]
```

## Storage

- **Package**: `configstore` (stores as `cordova-config`)
- **Location**: `~/.config/configstore/cordova-config.json`
- **Affects**: `searchpath` and `save-exact` are read from config as defaults when not passed via CLI flags

## Key Source

- `src/cli.js` lines 91-131 (config subcommand handling)
- `src/cli.js` lines 327-334 (config values used as defaults in plugin/platform ops)
