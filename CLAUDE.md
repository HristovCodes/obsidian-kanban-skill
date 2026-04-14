# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working **inside this repository**.

## What this repository is

This is a **portable Claude Code skill** for managing Kanban boards as markdown files in any project. The skill is designed to be dropped into any codebase — either cloned standalone or copied into `<project_root>/.claude/skills/obsidian-kanban/`. When invoked in a project, it teaches Claude how to create and maintain kanban boards inside that project's own `kanbanboards/` folder.

**Two modes of working with this repo:**

1. **Using the skill** (inside a host project) — Claude reads `SKILL.md` and follows its rules to manage that project's boards.
2. **Maintaining the skill** (inside this repo) — Claude edits the skill files themselves. That's what this CLAUDE.md is for.

If you are here to use the skill, start with `SKILL.md`. If you are here to maintain it, read on.

## Repository layout

```
./
├── CLAUDE.md                 ← this file (maintainer guidance)
├── README.md                 ← human-facing overview + plugin install instructions
├── SKILL.md                  ← the skill itself (what Claude loads in host projects)
├── SETUP.md                  ← bootstrap instructions referenced from SKILL.md
├── example_board/            ← the example board scaffold (copied into host projects by SETUP.md)
│   ├── example_board.md
│   ├── example_board_context.md
│   └── example_board_issues/
│       ├── 1.1-database-tables.md
│       ├── 1.2-api-endpoints.md
│       └── 1.3-login-page.md
├── .obsidian/                ← minimal Obsidian vault config (no plugin binaries)
│   ├── app.json
│   ├── appearance.json
│   ├── community-plugins.json
│   └── core-plugins.json
└── kanban-guides/            ← obsidian-kanban plugin reference docs (copied into host projects)
    ├── Obsidian Kanban Plugin.md
    ├── FAQs/
    ├── How do I/
    └── Settings/
```

## How the pieces fit together

- **`SKILL.md`** is the entry point. A host project's Claude loads it when asked to work with kanban boards. It instructs Claude to check for `<project_root>/kanbanboards/` and — if missing — to read `SETUP.md` and run bootstrap.
- **`SETUP.md`** describes the bootstrap flow: create the folder, copy `.obsidian/`, copy `example_board/`, copy `kanban-guides/`. It is only read when a host project is being set up for the first time.
- **`README.md`** is for humans, not for Claude's runtime. It explains what the skill is, how to install it, how to optionally install the Obsidian plugin via the UI, and tours the repo.
- **`example_board/`** is the canonical reference for the per-board folder pattern (`<board_name>/<board_name>.md` + `<board_name>_issues/` + `<board_name>_context.md`). SETUP.md copies the whole folder into host projects so users have a working example.
- **`.obsidian/`** holds the minimum vault config needed for Obsidian to recognize the folder as a vault with the kanban plugin enabled. Plugin binaries are deliberately omitted — users install the plugin through Obsidian's Community Plugins browser.
- **`kanban-guides/`** is the full plugin documentation, travelling with the vault so docs remain accessible offline.

## Rules for editing this repo

1. **Keep the skill project-agnostic.** Nothing in `SKILL.md`, `SETUP.md`, `README.md`, `CLAUDE.md`, or `example_board/` should reference a specific company, team, product, language, or project name. Generic placeholders like `<project_root>`, `<board_name>`, `<skill_dir>` are the convention.

2. **SKILL.md and SETUP.md must stay in sync.**
   - If you change the structure `SETUP.md` bootstraps, update `SKILL.md`'s file-structure diagram and live-example paths.
   - If you add or remove a file in `example_board/`, `.obsidian/`, or `kanban-guides/`, update SETUP.md's copy steps and verification tree.
   - If you change naming conventions in SKILL.md, update `example_board/` to match.

3. **`example_board/` is a scaffold, not a real board.** Its content (auth feature with users/sessions/login) is placeholder demonstration material. When editing it, keep the content:
   - Generic and recognizable to any web-app team (avoid niche tech).
   - Consistent across the board, context, and issue files (same decisions, same file names).
   - Free of any non-English text and free of any real project references.

4. **Do not bundle plugin binaries.** `.obsidian/plugins/obsidian-kanban/` must stay absent. The skill depends on users installing the plugin via the Obsidian UI (steps live in `README.md`). Bundling the ~1 MB binaries bloats the repo and ties it to a specific plugin version.

5. **Preserve the kanban markdown format exactly.** `example_board/example_board.md` is a working kanban board — it must keep:
   - `kanban-plugin: board` in frontmatter.
   - `## ` (H2) for lane titles, optional `(N)` WIP limit suffix.
   - `- [ ]` / `- [x]` for cards, with TAB-indented continuation lines.
   - `**Complete**` marker on the done lane.
   - The `%% kanban:settings ... %%` block as the last thing in the file.
   
   If you edit the example board, validate it still parses as a kanban board by re-reading SKILL.md's "Board File Structure" section.

6. **Naming conventions are enforced in both the rules and the scaffold.** All file and folder names in `example_board/` use `snake_case`; issue files use `{number}-{kebab-case-title}.md`. Changing the conventions means changing SKILL.md, the scaffold, and SETUP.md's copy commands and verification tree together.

7. **Never edit `kanban-guides/`.** Those files are upstream documentation for the obsidian-kanban plugin. Treat them as a vendored artifact. If they need updating, re-export them from the plugin's documentation source.

## Common maintenance tasks

**Updating a rule in SKILL.md:**
- Edit SKILL.md.
- Check if the rule also applies to or is demonstrated by `example_board/` — update the scaffold to match if so.
- Check if SETUP.md references the rule — update if so.

**Adding a new section to SKILL.md:**
- Add the content.
- If the section introduces a new file type or pattern, ensure `example_board/` demonstrates it.
- If it changes setup expectations, update `SETUP.md`.

**Tweaking the example scaffold:**
- Keep the three-file pattern (`<board_name>.md` + `<board_name>_issues/` + `<board_name>_context.md`).
- Keep content generic and in English.
- Re-read SKILL.md's Critical Rules and confirm the example still satisfies all of them.

**Adding a new file to the scaffold:**
- Add it under `example_board/` (or wherever it belongs).
- Update SETUP.md's Step 3 copy command and verification tree.
- Update README.md's file tour.
- Update SKILL.md's file-structure diagram if it's part of the per-board layout.

## What Claude should NOT do in this repo

- Do not add host-project-specific examples to SKILL.md or the scaffold.
- Do not introduce a SKILL.md rule that is not also demonstrated or at least consistent with `example_board/`.
- Do not commit plugin binaries under `.obsidian/plugins/`.
- Do not translate or localize any content — the skill is English-only.
- Do not edit `kanban-guides/` content.

## Verification

After making changes, run these sanity checks:

- `grep -rE '[А-Яа-яЁё]' .` → no matches (no Cyrillic / non-English leakage).
- `ls example_board/example_board.md` exists and begins with `---\nkanban-plugin: board\n---`.
- SKILL.md's file-structure diagram matches the actual layout of `example_board/`.
- SETUP.md's verification tree matches what its copy commands would produce.
- README.md's file-tour table lists every top-level entry in the repo.
