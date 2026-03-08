# Persistence Contract (shared across all SDD skills)

## Engram is Mandatory

**Engram MUST be available and active.** If Engram MCP tools (`mem_save`, `mem_search`, `mem_get_observation`) are not accessible, halt immediately and notify the orchestrator with:

```
ERROR: Engram is required but not available.
Install Engram: https://github.com/gentleman-programming/engram
SDD cannot proceed without persistent memory.
```

Do NOT fall back to any other mode. Do NOT continue without Engram.

## Mode Resolution

The orchestrator passes `artifact_store.mode` with one of: `dual | engram-only`.

| Mode | Description |
|------|-------------|
| `dual` | **Default.** Write artifacts to both Engram AND `openspec/` filesystem. Engram is canonical; openspec is the auditable project trail. |
| `engram-only` | Write artifacts to Engram only. No `openspec/` directory is created or modified. Use when the project tree must stay clean or git-uncontaminated. |

`dual` is ALWAYS the default unless the orchestrator explicitly passes `engram-only`.

## Behavior Per Mode

| | Engram | openspec/ | Project files |
|---|---|---|---|
| `dual` | ✅ Always — canonical artifact | ✅ Always — auditable trail | ❌ Never |
| `engram-only` | ✅ Always — canonical artifact | ❌ Never | ❌ Never |

## Write Order in `dual` Mode

Always write to Engram first. If the openspec write fails, the Engram artifact is already saved and the skill returns a warning — the change is NOT lost.

```
1. Write to Engram (mem_save / mem_update)
2. Write to openspec/ filesystem
3. Return structured result
```

## State Persistence (Orchestrator)

The orchestrator persists DAG state after each phase transition to enable SDD recovery after context compaction.

| Mode | Persist State | Recover State |
|------|--------------|---------------|
| `dual` | Engram: `mem_save(topic_key: "sdd/{change-name}/state")` + `openspec/changes/{change-name}/state.yaml` | `mem_search("sdd/*/state")` → `mem_get_observation(id)` → parse YAML |
| `engram-only` | Engram: `mem_save(topic_key: "sdd/{change-name}/state")` | `mem_search("sdd/*/state")` → `mem_get_observation(id)` → parse YAML |

In `dual` mode, Engram is written first (canonical). The `state.yaml` file is a readable copy for human inspection.

## Common Rules

- NEVER create `openspec/` structure in `engram-only` mode.
- NEVER skip Engram in any mode — it is always required.
- In `dual` mode, ALWAYS write to openspec using the paths defined in `openspec-convention.md`.
- In `dual` mode, ALWAYS write to Engram using the naming defined in `engram-convention.md`.
- Project files (CHANGELOG.md, git commits, CLAUDE.md) are NEVER managed by the persistence contract — they are outputs written directly to the project regardless of mode.

## Exceptions

Two external skills follow different rules:

| Skill | Engram | openspec/ | Project |
|-------|--------|-----------|---------|
| `sdd-commit` | ✅ commit hash + context | ❌ | ✅ CHANGELOG.md + git commit |
| `sdd-docs` | ❌ | ✅ config.yaml | ✅ AGENTS.md, CLAUDE.md |

`sdd-commit` writes to Engram for traceability but its primary output is the git commit.
`sdd-docs` generates project configuration files; these belong in git, not in agent memory.

## Detail Level

The orchestrator may also pass `detail_level`: `concise | standard | deep`.
This controls output verbosity but does NOT affect what gets persisted — always persist the full artifact.
