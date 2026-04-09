# Data Flow Diagrams

## System Architecture

```mermaid
graph TB
    subgraph "User"
        Terminal[Terminal / Shell]
    end

    subgraph "cordova-cli"
        Entry["bin/cordova"]
        CLI["src/cli.js<br/>(nopt parser + dispatcher)"]
        Help["src/help.js"]
        Info["src/info.js"]
        ConfigStore["configstore"]
    end

    subgraph "External Packages"
        CordovaLib["cordova-lib<br/>(core operations)"]
        CordovaCreate["cordova-create"]
        CordovaCommon["cordova-common<br/>(logging, errors)"]
    end

    subgraph "File System"
        DocFiles["doc/*.md/*.txt"]
        ProjectDir["Cordova Project<br/>(config.xml, platforms/, plugins/)"]
        ConfigJSON["~/.config/cordova-config.json"]
    end

    Terminal -->|"$ cordova <cmd>"| Entry
    Entry --> CLI
    CLI -->|"help"| Help
    CLI -->|"info"| Info
    CLI -->|"config"| ConfigStore
    CLI -->|"create"| CordovaCreate
    CLI -->|"build/run/plugin/platform/..."| CordovaLib
    Help -->|reads| DocFiles
    Info -->|inspects| ProjectDir
    ConfigStore -->|read/write| ConfigJSON
    CordovaLib -->|manages| ProjectDir
    CordovaCreate -->|scaffolds| ProjectDir
    CLI -->|logging| CordovaCommon
```

## Command Execution Flow

```mermaid
sequenceDiagram
    participant User
    participant bin/cordova
    participant src/cli.js
    participant cordova-lib

    User->>bin/cordova: $ cordova build android --release
    bin/cordova->>src/cli.js: cli(process.argv)
    src/cli.js->>src/cli.js: nopt parse args
    src/cli.js->>src/cli.js: Setup logger (verbose/silent)
    src/cli.js->>src/cli.js: Check Node.js version
    src/cli.js->>src/cli.js: Extract unparsed args (after --)
    src/cli.js->>cordova-lib: cordova.build({platforms: ['android'], options: {release: true}})
    cordova-lib-->>src/cli.js: Promise<result>
    src/cli.js-->>bin/cordova: Promise<result>
    bin/cordova-->>User: Exit code 0 (or error message)
```

## Config Command Flow

```mermaid
sequenceDiagram
    participant User
    participant src/cli.js
    participant configstore

    User->>src/cli.js: $ cordova config set autosave true
    src/cli.js->>src/cli.js: Detect "config" command
    src/cli.js->>src/cli.js: Detect subcommand "set"
    src/cli.js->>configstore: conf.set("autosave", "true")
    configstore-->>src/cli.js: Written to ~/.config/cordova-config.json
    src/cli.js-->>User: (silent success)
```

## Plugin/Platform Management Flow

```mermaid
sequenceDiagram
    participant User
    participant src/cli.js
    participant cordova-lib

    User->>src/cli.js: $ cordova plugin add cordova-plugin-camera --variable API_KEY=abc
    src/cli.js->>src/cli.js: Parse: cmd=plugin, subcommand=add, targets=[cordova-plugin-camera]
    src/cli.js->>src/cli.js: Parse --variable into cli_vars: {API_KEY: "abc"}
    src/cli.js->>src/cli.js: Build download_opts (save, searchpath, cli_variables, etc.)
    src/cli.js->>cordova-lib: cordova.plugin("add", ["cordova-plugin-camera"], download_opts)
    cordova-lib-->>src/cli.js: Promise<result>
    src/cli.js-->>User: Success/error output
```

## Info Command Flow

```mermaid
sequenceDiagram
    participant User
    participant src/cli.js
    participant src/info.js
    participant cordova-lib
    participant OS

    User->>src/cli.js: $ cordova info
    src/cli.js->>src/info.js: info()
    src/info.js->>src/info.js: getCordovaDependenciesInfo()
    src/info.js->>cordova-lib: getInstalledPlatformsWithVersions()
    src/info.js->>cordova-lib: getInstalledPlugins()
    src/info.js->>OS: osInfo(), npm -v
    src/info.js->>src/info.js: getPlatformEnvironmentData()
    src/info.js->>src/info.js: getProjectSettingsFiles()
    src/info.js->>src/info.js: _formatNodeList(results)
    src/info.js-->>User: Formatted environment/project info
```

## Feature Dataflows

Individual dataflow diagrams for each feature are in `.ai/specs/dataflows/`:

| Feature | Dataflow File | Status |
|---------|--------------|--------|
| Command Dispatch | [dataflows/command-dispatch.md](dataflows/command-dispatch.md) | Complete |
| Config Management | [dataflows/config-management.md](dataflows/config-management.md) | Complete |
| Plugin Management | [dataflows/plugin-management.md](dataflows/plugin-management.md) | Complete |
| Platform Operations | [dataflows/platform-operations.md](dataflows/platform-operations.md) | Complete |
| System Info | [dataflows/system-info.md](dataflows/system-info.md) | Complete |
