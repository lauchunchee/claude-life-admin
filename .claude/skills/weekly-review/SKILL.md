---
name: weekly-review
description: Weekly sweep of the whole workspace — overdue and upcoming deadlines, waiting items due a follow-up, stuck work, unanswered Open questions, inbox backlog, archive candidates, and (monthly) drift in the instruction files. Writes one dated review file with a batched question list, then applies only approved changes. Use when the user says "weekly review", "what's stale", "what's overdue", "what did I drop", or asks to plan the week.
---

Steps 0–3 are read-only; step 4 writes only the review file; nothing else
changes before the approval in step 5. Scan only `inbox.md`, `areas/`,
`projects/`, `reference/` and `reviews/` — never `private/`, `archive/`,
`tmp/` or `artifacts/`. Skip any step whose input does not exist. Use the
Read, Grep and Glob tools rather than `find`/`ls` — they, `git status`
and `git log` run without permission prompts.

A quick question ("what's overdue?", "what's stale?") → run steps 0–2,
answer in chat, offer the full review, write nothing.

## 0. Anchor
- Last review = newest `reviews/*.md`. None (first run) → window is the
  last 7 days; say so; step 3 runs.
- Otherwise window = its date → today. Read its `## Carried forward`: each
  line re-enters this review as a Q or C with its count +1.
- Private mode (`.gitignore` line 1) → `git log --since=<window start>
  --stat` adds what changed.

## 1. Get clear
- Inbox: each `inbox.md` item → one C with a proposed destination.
- Open questions: Grep `\*\*Open \(` in `inbox.md`, `areas/`, `projects/`
  and `reference/` → one Q each, citing `path:line`. Drop any already
  answered elsewhere.
- A Q or C reaching count 2 is re-asked as a forced choice with a default
  ("no reply → I will X"). At count 3, apply that default as an approved
  change — unless it is outward-facing (CLAUDE.md Rules); then ask again.

## 2. Get current (frontmatter only)
One Grep over `areas/ projects/ reference/` (`*.md`, content mode, line
numbers) for `^(status|due|check|updated|waiting_on):`; ignore matches
below a file's frontmatter. Pipeline values declared in a
`.claude/rules/` module count as the core status they map to.
- Overdue: `due` < today, status not `done`.
- Due soon: today ≤ `due` ≤ today + 14 days.
- Follow up: status `waiting` or `parked`, and `check` ≤ today or no
  `check`. `waiting` → C with a drafted chase message to `waiting_on`;
  `parked` → C to promote, extend `check`, or drop.
- Stuck: status `active`, `updated` more than 14 days ago. A living doc
  marked `active` → C to relabel it `reference`.
- Archive candidates: a project whose task files are all `done`, or whose
  newest `updated` is more than 30 days ago (unless the project is
  `parked`).
- Wins: files whose `updated` falls in the window (`done` first).
- Area checks: run the read-only checks an area declares under
  `## Weekly checks` in its README.md, CLAUDE.md or rules file. Never
  invent checks.
- External task system connected (CLAUDE.local.md) → list its overdue and
  this-week tasks; flag divergence from repo state.

## 3. Hygiene (conditional)
Run on the first review ever or of the calendar month, or when
`git status`/`git log` shows `CLAUDE.md` or `.claude/` changed in the
window.
- Every path cited in CLAUDE.md, CLAUDE.local.md, `.claude/rules/` and
  nested CLAUDE.md files exists; every `paths:` glob matches a real folder;
  the MCP allowlist in `.claude/settings.local.json` matches the tool
  routing; `.claude/skills/` holds only setup, visibility and
  weekly-review (anything else is published in public mode — propose
  moving it into its area).
- Frontmatter breaking CLAUDE.md's State rules: status outside the core
  set and declared pipelines; `#` comments; `due`/`check` on `done`;
  `waiting`/`parked` without `waiting_on`; missing `updated`.
- Facts restated outside their owner file; any status, date or verdict in
  CLAUDE.local.md.
Each finding → one C.

## 4. Write `reviews/YYYY-MM-DD-weekly-review.md`
Create `reviews/` if needed; a rerun the same day overwrites the file. Use
this template exactly; an empty section gets `- none`:

```
# Weekly review YYYY-MM-DD
Window: YYYY-MM-DD → YYYY-MM-DD (first review: last 7 days)

## Overdue
- path — due YYYY-MM-DD — one-line state
## Due soon
## Follow up
## Stuck
## Wins
## Focus next week
- up to 3, each citing a path
## Questions
- Q1 (count) question — path:line
## Proposed changes
- C1 (count) path: the exact edit
## Carried forward
## Approved
```

Wins: up to 3, each citing a path. (count) is 1 for new items. Chase
messages are drafts inside their C — sending one is an outward action.

## 5. Present, then apply
- In chat show only: Focus next week, the Q list and the C list, plus a
  link to the review file. Ask once: "Anything on your mind that isn't in
  the workspace?" — answers are appended to `inbox.md`.
- Stop and wait for one reply such as `Q1 yes, Q2 skip, C1 C3`, `C all`
  or `none`.
- Apply only what was approved, each in its owner file:
  - Answered Q → record the answer in the owner file, delete its
    `**Open` line.
  - Set `updated:` to today only when the edit changes the matter itself
    (status, a date, a decision, an outcome) — not for moves, links or
    wording. Moving to `done` deletes `due` and `check`.
  - Inbox filing and archiving follow CLAUDE.md (Layout).
- Fill the review file: `## Approved` lists each applied or skipped item
  (`C1 applied`, `Q2 skipped`); every unmentioned Q/C goes to
  `## Carried forward` unchanged, keeping its count.
