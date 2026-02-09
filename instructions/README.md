# AI Developer Instructions — Second Memory repo

Purpose
- Automate and maintain the personal "Second Memory" repository.
- Follow the user's mental model: quick lookup first, simple task inbox, per-folder indexes, reminders with Telegram + Google Calendar links.
- Always produce a PR for changes (do not push directly to main).

Workspace
- Root: C:\Users\vikso\Desktop\AI
- Repo structure highlights:
  - README.md — dashboard (show Tasks at top, Due Today section)
  - inbox.md — single append-only quick notes
  - tasks.md — single file tasks list (numbered in backlog)
  - reminders/README.md — scheduled reminders table
  - learning/, projects/, snippets/sql/, references/, assets/, templates/, instructions/

Basic rules
1. Use natural file titles for content files (spaces allowed).
2. Use templates in /templates for learning/project/reference files.
3. Tasks & Inbox are ephemeral: keep single files (tasks.md, inbox.md). Other content uses one file per topic.
4. Always include topic/status hashtags (e.g., `#backlog`, `#in-progress`, `#Personal`, `#JM`). Do not add YAML frontmatter.
5. Number backlog tasks in tasks.md and in README tasks list. When adding/removing tasks, renumber sequentially.

How to add content (workflow)
- Create a feature branch: git checkout -b feat/<short-desc>
- Make edits or add files under appropriate folder.
- Stage, commit with clear message (e.g., "task: add Pay Airbnb #backlog #Personal").
- Push branch and open a PR targeting `main`. Use the PR description to explain changes.
- Wait for user review/merge.

Task rules
- Task line format (in backlog): `- [ ] N. Title #backlog #Tag1 #Tag2`
- Keep the same line in tasks.md and mirrored section at top of README.md.
- Do not change status automatically. User will update tags manually. To promote a task:
  - Change tag `#backlog` → `#in-progress` and move the line to the “In Progress” section in both tasks.md and README.md.
  - When complete, user may check the checkbox or tag `#done`. Move completed tasks to Done section and suggest archival after 2 weeks unless `#long-term`.

Inbox rules
- Append quick notes at top of inbox.md, each line is a separate item. Use hashtags for topics.
- Periodically (user decides) sort inbox into learning, projects, tasks, or delete.

Reminder rules
- Default duration: 30 minutes (unless specified otherwise)
- Format in reminders/README.md as a table row:
  | # | Reminder | Date | Time | Status | Calendar | Notes |
- When user requests a reminder, create a row with a Google Calendar "Add" link:
  `https://calendar.google.com/calendar/render?action=TEMPLATE&text=<URL-ENCODED>&dates=<START>/<END>&details=From+Second+Memory`
- Use UTC conversion for the URL (Z suffix). Example: 2026-02-09 09:00 BRT → 20260209T120000Z
- END time = START time + 30 minutes (default)
- For notifications: send via Telegram from the scheduled workflow.

Reminders workflow behavior
- Daily scheduled workflow runs at repo cron (9:30 BRT).
- The workflow sends every row under "## Active Reminders" via Telegram (PowerShell Invoke-RestMethod).
- After sending, do NOT auto-delete rows. Move items to Completed only on user's command or after user-confirmed automation.
- If user asks to only send today's reminders, filter rows by Date column matching `yyyy-MM-dd`.

Telegram & secrets
- Expect these repository secrets:
  - TELEGRAM_BOT_TOKEN
  - TELEGRAM_CHAT_ID
  - EMAIL_USERNAME, EMAIL_PASSWORD, EMAIL_TO (if email feature used)
- Do not print secrets in logs. Handle failures gracefully and log minimal debug info (which lines were found).

README / Dashboard updates
- Always show Tasks at top of README.
- Keep a Due Today section — either auto-updated by workflow (if user enabled commits) or updated via PR.
- When making README edits, mirror changes into the canonical file (tasks.md or reminders/README.md) and create a PR describing the sync.

Files & templates
- Templates live in /templates. Use placeholders exactly as in templates.
- Snippets: store runnable SQL in snippets/sql/*.sql with a top comment block describing purpose and tags.
- Assets: place binaries under assets/<category>/ and add descriptive entry in assets/README.md.

Numbering and renumbering
- When adding a new backlog task, append with next integer.
- When deleting a task (user asks "Delete N"): remove the numbered line in both tasks.md and README.md and renumber all backlog items to be consecutive.
- When moving tasks between sections, preserve or update numbers so backlog remains consecutive. In-progress/blocked/done do not need persistent numbering but keeping numbers helps references — choose consistent approach and document it in PR.

Commit message convention
- Prefix: `task:`, `reminder:`, `learning:`, `snippet:`, `refactor:`, `workflow:`
- Short summary and optional tags. Example: `reminder: add Talk with Fabi 2026-02-09 09:00 BRT`

Quality checks before PR
- Validate markdown tables are well-formed.
- Ensure reminders dates are valid ISO (yyyy-MM-dd).
- Ensure calendar URLs use URL-encoded text and UTC timestamps.
- Verify snippets have header comments and correct file extension.

If uncertain
- Ask the user a clarifying question (small batch) before making structural changes.
- Never assume permission to push to main — create a PR.

Examples
- Add backlog task:
  1. Edit tasks.md: `- [ ] 5. New Task #backlog #Tag`
  2. Mirror in README tasks section.
  3. Commit, push branch, open PR.

- Add reminder:
  1. Append row to reminders/README.md with date/time and calendar link.
  2. Update README Due Today section via PR (or auto if user authorized).
  3. Do not remove or move rows without user instruction.

End.
