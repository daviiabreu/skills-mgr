<p align="center">
  <h1 align="center">skills-mgr</h1>
  <p align="center">
    Manage AI coding tool skills from the terminal.
    <br />
    One source of truth. Any tool. Global or per-project.
  </p>
  <p align="center">
    <a href="#install">Install</a> &middot;
    <a href="#quick-start">Quick Start</a> &middot;
    <a href="#supported-tools">Supported Tools</a> &middot;
    <a href="#cli-reference">CLI Reference</a>
  </p>
</p>

<br />

```
┌────────────────────────────────────────────────────────────────┐
│ Skills Manager  ·  Claude Code  ·  12/47 global  ·  3 projects │
└────────────────────────────────────────────────────────────────┘

  📌  my-project (5 skills)
  🔧  Manage global skills
  📁  Manage by project
  🔍  Search skill
  📊  Full status
  ⚙️  Target tool
  🌐  Language
  ❌  Exit
```

## Why

AI coding tools ship with 100+ skills. Most are noise for any given project. `skills-mgr` lets you toggle skills on/off globally or per-project — across Claude Code, Gemini CLI, Codex, and OpenCode — from a single place.

## Supported Tools

| Tool            | Global Skills                | Project Skills                |
| --------------- | ---------------------------- | ----------------------------- |
| **Claude Code** | `~/.claude/skills/`          | `<project>/.claude/skills/`   |
| **Gemini CLI**  | `~/.gemini/skills/`          | `<project>/.gemini/skills/`   |
| **Codex**       | `~/.agents/skills/`\*        | `<project>/.agents/skills/`   |
| **OpenCode**    | `~/.config/opencode/skills/` | `<project>/.opencode/skills/` |

> \* Codex reads directly from source — all skills are always visible globally. Per-project scoping still works.

All tools use the same `SKILL.md` format. Switch targets anytime with `skills-mgr tool <name>`.

## Install

### Homebrew (coming soon)

```bash
brew install daviabreu/tap/skills-mgr
```

### npm

```bash
npm i -g @daviabreu/skills-mgr
```

### Manual

```bash
curl -fsSL https://raw.githubusercontent.com/daviiabreu/skills-mgr/main/skills-mgr \
  -o ~/.local/bin/skills-mgr && chmod +x ~/.local/bin/skills-mgr
```

### Dependencies

- [gum](https://github.com/charmbracelet/gum) — `brew install gum`

## Quick Start

```bash
# Launch interactive TUI
skills-mgr

# Or use CLI directly
skills-mgr tool gemini          # target Gemini CLI
skills-mgr on find-skills       # enable a skill globally
skills-mgr off @recipe          # disable an entire category
skills-mgr project add          # register current dir
skills-mgr project enable . @gws  # enable GWS skills in this project
```

On first run, you'll be asked to choose a language (English or Portuguese).

## How It Works

```
~/.agents/skills/              ← source of truth (all installed skills)
  ├── gws-gmail/SKILL.md
  ├── find-skills/SKILL.md
  └── ...

~/.claude/skills/              ← Claude Code (symlinks to source)
  ├── find-skills → ~/.agents/skills/find-skills
  └── ...

~/.gemini/skills/              ← Gemini CLI (symlinks to source)
  └── ...

~/my-project/.claude/skills/   ← project-scoped (symlinks to source)
  ├── gws-gmail → ~/.agents/skills/gws-gmail
  └── ...
```

Skills are never copied — only symlinked. Enable/disable is instant. Global skills are inherited by all projects. Project skills add on top.

## TUI Features

- **Fuzzy search** across all skills with descriptions
- **Multi-select** enable/disable (space to select, enter to confirm)
- **Category toggles** — enable/disable `@gws`, `@recipe`, `@persona` etc. in one shot
- **Project management** — register projects, scope skills, compare with global
- **Tool switching** — change target tool without leaving the TUI
- **i18n** — English and Portuguese

## CLI Reference

```
skills-mgr                                 Interactive TUI
skills-mgr tool [claude|gemini|codex|opencode]  Get/set target tool
skills-mgr status [category]               Show skill status
skills-mgr on|enable <skill|@cat>          Enable globally
skills-mgr off|disable <skill|@cat>        Disable globally
skills-mgr info <skill>                    Skill details
skills-mgr cats                            List categories
skills-mgr ps                              List registered projects
skills-mgr project add [path]              Register project (default: .)
skills-mgr project rm <path>               Unregister project
skills-mgr project status [path]           Show project skills
skills-mgr project enable [path] <skill|@cat>   Enable in project
skills-mgr project disable [path] <skill|@cat>  Disable in project
skills-mgr lang <en|pt>                    Set language
```

## Categories

Batch operations with `@category`:

| Category   | Prefix          | Example                 |
| ---------- | --------------- | ----------------------- |
| `@gws`     | `gws-*`         | `skills-mgr off @gws`   |
| `@recipe`  | `recipe-*`      | `skills-mgr on @recipe` |
| `@persona` | `persona-*`     |                         |
| `@plugin`  | `plugin-*`      |                         |
| `@writing` | `writing-*`     |                         |
| `@other`   | everything else |                         |

## Config

Stored in `~/.config/skills-mgr/`:

| File            | Purpose                  |
| --------------- | ------------------------ |
| `tool.conf`     | Active target tool       |
| `lang.conf`     | Language (en/pt)         |
| `projects.conf` | Registered project paths |

> Migrates automatically from `~/.claude/skills-mgr/` on first run.

## Skill Advisor

Included skill that analyzes your project and recommends what to enable:

```bash
# Inside Claude Code
/skill-advisor
```

Detects `package.json`, `Cargo.toml`, `go.mod`, etc. and outputs ready-to-run `skills-mgr` commands.

## Requirements

- bash 3.2+
- [gum](https://github.com/charmbracelet/gum)

## License

MIT
