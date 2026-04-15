# obsidian-kanban-skill

A portable Claude Code skill for managing Kanban boards as markdown files in any project.

## What this does

Teaches Claude how to create and maintain Kanban boards inside a project-local `kanbanboards/` folder. That folder is a self-contained Obsidian vault: boards, per-card issue specs, persistent context/memory files, and the full plugin reference guides all live inside it.

The boards are plain markdown. They read cleanly in any editor and render as interactive drag-and-drop boards in Obsidian when the `obsidian-kanban` plugin is installed. No build step, no runtime dependencies — just files on disk.

## Key features

- **Plain-markdown boards** — `## ` lane headings and `- [ ] ` / `- [x] ` cards work in any editor.
- **One self-contained vault per project** — everything for a project's boards lives under `<project_root>/kanbanboards/`.
- **Issue-spec files** — full implementation detail lives in linked spec files, so cards stay compact.
- **Persistent context memory** — each board carries a companion `_context.md` that survives across Claude sessions, preserving architecture decisions, key files, and task history.
- **Offline plugin reference** — the complete obsidian-kanban documentation travels with the vault.
- **No binaries shipped** — the Obsidian plugin itself is installed through Obsidian's UI, keeping the skill lightweight and version-flexible.

## Installation

Pick one of the two placements below. Both work; per-project keeps the skill versioned with the codebase, user-global makes it available everywhere.

**Per-project** (auto-picked-up by Claude Code for that project):

```bash
git clone https://github.com/HristovCodes/obsidian-kanban-skill.git \
  <project_root>/.claude/skills/obsidian-kanban
```

**User-global** (available in every project):

```bash
git clone https://github.com/HristovCodes/obsidian-kanban-skill.git \
  ~/.claude/skills/obsidian-kanban
```

Alternatively, clone the repo anywhere and point Claude Code at it with your usual skill-discovery configuration.

## Usage

Ask Claude to work with a Kanban board in your project:

```
Create a kanban board for the auth rewrite with lanes for Backlog, In Progress, and Done.
```

```
Move the "Add password reset" card to Done and log the result in the context file.
```

On first use in a project that has no `kanbanboards/` folder yet, Claude reads `SETUP.md` and scaffolds the vault: the folder, Obsidian config, example board, and reference guides. Subsequent runs skip bootstrap and go straight to board work.

## Obsidian is optional

The board files are plain markdown and render correctly in any editor. Obsidian with the `obsidian-kanban` plugin just adds an interactive drag-and-drop view on top of the same files. If no one on the team uses Obsidian, the `.md` files still work as a lightweight task tracker on their own.

## Installing the Obsidian plugin (optional)

Only needed for the interactive Kanban view.

1. Install Obsidian from <https://obsidian.md>.
2. Open Obsidian → **Open folder as vault** → select `<project_root>/kanbanboards/`.
3. **Settings → Community plugins**. If this is your first time, click **Turn on community plugins** and accept the warning.
4. Click **Browse**, search for **Kanban** by **mgmeyers**, click **Install**, then **Enable**.
5. Reopen any `.md` file with `kanban-plugin: board` in its frontmatter — it renders as an interactive board.

The bundled `.obsidian/community-plugins.json` already lists the plugin as enabled, so it activates automatically once installed through the UI.

## Repository layout

| Path | Purpose |
|------|---------|
| `SKILL.md` | The skill Claude loads — board format rules, conventions, critical rules, common mistakes, and references. |
| `SETUP.md` | Bootstrap instructions for projects without a `kanbanboards/` folder yet. Referenced from `SKILL.md`. |
| `CLAUDE.md` | Maintainer guidance for editing this skill repo itself. |
| `README.md` | This file — human-facing overview and plugin install instructions. |
| `example_board/` | A working example board folder: `example_board.md`, `example_board_context.md`, and `example_board_issues/`. Copied as a unit into new projects by `SETUP.md`, demonstrating the one-folder-per-board convention. |
| `.obsidian/` | Minimal Obsidian vault config (no plugin binaries — install via UI). |
| `kanban-guides/` | Full obsidian-kanban plugin reference: how-to guides, FAQs, and settings docs. |

## Conventions the skill enforces

- Board files, issue folders, and issue files use `snake_case`.
- Each board lives in its own `<board_name>/` folder with a matching `<board_name>_issues/` subfolder and a `<board_name>_context.md` companion file.
- Cards stay small on the board (title + one-line summary + `[[issue_link]]`); full detail lives in the linked issue file.
- The `_context.md` file is persistent memory — read at session start, updated after every completed task.

Full details live in [`SKILL.md`](./SKILL.md).

## License

MIT — use it, modify it, share it.
