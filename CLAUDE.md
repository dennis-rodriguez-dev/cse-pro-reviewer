# CSE Reviewer

Practice web app for the Philippine CSC Career Service Professional Exam: individual tests, full mock exam, scoring, review, leaderboard (IGN only). Personal project first, built so others can use it later.
Stack: Laravel 13, Livewire 4 + Alpine (user app), Filament 5 (admin only), Tailwind 4, Pest 5, Pint, SQLite locally. Exact versions: specs/plan.md §1.3. Windows 11 + Herd: run PHP commands in PowerShell, not Git Bash.
Exam: 170 items, 3h10m, pass = general rating >= 80.00. The per-subtest split is NOT official: it lives in DB settings (default 50/40/40/40). Never hardcode it.

## User Preferences

The project owner is Dennis.

Always address the project owner as "Dennis" in:
- status reports
- completion reports
- handoff files
- progress updates
- review summaries
- recommendations
- decision requests

Begin reports and completion summaries with "Dennis," whenever natural.

This preference applies to all future sessions unless explicitly overridden.

## Current State (post-MVP, live data)
- MVP done: M1-M7 merged into `main` and tagged `m1-done` ... `m7-done`.
- The book import is complete (2026-11-16). `database/database.sqlite` is the **source of truth** for questions, not the files in `content/staged/`.
- The database is live, not disposable. It holds published content, the audit log, real user accounts and real attempt data.
- Counts and open items: specs/HANDOFF.md.

## Safety rules (live data)
- Never run `php artisan migrate:fresh` or `php artisan migrate:fresh --seed`, `db:wipe`, or any command that drops or truncates tables.
- Never re-import book content, and never rebuild content from `content/staged/` files.
- Never run `content/staged/work/apply-editorial-review.php` or the other `work/build-*.php` builders.
- Ask Dennis first before: `migrate:fresh` / `migrate:fresh --seed` / `db:wipe` (forbidden above unless he explicitly overrides it), running or adding migrations, any schema change, any re-import, and bulk destructive operations (bulk delete, truncate, mass updates).
- Routine admin actions in `/admin` (publish, archive, review-state changes) need no approval. Read-only queries are fine.
- Back up the database before any migration, data fix or risky change, and ask first (see Commands).
- Tests are safe: they use an in-memory SQLite database (phpunit.xml).

## Where things live
- specs/ : constitution, spec, clarifications, plan, tasks (tick tasks when done)
- specs/decisions.md : decisions log. Check it before any design choice.
- specs/HANDOFF.md : app and repo state, where the last session stopped (overwrite each session, max 20 lines). Read it first.
- content/staged/HANDOFF-CURRENT.md : question-bank state and content history (gitignored, local only). Read it before any content work.
- SUGGESTIONS.md : parked ideas. Never implement unprompted.
- .claude/rules/ : area rules (testing, ui, data), loaded only when matching files are touched
- content/README.md : how source material is prepared and imported (used in M3 and for the book import; only for new content the owner asks for)

## Commands
- One area: `php artisan test --compact --filter=<Name>` (or pass a test path)
- Full suite (risky changes and before a release): `php artisan test --compact`
- Format: `vendor/bin/pint --dirty`
- Back up the DB (PowerShell): `New-Item -ItemType Directory -Force C:\ai\backups\<yyyy-mm-dd>; Copy-Item database\database.sqlite C:\ai\backups\<yyyy-mm-dd>\database.sqlite`
- Create admin: `php artisan cse:make-admin`
- New content only, on the owner's request: `php artisan cse:import content/staged/<file>.json --dry-run` (dropping `--dry-run` creates a pending batch in the live DB)
- Finalize expired timed attempts locally: `php artisan schedule:work`
- Portability run: set DB_* in the PowerShell process env only (never .env), then the full suite. Last run: specs/decisions.md (M7-06)
- PowerShell eats `^` and `|` in arguments (php.bat): pass test paths, not `--filter='a|b'`.

## Post-MVP workflow
Typical work now: content QA, duplicate review, explanation audits, testing, documentation, and Part 4 (Subprofessional) planning.
- Content QA, duplicate decisions and explanation audits: report and recommend; the owner makes the changes in `/admin`.
- New features or larger changes (for example Part 4 support) need a spec story and a task in specs/ first, and the owner's go-ahead.
- Record owner decisions in specs/decisions.md. Update content/staged/HANDOFF-CURRENT.md after content changes and specs/HANDOFF.md at the end of each session.

## Workflow rules
1. Tests first for risky logic (scoring, timer, answer saving, import validation, PIN/login/session checks): write the failing test, run only it, show the failure line, then implement. For simple screens and plain admin resources, write the tests with the code and run them once.
2. Per task: run tests for the touched area plus `vendor/bin/pint --dirty`, then one commit.
3. Full suite before risky changes (migrations, shared base classes) and before a merge or release.
4. Before any commit, show the files changed, a summary and the proposed commit message, and wait for approval.
5. Decision not in specs: stop, give 2-3 options with a recommendation, wait.
6. Never without asking: push, merge, tag, rebase, reset, force, branch deletion, deleting files, installing or removing packages, editing .env, changing committed migrations, and the database operations listed under Safety rules.
7. Output: task summary of 5 lines or less (what changed, test status, commit hash). No full logs unless I ask. After each task group (or on pause), say in one line how I can check it by hand (a page to open, a command to run).
8. Pause: when I say 'pause', finish the current task, commit with tests green (after approval), overwrite specs/HANDOFF.md, commit it, and stop. Don't merge, tag or push.

## Project rules (every task)
- Every feature traces to a story in specs/spec.md and a task in specs/tasks.md.
- Server is authoritative for time, answers and scores. Never trust the client.
- Never fabricate a correct answer or explanation. Missing means flag `needs_review`. Exception: the editorial repair policy for book content (specs/decisions.md, 2026-11-14), applied only with the owner's approval.
- Imports are never published: they land as `draft` or `needs_review`, in a private source by default.
- Code must stay portable to MySQL/PostgreSQL (no SQLite-only SQL).
- UI copy is English, wrapped in `__()`.
- Keep it simple. Don't add abstractions, options or features the task doesn't need. Comment only non-obvious code.
- Stay inside the task you were given. If you spot something that could be better, add it to SUGGESTIONS.md and keep going. Don't change it.

## Git
- Repo: https://github.com/dennis-rodriguez-dev/cse-pro-reviewer (private, `origin`). Default branch `main`. Never rewrite history.
- Commit format: `<type>(<task-id>): <summary>`, e.g. `feat(M3-02): add import schema validator`; post-MVP work without a task id uses `<type>: <summary>`. Types: feat, fix, test, docs, chore, refactor.
- M1-M7 used one branch per milestone, merged `--no-ff` into main and tagged `m<n>-done` (all done). The branch model for post-MVP work isn't decided yet: ask before starting feature work.
- Never commit: .env, vendor/, node_modules/, database/*.sqlite, storage logs, content/inbox/, content/staged/, uploaded media, secrets, real user data.
- CLAUDE.md is tracked; changes to it are committed like any other file.

# Compact instructions
When compacting, keep: current task id, files changed, failing test names, next step. Drop passing test output and long logs.
