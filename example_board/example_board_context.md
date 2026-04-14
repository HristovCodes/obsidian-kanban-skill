# Context — Example Board

> Feature memory file. Claude reads this at session start and updates it after each task.
> This serves as living documentation — if you return to this feature in months, this file tells you everything.

## Feature Overview

User authentication system for the example application. Covers database schema, REST API endpoints, and a login page UI. The feature lives primarily in the `database/`, `api/`, and `templates/` directories.

## Architecture & Decisions

- **Decision:** Use bcrypt via `password_hash()` — PHP has native support, industry standard, no extra dependencies
- **Decision:** Server-side sessions stored in `sessions` table (not JWT) — need ability to revoke sessions on logout
- **Decision:** Session tokens are UUIDs (`CHAR(36)`) — avoids sequential IDs, safe for cookies
- **Decision:** Login form uses `fetch()` API with JSON responses — consistent with other AJAX patterns in the app

## Key Files & Locations

| File | Purpose |
|------|---------|
| `database/migrations/001-users.sql` | Users + sessions table schema |
| `api/auth.php` | Login, logout, session validation endpoints |
| `api/users.php` | User CRUD endpoints (list, get, create, update, delete) |
| `templates/login.tpl` | Login form template (Smarty) |
| `class/db_site_functions.inc.php` | DB helper functions (`kanban_` prefix) |

## Implementation Notes

- DB functions go in `class/db_site_functions.inc.php` with `kanban_` prefix and prepared statements via `$db->prepare()`
- AJAX handlers: switch on `$_POST['action']`, return `json_encode(['success' => bool, 'data' => ..., 'error' => ...])`
- Login form stores auth token in httpOnly cookie (not localStorage)
- `sessions.expires_at` defaults to 24 hours from creation
- Index on `sessions.user_id` for join performance
- Rate limiting available at `middleware/rate_limit.php` — reused for login endpoint (5 attempts/minute)

## Task History

| Task | Summary | Status |
|------|---------|--------|
| 1.1 | Created `users` and `sessions` tables with migration, added indexes | Done |
| 1.2 | REST endpoints: 5 user CRUD + login/logout with bcrypt auth | Done |
| 1.3 | Login page with email/password/remember-me, client-side validation | In Progress |

## Current State

- Database migration complete and applied
- All API endpoints functional and tested
- Login page UI built, form validation working
- Remaining on 1.3: error state styling and "remember me" cookie extension logic
- No known blockers
