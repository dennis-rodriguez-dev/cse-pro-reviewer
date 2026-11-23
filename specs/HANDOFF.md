# Handoff (overwrite each session, max 20 lines)
Updated: 2026-11-23 (post-MVP: content QA and docs sync)
- Status: MVP done. M1-M7 merged into `main`, tags `m1-done`..`m7-done` on origin. Book import complete (2026-11-16).
- Repo: `origin` = https://github.com/dennis-rodriguez-dev/cse-pro-reviewer (account renamed 2026-11; checked with `git ls-remote`).
- Tests: full suite on SQLite 788/788 (re-run 2026-11-23). MySQL 8.4 788/788 (M7-06). PostgreSQL never run: do it before any deploy that targets it.
- LIVE DATA: `database/database.sqlite` is the source of truth. It holds published content, the audit log, real user accounts and real attempt data. Never migrate:fresh, re-import, or run the content/staged builders (CLAUDE.md, Safety rules).
- BACKUP: the newest DB backup found is from 2026-11-14, before the import (`C:\ai\db-backups`). Back up before any DB change.
- Question bank (2026-11-23): 791 imported, 784 published, 4 needs_review (#150, #192, #521, #763), 3 archived (#394, #448, #566). Source private. Part 4 (45 Clerical items) not imported.
- Content details and history: content/staged/HANDOFF-CURRENT.md (gitignored, this machine only).
- Open items:
  1. Back up the DB, content/staged/ and content/inbox/ off this disk.
  2. Decide the open duplicate pairs #380/#393, #52/#313, #66/#209 (both items of each pair published).
  3. Decide the 4 needs_review items: rewrite under the editorial policy, or archive.
  4. Spot-audit the AI-written explanations in Parts 1-3; run the specs/FINAL-CHECK.md manual pass.
  5. Part 4 / Subprofessional: needs a spec before any import. PostgreSQL run still open.
- Next step: item 1, then items 2-3 in /admin. App gaps found in QA are listed in HANDOFF-CURRENT.md (section 9).
- Don't edit text files with PowerShell Set-Content (BOM/UTF-8); use Edit. Bash has no php: run php/pint in PowerShell.
