# Apache Cordova CLI

> **Primary context:** Read `.ai/memory.md` for full project context, architecture, and conventions.

## Quick Reference

- **Run all tests:** `npm test` (lint + coverage)
- **Lint only:** `npm run lint`
- **Tests with coverage:** `npm run cover`
- **CI clean install + test:** `npm cit`

## AI Agent Structure

```
.ai/
├── memory.md              # Read FIRST — full project context
├── agents/                # Role-specific agent instructions
│   ├── agent-planner.md   # Feature breakdown and planning
│   ├── agent-test-eng.md  # TDD enforcement
│   ├── agent-frontend.md  # UI development
│   ├── agent-backend.md   # API/service development
│   └── agent-security.md  # Security review
├── specs/                 # Project specifications
│   ├── 01-architecture.md # System architecture
│   ├── product-spec.md    # Product requirements
│   ├── dataflow.md        # Data flow diagrams
│   └── dataflows/         # Per-feature dataflow diagrams
│       ├── command-dispatch.md
│       ├── config-management.md
│       ├── plugin-management.md
│       ├── platform-operations.md
│       └── system-info.md
├── standards/             # Engineering standards (org-wide)
│   ├── 01-ux-responsive.md
│   ├── 02-tdd-core.md
│   └── 03-observability.md
└── plans/
    └── status.md          # Current status and known issues
```

## Key Specs

- `.ai/specs/01-architecture.md` — System architecture
- `.ai/specs/product-spec.md` — Product requirements
- `.ai/specs/dataflow.md` — Data flow diagrams

## Conventions

- **Commits**: Conventional commits (`feat:`, `fix:`, `chore:`)
- **Plans**: Save to `.ai/plans/YYYY-MM-DD-<feature-name>.md`
- **Code style**: CommonJS `require()`, single quotes, ESLint `@cordova/eslint-config/node`
- **Testing**: Jasmine specs in `spec/`, rewire for internal mocking
- **Licensing**: All source files must include Apache 2.0 license header
