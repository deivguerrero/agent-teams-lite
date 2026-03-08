---
name: sdd-init
description: >
  Initialize Spec-Driven Development context in any project. Detects stack, conventions, and bootstraps the active persistence backend.
  Trigger: When user wants to initialize SDD in a project, or says "sdd init", "iniciar sdd", "openspec init".
license: MIT
metadata:
  author: gentleman-programming
  version: "2.0"
---

## Purpose

You are a sub-agent responsible for initializing the Spec-Driven Development (SDD) context in a project. You detect the project stack and conventions, then bootstrap the active persistence backend.

## Execution and Persistence Contract

Read and follow `skills/_shared/persistence-contract.md` for mode resolution rules.

- **Engram is always required.** If Engram tools are unavailable, halt and report the error.
- If mode is `dual`: Read and follow BOTH `skills/_shared/engram-convention.md` AND `skills/_shared/openspec-convention.md`. Persist context to Engram first, then bootstrap `openspec/`.
- If mode is `engram-only`: Read and follow `skills/_shared/engram-convention.md` only. Do NOT create `openspec/` or any project files.

## What to Do

### Step 1: Detect Project Context

Read the project to understand:
- Tech stack (check package.json, go.mod, pyproject.toml, etc.)
- Existing conventions (linters, test frameworks, CI)
- Architecture patterns in use

### Step 2: Initialize Persistence Backend

In `dual` mode, create this directory structure in the project:

```
openspec/
├── config.yaml              ← Project-specific SDD config
├── specs/                   ← Source of truth (empty initially)
└── changes/                 ← Active changes
    └── archive/             ← Completed changes
```

In `engram-only` mode, skip the openspec structure but still run the `.gitignore` setup below.

### Step 2b: Configure .gitignore for Engram (all modes)

**Always** ensure `.gitignore` has the correct Engram entries, regardless of mode:

```bash
# Check if .gitignore already has Engram entries
grep -q "engram.db" .gitignore 2>/dev/null || cat >> .gitignore << 'EOF'

# Engram — local memory database (never commit, it's personal and binary)
.engram/engram.db
.engram/*.db

# Engram chunks are committed intentionally — they enable team memory sync
# DO NOT add .engram/chunks/ to .gitignore
EOF
```

If `.gitignore` doesn't exist, create it with the entries above.

Report what was done:
```
✓ .gitignore — .engram/engram.db excluded (personal SQLite, never versioned)
✓ .gitignore — .engram/chunks/ NOT excluded (team sync artifacts, always versioned)
```

### Step 3: Generate Config (dual mode only)

Based on what you detected, create `openspec/config.yaml` when in `dual` mode:

```yaml
# openspec/config.yaml
schema: spec-driven

context: |
  Tech stack: {detected stack}
  Architecture: {detected patterns}
  Testing: {detected test framework}
  Style: {detected linting/formatting}

rules:
  proposal:
    - Include rollback plan for risky changes
    - Identify affected modules/packages
  specs:
    - Use Given/When/Then format for scenarios
    - Use RFC 2119 keywords (MUST, SHALL, SHOULD, MAY)
  design:
    - Include sequence diagrams for complex flows
    - Document architecture decisions with rationale
  tasks:
    - Group tasks by phase (infrastructure, implementation, testing)
    - Use hierarchical numbering (1.1, 1.2, etc.)
    - Keep tasks small enough to complete in one session
  apply:
    - Follow existing code patterns and conventions
    - Load relevant coding skills for the project stack
  verify:
    - Run tests if test infrastructure exists
    - Compare implementation against every spec scenario
  archive:
    - Warn before merging destructive deltas (large removals)
```

### Step 4: Return Summary

Persist project context to Engram following `skills/_shared/engram-convention.md` with title and topic_key `sdd-init/{project-name}`. Always do this regardless of mode.

Return a structured summary adapted to the resolved mode:

#### Result in `dual` mode:
```
## SDD Initialized

**Project**: {project name}
**Stack**: {detected stack}
**Mode**: dual (Engram + openspec)

### Engram
- Context saved — Topic key: sdd-init/{project-name}
- Engram ID: #{observation-id}

### openspec/ Created
- openspec/config.yaml ← Project config with detected context
- openspec/specs/      ← Ready for specifications
- openspec/changes/    ← Ready for change proposals

### .gitignore Configured
- .engram/engram.db → excluded (personal binary, never versioned)
- .engram/chunks/   → versioned (team memory sync artifacts)

### Next Steps
Ready for /sdd-explore <topic> or /sdd-new <change-name>.
```

#### Result in `engram-only` mode:
```
## SDD Initialized

**Project**: {project name}
**Stack**: {detected stack}
**Mode**: engram-only

### Engram
- Context saved — Topic key: sdd-init/{project-name}
- Engram ID: #{observation-id}

### .gitignore Configured
- .engram/engram.db → excluded (personal binary, never versioned)
- .engram/chunks/   → versioned (team memory sync artifacts)

### Next Steps
Ready for /sdd-explore <topic> or /sdd-new <change-name>.
```

## Rules

- NEVER create placeholder spec files - specs are created via sdd-spec during a change
- ALWAYS detect the real tech stack, don't guess
- If the project already has an `openspec/` directory, report what exists and ask the orchestrator if it should be updated
- Keep config.yaml context CONCISE - no more than 10 lines
- Return a structured envelope with: `status`, `executive_summary`, `detailed_report` (optional), `artifacts`, `next_recommended`, and `risks`
