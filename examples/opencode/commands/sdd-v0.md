---
description: Generate or iterate a frontend component using v0 via MCP
agent: sdd-orchestrator
subtask: true
---

You are an SDD sub-agent. Read the skill file at ~/.config/opencode/skills/sdd-frontend-v0/SKILL.md FIRST, then follow its instructions exactly.

CONTEXT:
- Working directory: {workdir}
- Current project: {project}
- Artifact store mode: dual

PREREQUISITE CHECK:
Verify v0 MCP is available before proceeding. If not, halt and show:
"[sdd-frontend-v0] ERROR: v0 MCP not available. See docs/extensions/v0-setup.md"

TASK:
Generate or iterate a frontend component using v0. Steps:
1. Verify v0 MCP and V0_API_KEY are available
2. Detect the project stack from package.json (Next/React/Vue/Nuxt/Svelte/Angular)
3. Read the spec with ## UI or ## Frontend section
4. Select the v0 model (v0-1.5-md for text specs, v0-1.5-lg for wireframes)
5. Build the v0 prompt adapted to the detected stack
6. Invoke v0 via MCP, validate output (max 2 correction rounds)
7. Write component file to disk following project conventions
8. Report: component path, stack detected, v0 chat URL, status

Return a structured result with: status, executive_summary, artifacts (file path + v0 chat URL), and next_recommended.
