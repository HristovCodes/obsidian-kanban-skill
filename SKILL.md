---
name: obsidian-kanban
description: Create and edit Obsidian Kanban board markdown files in any project's `kanbanboards/` folder. Use when asked to create, modify, or work with Kanban boards.
---

# Obsidian Kanban Skill

Create and edit markdown-backed Kanban boards for the obsidian-kanban plugin (v2.0.51). Boards are plain `.md` files that render as interactive Kanban boards in Obsidian, and still read cleanly as plain markdown without it.

This skill is **project-portable**: it expects each project to have its own `kanbanboards/` folder at the repository root (e.g. `<project_root>/kanbanboards/`). That folder is a self-contained Obsidian vault holding all boards, issue specs, context files, and reference guides for the project.

---

## Bootstrap — First-Time Setup in a New Project

**Before creating or editing any board, check that `<project_root>/kanbanboards/` exists.**

- If the folder exists, proceed with the rules below.
- If it does **not** exist, **read `SETUP.md` in this skill directory and follow it end-to-end** before doing any board work. SETUP.md scaffolds the folder, the Obsidian vault config, the example board + issues + context file, and the reference guides.

Do not attempt to create a board before bootstrap is complete — the vault configuration and example scaffold need to be in place first.

---

## Board File Structure

Every board file must follow this exact format:

```markdown
---
kanban-plugin: board
---

## Lane Title

- [ ] Card one
- [ ] Card two
- [x] Completed card

## Another Lane (3)

- [ ] Card with a [[Link to note]]
- [ ] Card with date @{2024-01-15}

## Done

**Complete**
- [x] Finished task


%% kanban:settings
```
{"kanban-plugin":"board"}
```
%%
```

### Key structural rules

1. **Frontmatter** must contain `kanban-plugin: board` — this is how the plugin identifies a file as a kanban board
2. **Lanes** are `## ` headings (H2). Each lane is a column on the board
3. **Cards** are list items with checkboxes: `- [ ] ` (incomplete) or `- [x] ` (complete)
4. **WIP limits** are set in parentheses after the lane title: `## In Progress (5)` limits that lane to 5 cards. The counter turns bold when exceeded
5. **Complete lane** — add `**Complete**` on its own line before cards in a lane to mark it as the "done" lane. Cards moved here get auto-checked
6. **Archive** — archived cards appear at the bottom of the file (only visible in markdown mode). Controlled by the "Maximum number of archived cards" setting (-1 for unlimited)
7. **Board settings block** goes at the very end of the file wrapped in `%% kanban:settings ... %%`

---

## Card Content: Multi-line Cards and Issue Links

Cards support multi-line content. Continuation lines after the `- [ ] Title` line must be **indented with one TAB character**. The plugin renders all indented lines as the card body.

### Preferred pattern: title + summary + link to issue file

For boards tracking implementation tasks, **always** use this pattern to keep the board file small while providing full detail:

1. **Card title**: short descriptive name
2. **One-line summary**: what this task does (tab-indented)
3. **Link to issue file**: `[[<board_name>_issues/<issue>]]` with full implementation details (tab-indented)

Create a **separate `.md` file per card** in a `<board_name>_issues/` folder next to the board. These issue files are regular Obsidian notes (no kanban frontmatter) and can contain full implementation plans, code snippets, SQL, file lists, etc.

### Naming convention: snake_case

All board files, issue folders, and issue files use **snake_case**.

### Live example

A working example is installed by SETUP.md into every project's `kanbanboards/` folder — **read these files before creating new boards**:

- **Board:** `<project_root>/kanbanboards/example_board/example_board.md`
- **Context file:** `<project_root>/kanbanboards/example_board/example_board_context.md`
- **Issue files:**
  - `<project_root>/kanbanboards/example_board/example_board_issues/1.1-database-tables.md`
  - `<project_root>/kanbanboards/example_board/example_board_issues/1.2-api-endpoints.md`
  - `<project_root>/kanbanboards/example_board/example_board_issues/1.3-login-page.md`

### File structure

Each board gets its own folder under `kanbanboards/`. This keeps the vault tidy when the project grows to multiple boards:

```
<project_root>/
└── kanbanboards/                         ← the Obsidian vault for this project
    ├── <board_name>/                     ← one folder per board
    │   ├── <board_name>.md               ← kanban board (compact cards with summaries + links)
    │   ├── <board_name>_issues/
    │   │   ├── 1.1-database-tables.md    ← full implementation spec
    │   │   ├── 1.2-api-endpoints.md
    │   │   └── 1.3-login-page.md
    │   └── <board_name>_context.md       ← persistent memory / feature documentation
    ├── .obsidian/                        ← vault config (installed by SETUP.md)
    └── kanban-guides/                    ← plugin reference docs
```

Board and folder names use `snake_case`. Issue filenames use `{number}-{kebab-case-title}.md`. The `_issues` suffix ties the folder to its board. The `_context.md` file preserves working memory across sessions.

### Context file (feature memory)

Every board has a companion `<board_name>_context.md` file in the same folder. This file is persistent memory — it preserves architecture decisions, key files, implementation patterns, and current state across Claude Code sessions. It also serves as long-term feature documentation.

**At session start:** Read the context file before doing any work. It tells you everything about the feature.

**After completing any task:** Update the context file — add decisions, files, patterns, update task history and current state.

**When creating a new board:** Also create its `_context.md` file. Use `example_board_context.md` as the template. Fill in the Feature Overview; other sections populate as work progresses.

**Context file sections:**
- **Feature Overview** — what this feature does, why, where it lives
- **Architecture & Decisions** — key decisions with rationale
- **Key Files & Locations** — files created/modified, grouped logically
- **Implementation Notes** — patterns, conventions, gotchas
- **Task History** — table of tasks with summary and status
- **Current State** — what's working, what's pending, known issues

### When to use each approach

| Scenario | Card format |
|----------|-------------|
| Simple task (1-2 lines of context) | Title + tab-indented summary, no spec file |
| Implementation task with details | Title + one-line summary + `[[spec link]]` |
| Completed/archived cards | Title only (details no longer needed on the card) |

### Multi-line card syntax reference

```markdown
- [ ] Card title goes here
	Second line (indented with ONE TAB)
	Third line with **markdown** and `code`
	[[Link to detailed spec]]
```

**Important:** Use actual TAB characters (`\t`), not spaces. The plugin serializes multi-line cards using tabs (or 4 spaces if the vault is configured for spaces, but tabs are the default).

---

## Cards

### Dates and times on cards

- **Date trigger**: typing `@` (default) in a card opens the date picker. Dates are formatted using Moment.js format strings (default: `YYYY-MM-DD`)
- **Time trigger**: typing `@@` (default) opens the time picker. Only available on cards that already have a date
- Dates can also be added via right-click > "Add a date"

### Images in cards

Images are embedded using standard Obsidian syntax:

```markdown
- [ ] Card with image ![[photo.png]]
```

When using images in linked note frontmatter, they **must be wrapped in quotes** (YAML quirk):

```yaml
---
delivery-notes: "![[LinkToImage.png]]"
---
```

Inline Dataview fields do NOT require quotes:

```markdown
delivery-notes:: ![[LinkToImage.png]]
```

### Creating notes from cards

Right-click a card > "New note from card" creates a note using the configured Note template in the configured Note folder. The card then links to the new note.

### Linked page metadata

Cards linking to notes can display that note's frontmatter or Dataview inline fields below the card. Configure which metadata keys to show in board settings under "Linked Page Metadata". Toggle "Field contains markdown" for fields with markdown content.

**Gotcha**: Links and image embeds in frontmatter must be wrapped in quotes to display correctly as linked metadata.

---

## Settings

Settings can be configured **globally** (Settings > Kanban) or **per-board** (board header buttons or "More options" menu). Per-board settings override global ones.

### Key settings

| Setting | Default | Purpose |
|---------|---------|---------|
| Date trigger | `@` | Character that opens date picker |
| Time trigger | `@@` | Character that opens time picker |
| Date format | `YYYY-MM-DD` | Moment.js format for dates |
| Lane width | (plugin default) | Width of board columns |
| Note folder | vault default | Where "New note from card" saves files |
| Note template | none | Template for notes created from cards |
| Prepend/append new cards | append | Where new cards are inserted in a lane |
| Display card checkbox | on | Show/hide checkboxes on cards |
| Max archived cards | -1 | Limit archive size (-1 = unlimited) |

---

## Critical Rules

1. **Always include `kanban-plugin: board` in frontmatter** — without it the file won't be recognized as a board
2. **Use `## ` (H2) for lanes only** — other heading levels are not recognized as lanes
3. **Use `- [ ] ` / `- [x] ` for cards** — plain list items without checkboxes won't render as cards
4. **Keep the settings block at the end** — the `%% kanban:settings ... %%` block must be the last thing in the file
5. **Quote links and embeds in YAML frontmatter** — `"![[image.png]]"` and `"[[note]]"` need quotes in frontmatter fields
6. **WIP limits use parentheses in lane title** — `## Lane Name (N)` where N is the limit number
7. **Boards live in the project's `kanbanboards/` folder at the repository root**, each inside its own `<board_name>/` subfolder — never elsewhere in the project tree
8. **Use TAB-indented lines for multi-line card content** — continuation lines must start with a TAB character
9. **Keep board files small** — for detailed tasks, link to spec files instead of putting everything inline
10. **Always create a context file when creating a new board** — `<board_name>_context.md` alongside the board file, using `example_board_context.md` as the template
11. **Update the context file after completing any task** — add decisions, files, patterns; update task history and current state
12. **Run bootstrap first if `kanbanboards/` is missing** — read and follow `SETUP.md` before any board work in a fresh project

## Common Mistakes to Avoid

- Forgetting the `kanban-plugin: board` frontmatter — file opens as a normal note
- Using `#` or `###` instead of `##` for lane headings
- Using `- ` without `[ ] ` for cards — they won't be interactive
- Placing content after the `%% kanban:settings %%` block
- Unquoted embeds/links in YAML frontmatter fields
- Using spaces instead of TAB for multi-line card indentation
- Putting full implementation details inline on cards instead of linking to spec files
- Forgetting to create a `_context.md` file when creating a new board
- Forgetting to update the context file after completing a task
- Not reading the context file at session start — this is the feature's memory
- Creating a board when `kanbanboards/` doesn't exist yet — follow SETUP.md first

---

## Reference Guides

Full plugin documentation is installed by SETUP.md into `<project_root>/kanbanboards/kanban-guides/`. Consult these for detailed information:

### Overview
- `kanban-guides/Obsidian Kanban Plugin.md` — Plugin overview and links

### How-to Guides
- `kanban-guides/How do I/Create a Kanban board.md` — Creating boards (UI methods)
- `kanban-guides/How do I/Add a date to a card.md` — Date picker usage
- `kanban-guides/How do I/Add a time to a card.md` — Time picker usage
- `kanban-guides/How do I/Add an image to a card.md` — Embedding images and metadata images
- `kanban-guides/How do I/Create notes from cards.md` — Creating linked notes from cards
- `kanban-guides/How do I/Install the plugin.md` — Installation instructions
- `kanban-guides/How do I/Search a Kanban board.md` — Searching with Ctrl/Cmd+F
- `kanban-guides/How do I/Set a WIP Limit.md` — Work-in-progress limits on lanes
- `kanban-guides/How do I/View a Kanban's archive.md` — Viewing archived cards in markdown mode

### FAQs
- `kanban-guides/FAQs/Frontmatter limitations & gotchas.md` — YAML quoting rules for links/images

### Settings Reference
- `kanban-guides/Settings/Local vs. global settings.md` — Per-board vs global settings
- `kanban-guides/Settings/Date trigger.md` — Date picker trigger character
- `kanban-guides/Settings/Time trigger.md` — Time picker trigger character
- `kanban-guides/Settings/Date format.md` — Moment.js date output format
- `kanban-guides/Settings/Date display format.md` — How dates appear on cards
- `kanban-guides/Settings/Time format.md` — Time output format
- `kanban-guides/Settings/Lane width.md` — Column width setting
- `kanban-guides/Settings/Note folder.md` — Folder for notes created from cards
- `kanban-guides/Settings/Note template.md` — Template for new notes from cards
- `kanban-guides/Settings/Linked page metadata.md` — Displaying note metadata on cards
- `kanban-guides/Settings/Display card checkbox.md` — Toggle card checkboxes
- `kanban-guides/Settings/Prepend append new cards.md` — New card insertion position
- `kanban-guides/Settings/Board header buttons.md` — Board toolbar buttons
- `kanban-guides/Settings/Maximum number of archived cards.md` — Archive size limit
- `kanban-guides/Settings/Show relative date.md` — Relative vs absolute dates
- `kanban-guides/Settings/Hide card display dates.md` — Hide date badges on cards
- `kanban-guides/Settings/Hide card display tags.md` — Hide tag badges on cards
- `kanban-guides/Settings/Hide dates in card titles.md` — Hide date text in titles
- `kanban-guides/Settings/Hide tags in card titles.md` — Hide tag text in titles
- `kanban-guides/Settings/Link dates to daily notes.md` — Link date badges to daily notes
- `kanban-guides/Settings/New line trigger.md` — Key for new lines in cards
- `kanban-guides/Settings/Add date and time to archived cards.md` — Timestamp archived cards
- `kanban-guides/Settings/Archive date time format.md` — Archive timestamp format
- `kanban-guides/Settings/Archive date time position.md` — Where archive timestamp appears
- `kanban-guides/Settings/Archive date time separator.md` — Archive timestamp separator
