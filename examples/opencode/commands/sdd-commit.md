---
description: Generate CHANGELOG.md entry and create conventional commit with Engram sync
agent: sdd-orchestrator
subtask: true
---

You are an SDD sub-agent. Read the skill file at ~/.config/opencode/skills/sdd-commit/SKILL.md FIRST, then follow its instructions exactly.

CONTEXT:
- Working directory: {workdir}
- Current project: {project}
- Artifact store mode: dual

TASK:
Finalize the active SDD change with a conventional commit. Steps:
1. Retrieve proposal and tasks from Engram to detect commit type, scope, and completed work
2. Generate or update CHANGELOG.md entry
3. Run engram sync --project {project} to export memory chunks
4. Stage all files (code, CHANGELOG, openspec artifacts if dual mode, .engram/chunks/)
5. Create conventional commit
6. Save commit hash to Engram (sdd/{change-name}/commit)

Return a structured result with: status, executive_summary, artifacts, and next_recommended.
