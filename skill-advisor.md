---
name: skill-advisor
description: Analyzes a project's tech stack and recommends which skills to activate or deactivate using skills-mgr. Invoke when entering a new project or when the user asks which skills make sense for their repo.
---

# Skill Advisor

Analyze the current project and recommend which Claude Code skills to activate.

## When to Use

- User asks "which skills should I use here?"
- User enters a new project and wants skill recommendations
- User wants to clean up / optimize their active skills for a repo

## How It Works

1. **Analyze the project** by reading key files to identify the tech stack
2. **Map tech stack to relevant skill categories**
3. **Generate a recommendation** with reasoning
4. **Optionally apply** via `skills-mgr`

## Step 1: Detect Tech Stack

Read whichever of these files exist in the project root:

- `package.json` — Node.js/frontend/React/Next.js
- `Cargo.toml` — Rust
- `go.mod` — Go
- `pyproject.toml`, `requirements.txt`, `setup.py` — Python
- `build.gradle`, `build.gradle.kts`, `pom.xml` — Java/Kotlin
- `Gemfile` — Ruby
- `CMakeLists.txt`, `Makefile` — C/C++
- `Package.swift` — Swift
- `docker-compose.yml`, `Dockerfile` — Docker
- `.github/workflows/` — CI/CD
- `CLAUDE.md` — existing Claude Code config
- `README.md` — project description

Also check for:
- `.claude/skills/` — already active project-level skills
- Google Workspace usage (gws CLI, Google API dependencies)
- Database files (`schema.sql`, `migrations/`, Prisma, Drizzle, etc.)

## Step 2: Map to Skills

Use this mapping to decide which skill categories are relevant:

### Always Relevant (development tools)
These are almost always useful regardless of project type:
- `find-skills` — discovering new skills
- `skill-development` — if the user creates skills
- `agent-development` — if building Claude Code plugins
- `hook-development` — if building hooks
- `plugin-settings` — if building plugins
- `plugin-structure` — if building plugins

### Google Workspace (`@gws` + `@recipe` + `@persona`)
**Activate when:** Project uses Google APIs, has `googleapis` dependency, gws CLI, or is a productivity/business tool.
**Deactivate when:** Pure software engineering project with no Google integration.

Individual GWS skills to consider:
- `gws-gmail*` — Email automation
- `gws-calendar*` — Calendar management
- `gws-docs*` — Document generation
- `gws-sheets*` — Spreadsheet operations
- `gws-drive*` — File management
- `gws-chat*` — Team messaging
- `gws-meet` — Video conferencing
- `gws-tasks` — Task management
- `gws-forms` — Form creation
- `gws-slides` — Presentations
- `gws-classroom` — Education
- `gws-keep` — Notes
- `gws-modelarmor*` — Content safety/filtering
- `gws-workflow*` — Cross-service workflows

Recipes (`@recipe`) are GWS workflow automations — only relevant if GWS is in use.
Personas (`@persona`) are role-based GWS presets — only relevant if GWS is in use.

### Frontend
- `frontend-design` — React/Vue/Svelte/Next.js projects
- `remotion-best-practices` — Video rendering with Remotion

### Research & Search
- `valyu-best-practices` — Projects using Valyu API for AI search

### Content
- `writing-hookify-rules` — Projects that use hookify

## Step 3: Generate Recommendation

Present the recommendation as a table:

```
| Skill/Category     | Recomendacao | Motivo                              |
|--------------------|-------------|--------------------------------------|
| @gws               | DESATIVAR   | Projeto e um CLI em Rust, sem GWS   |
| @recipe            | DESATIVAR   | Sem integracao Google                |
| @persona           | DESATIVAR   | Sem integracao Google                |
| frontend-design    | ATIVAR      | Projeto React com Next.js            |
| find-skills        | MANTER      | Util para descobrir novas skills     |
```

Use these labels:
- **ATIVAR** — activate (currently off, should be on)
- **DESATIVAR** — deactivate (currently on, should be off)
- **MANTER** — keep as is (already correct)

## Step 4: Apply

After showing the recommendation, ask the user if they want to apply.

If the project is NOT registered in skills-mgr yet:
```bash
skills-mgr project add <project-path>
```

To apply the recommendations:
```bash
# Disable globally what's not needed
skills-mgr off @gws
skills-mgr off @recipe
skills-mgr off @persona

# Or enable for this project specifically
skills-mgr project enable <path> frontend-design
```

**Important:** Ask the user before running any commands. Show the exact commands that will be executed.

## Step 5: Summary

After applying, show:
- How many skills were changed
- Current state via `skills-mgr status` or `skills-mgr project status <path>`

## Notes

- Never disable `find-skills` — it's always useful
- Local skills (humanizer, writing-papers, learned) are not managed by skills-mgr
- When in doubt, recommend keeping a skill active — it's better to have it and not need it
- Consider if the user might use GWS tools for project management even in a pure code project
- Skills from other sources (everything-claude-code, pr-review-toolkit, etc.) are installed as plugins/agents and are NOT managed by skills-mgr — only mention skills in `~/.agents/skills/`
