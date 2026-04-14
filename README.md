# obsidian-kanban-skill

A portable Claude Code skill for managing Kanban boards as markdown files in any project.

Each project gets its own `kanbanboards/` folder at the repository root. That folder is a self-contained Obsidian vault: boards, issue specs, persistent context/memory files, and the full plugin reference guides all live inside it. The skill instructs Claude how to create, edit, and maintain these boards consistently.

## Obsidian is optional

The board files are plain markdown and read perfectly well in any editor — `## ` lane headings and `- [ ]` / `- [x]` cards are standard markdown. Obsidian with the `obsidian-kanban` plugin just adds an interactive drag-and-drop view on top of the same files. If no one on the team uses Obsidian, the `.md` files still work as a lightweight task tracker on their own.

## Quick start

1. Place this skill directory where Claude Code can find it — either:
   - Clone/copy this repo somewhere and point Claude Code at it, or
   - Copy it into `<project_root>/.claude/skills/obsidian-kanban/` so it's picked up automatically for that project.
2. Ask Claude to create or work with a Kanban board in your project.
3. If the project has no `kanbanboards/` folder yet, Claude will follow `SETUP.md` to scaffold one (example board, vault config, reference guides).
4. Optional: install the Obsidian plugin (below) if you want the visual board view.

## Installing the obsidian-kanban plugin via the Obsidian UI

Only needed if you want the interactive Kanban view. The markdown files work without it.

1. Download Obsidian from <https://obsidian.md> and install it.
2. Open Obsidian and choose **"Open folder as vault"** → select `<project_root>/kanbanboards/`.
3. Open **Settings → Community plugins**.
4. If this is your first time, click **"Turn on community plugins"** (accept the warning).
5. Click **Browse**, search for **"Kanban"** by **mgmeyers**, then click **Install**.
6. After installing, click **Enable**.
7. Close and reopen any `.md` file with `kanban-plugin: board` in its frontmatter — it now renders as an interactive board.

The vault config in `.obsidian/community-plugins.json` already lists the plugin as enabled, so once it is installed in the Obsidian UI it will activate automatically.

## What's in this repo

| Path | Purpose |
|------|---------|
| `SKILL.md` | The skill Claude loads — board format rules, conventions, critical rules, common mistakes, and references. |
| `SETUP.md` | Bootstrap instructions for projects without a `kanbanboards/` folder yet. Referenced from `SKILL.md`. |
| `README.md` | This file — human-facing overview and plugin install instructions. |
| `example_board/` | A working example board folder — contains `example_board.md`, `example_board_context.md`, and `example_board_issues/`. Copied as a unit into new projects by `SETUP.md`, demonstrating the one-folder-per-board convention. |
| `.obsidian/` | Minimal Obsidian vault config (no plugin binaries — install via UI). |
| `kanban-guides/` | Full obsidian-kanban plugin reference: how-to guides, FAQs, and settings docs. |

## Conventions the skill enforces

- Board files, issue folders, and issue files use `snake_case`.
- Each board has a matching `<board_name>_issues/` folder and a `<board_name>_context.md` companion file.
- Cards stay small on the board (title + one-line summary + `[[issue_link]]`); full details live in the linked issue file.
- The `_context.md` file is persistent memory — read at session start, updated after every completed task.

Full details are in `SKILL.md`.
