# System Info Dataflow

## Overview

`cordova info` collects and displays comprehensive environment and project diagnostics via `src/info.js`.

## Flow

```mermaid
sequenceDiagram
    participant User
    participant cli.js
    participant info.js
    participant cordova-lib
    participant systeminformation
    participant npm

    User->>cli.js: $ cordova info
    cli.js->>info.js: info()
    info.js->>info.js: cdProjectRoot() → projectRoot

    par Parallel data collection
        info.js->>info.js: getCordovaDependenciesInfo()
        Note over info.js: Reads cli + cordova-lib package versions<br/>and their cordova-* dependency versions
        info.js->>cordova-lib: getInstalledPlatformsWithVersions(projectRoot)
        info.js->>cordova-lib: getInstalledPlugins(projectRoot)
        info.js->>systeminformation: osInfo()
        info.js->>npm: execa('npm', ['-v'])
        info.js->>info.js: getPlatformEnvironmentData(projectRoot)
        Note over info.js: Per-platform: PlatformApi.getEnvironmentInfo()<br/>or legacy (xcodebuild/avdmanager)
        info.js->>info.js: getProjectSettingsFiles(projectRoot)
        Note over info.js: Reads config.xml + package.json cordova snippet
    end

    info.js->>info.js: _formatNodeList(results) → indented tree output
    info.js->>User: Formatted multi-section report
```

## Output Sections

1. **Cordova Packages** — cli version → lib version → cordova-* dependency versions
2. **Project Installed Platforms** — platform name → version
3. **Project Installed Plugins** — plugin id → version
4. **Environment** — OS, Node.js, npm versions
5. **{Platform} Environment** — Platform-specific toolchain info (xcodebuild, android SDK)
6. **Project Setting Files** — config.xml contents, package.json cordova snippet

## Key Source

- `src/info.js` (full file, 225 lines)
- Data collectors: lines 34-132
- Formatter: `_formatNodeList` lines 183-208
- Orchestrator: lines 210-224
