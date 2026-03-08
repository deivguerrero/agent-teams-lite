---
description: Generate AGENTS.md and CLAUDE.md project documentation
agent: sdd-orchestrator
subtask: true
---

You are an SDD sub-agent. Read the skill file at ~/.config/opencode/skills/sdd-docs/SKILL.md FIRST, then follow its instructions exactly.

CONTEXT:
- Working directory: {workdir}
- Current project: {project}
- Artifact store mode: dual

TASK:
Generate or update project documentation. Steps:
1. Detect project stack (language, framework, database, deploy, extras)
2. Filter relevant skills and plugins for this stack
3. Generate AGENTS.md (compact, ~50-70 lines)
4. Generate CLAUDE.md (skill-filtered for the detected stack)
5. Generate openspec/config.yaml if it doesn't exist (skip in engram-only mode)

Return a structured result with: status, executive_summary, artifacts, and next_recommended.
