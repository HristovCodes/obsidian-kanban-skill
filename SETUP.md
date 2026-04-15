# SETUP — Bootstrap a `kanbanboards/` Vault in a New Project

This file is referenced from `SKILL.md`. Follow it end-to-end when a project has no `kanbanboards/` folder at its root yet. After running through these steps, the project will have a self-contained Obsidian vault with an example board, reference guides, and the configuration needed for the obsidian-kanban plugin.

## When to use this file

`SKILL.md` instructs Claude to check for `<project_root>/kanbanboards/` before doing any board work. If that folder does **not** exist, stop and run this bootstrap first. If the folder already exists with at least an `.obsidian/` config and an `example_board.md`, bootstrap has already been done — skip this file.

## Assumptions

- `<project_root>` is the top-level directory of the project Claude is working in (the directory that contains the project's source code, e.g. the git repository root).
- This skill repository lives at a known path — call it `<skill_dir>` below. Typically it is either a cloned copy of this repo, or installed under `<project_root>/.claude/skills/obsidian-kanban/`.

## Step 1 — Create the `kanbanboards/` folder

```bash
mkdir -p <project_root>/kanbanboards
```

Everything in the remaining steps goes inside this new folder.

## Step 2 — Copy the Obsidian vault config

Copy the `.obsidian/` folder from this skill directory into the new vault. It contains the minimum configuration the obsidian-kanban plugin needs to be recognized once the user installs it.

```bash
cp -r <skill_dir>/.obsidian <project_root>/kanbanboards/.obsidian
```

What each file does:

| File | Purpose |
|------|---------|
| `community-plugins.json` | Declares `obsidian-kanban` as an enabled community plugin |
| `core-plugins.json` | Standard Obsidian core plugin toggles (file explorer, search, etc.) |
| `appearance.json` | Dark theme with a lime-green accent (`#85f55c`) — safe to customize |
| `app.json` | Empty placeholder required by Obsidian |

Note: the actual plugin binaries (`plugins/obsidian-kanban/main.js`, `styles.css`, `manifest.json`) are **not** included here. They are installed by the user through Obsidian's Community Plugins browser — see `README.md` for the UI steps. The vault works as plain markdown without them.

## Step 3 — Copy the example board folder

Copy the entire `example_board/` folder so the user has a concrete reference for the expected per-board structure:

```bash
cp -r <skill_dir>/example_board <project_root>/kanbanboards/
```

After the copy, the example lives at `<project_root>/kanbanboards/example_board/` and contains:

- `example_board.md` — the kanban board itself (lanes, cards with summary + issue link)
- `example_board_issues/` — one `.md` file per card with full implementation detail
- `example_board_context.md` — persistent feature memory (architecture, files, task history)

This demonstrates the canonical pattern: **each board gets its own `<board_name>/` folder** containing the board file, issues folder, and context file. Real boards the user creates later should follow the same layout.

## Step 4 — Copy the reference guides

Copy the `kanban-guides/` folder so plugin documentation travels with the vault:

```bash
cp -r <skill_dir>/kanban-guides <project_root>/kanbanboards/
```

These are the full obsidian-kanban plugin docs — how-to guides, FAQs, and settings reference. They are linked from the Reference Guides section of `SKILL.md`.

## Step 5 — Register the skill in the project's `CLAUDE.md`

So the user does not have to invoke this skill manually every session, append a Kanban section to `<project_root>/CLAUDE.md`. This teaches future Claude sessions when to load the skill and how to work with boards already in the vault.

- If `<project_root>/CLAUDE.md` does not exist, create it.
- If it already exists, append the block below to the end — do not overwrite existing content.
- Use the exact text below verbatim. The `<project_root>` and `<board_name>` placeholders stay as placeholders — do **not** substitute real paths.

```markdown
## Task Planning with Kanban Boards

When asked to plan a task with phases, issues, or sub-issues, create an Obsidian Kanban board in this project's vault at `<project_root>/kanbanboards/`.

**Workflow:**
1. Load the `obsidian-kanban` skill (board format, card structure, naming conventions)
2. Read the example board and issue files in `<project_root>/kanbanboards/example_board/` before creating anything
3. Create the board as `<board_name>/<board_name>.md` (snake_case) with compact cards: title + one-line summary + `[[link to issue file]]`
4. Create a `<board_name>_issues/` folder with one `.md` file per card containing the full implementation spec
5. Create `<board_name>_context.md` alongside the board using `<project_root>/kanbanboards/example_board/example_board_context.md` as the template

**Key rules:**
- Board and folder names use **snake_case**
- Issue filenames use `{number}-{kebab-title}.md`
- Cards link to issue files via `[[<board_name>_issues/<issue>]]` — keep the board file small
- Lanes represent phases or workflow stages (e.g., To Do, In Progress, Done)
- Use `**Complete**` marker on "Done" lanes, WIP limits where appropriate
- All boards live in `<project_root>/kanbanboards/`, each in its own `<board_name>/` folder

## Picking Up and Completing Kanban Tasks

When asked to pick up or complete tasks, look at the board files in `<project_root>/kanbanboards/`.

**1. Find the task.** Read the board `.md` file(s) and scan lane headings and card titles to locate the requested task(s).

**2. Pick up a task.** Move the card's `- [ ]` line (and all its TAB-indented content) from its current lane (e.g., `## To Do`) into the `## In Progress` lane.

**3. Mark a task complete.** Change `- [ ]` to `- [x]` and move the card into the `## Done` lane (the one with `**Complete**` on the line after the heading).

**4. Update linked issue files.** If the card links to an issue spec (e.g., `[[<board_name>_issues/1.1-task-spec]]`), open that file and update its `Status` field to reflect the new state (e.g., `In Progress` or `Done`).

## Persistent Context (Feature Memory)

Each board has a companion context file: `<board_name>_context.md` in the same folder. This file is the persistent memory for that feature — it preserves decisions, patterns, file locations, and implementation history across Claude Code sessions.

**Why it matters:** This file is living documentation. If the feature is revisited months later, Claude reads this file and has full context without rediscovering the codebase.

**At session start:**
1. Read the board file to see current task states
2. Read `<board_name>_context.md` — this tells you everything about the feature: architecture, files, patterns, current state
3. Read issue files only for tasks you're actively working on

**After completing any task:**
1. Update the context file:
   - Add any new architecture decisions to "Architecture & Decisions"
   - Add/update entries in "Key Files & Locations"
   - Add any new patterns or gotchas to "Implementation Notes"
   - Update the task's row in "Task History"
   - Update "Current State" to reflect what's working and what's next

**When creating a new board:**
1. Create the board folder, board file, and issues folder as usual
2. Create `<board_name>_context.md` using the template from `<project_root>/kanbanboards/example_board/example_board_context.md`
3. Fill in the Feature Overview section; other sections populate as work progresses
```

After inserting, tell the user that `CLAUDE.md` was updated so Claude will know to use the skill automatically on future board-related requests.

## Step 6 — Tell the user about the optional Obsidian plugin install

After bootstrap, inform the user:

> The `kanbanboards/` vault is set up. The `.md` board format works as plain markdown and is fully usable without Obsidian. If you want the interactive Kanban view, install the obsidian-kanban plugin in Obsidian — the install steps are in `<skill_dir>/README.md`.

Do **not** attempt to install the Obsidian plugin programmatically — it is a UI-driven install handled by the user.

## Verification

After running all steps, the project root should look like this:

```
<project_root>/
└── kanbanboards/
    ├── .obsidian/
    │   ├── app.json
    │   ├── appearance.json
    │   ├── community-plugins.json
    │   └── core-plugins.json
    ├── example_board/
    │   ├── example_board.md
    │   ├── example_board_context.md
    │   └── example_board_issues/
    │       ├── 1.1-database-tables.md
    │       ├── 1.2-api-endpoints.md
    │       └── 1.3-login-page.md
    └── kanban-guides/
        ├── Obsidian Kanban Plugin.md
        ├── FAQs/
        ├── How do I/
        └── Settings/
```

Quick checks:

- `ls <project_root>/kanbanboards/example_board/example_board.md` exists and starts with `---\nkanban-plugin: board\n---`.
- `ls <project_root>/kanbanboards/.obsidian/community-plugins.json` exists and contains `"obsidian-kanban"`.
- `ls <project_root>/kanbanboards/kanban-guides/` contains at least the `How do I/`, `FAQs/`, and `Settings/` subfolders.

Once these pass, bootstrap is complete. Return to `SKILL.md` and proceed with the user's original board request.
