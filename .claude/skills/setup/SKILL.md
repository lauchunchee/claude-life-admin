---
name: setup
description: Interviews the user about their life-admin workflow and generates the personal layer — CLAUDE.local.md, inbox.md, and optional domain modules. Also adds a module or a new area later. Use when the user asks to set up, initialize, personalize, or onboard this workspace, add a module ("/setup job-search"), start a new area ("/setup area health"), or accepts the offer to set up when CLAUDE.local.md is missing.
argument-hint: "[job-search | system-maintenance | finance | area <name>]"
---

# Setup — generate the personal layer

Generates the personal layer: `CLAUDE.local.md`, `inbox.md`, area and
project `README.md` files, and optional modules in `.claude/rules/`. Never
edit committed framework files (CLAUDE.md, settings.json, skills).

Placeholders in `templates/`: `{{name}}` is a value to fill;
`{{name — guidance}}` is filled per the guidance, which is never copied.
Template `<!-- -->` comments are for you; leave them out of generated
files.

## 1. Route

- `$ARGUMENTS` is `job-search`, `system-maintenance` or `finance` → go to
  **Modules**.
- `$ARGUMENTS` is `area <name>` → go to **New area**.
- Anything else non-empty → list these four forms and ask which was meant.
- Empty → continue below.

Modules and new areas need `CLAUDE.local.md`; if it is missing, say so and
run the full setup instead (modules are offered in its interview).

If `CLAUDE.local.md` exists, never overwrite it. Ask: review and improve
it, add a module, or start fresh — start fresh only on explicit
confirmation, and only after moving the old file to
`archive/YYYY-MM-DD-claude-local.md` (renamed so Claude Code never loads
it as live instructions). Otherwise, before interviewing, tell the user
in three lines: what will be generated; where it lands in git (public mode,
`.gitignore` line 1: gitignored, stays on this machine; private mode:
versioned in this repo); about five minutes.

## 2. Interview

Detect, never ask: name (`git config --get user.name`), OS, shell and its quirks
(e.g. Windows PowerShell 5.1: no `&&`), and connected MCP servers — they
go straight into the draft. Ask the rest with AskUserQuestion, up to four
questions per call, every option with a one-line description. Mark no
option "(Recommended)": these are questions about the user's life. The
tool adds "Other" itself.

Call 1:
1. **Areas** (multiSelect): ongoing parts of life with no end date —
   life admin/chores, machine maintenance, finance, job search.
2. **Projects:** current finite efforts with an end state (a move, a
   renovation, a dispute), or none.
3. **Working style:** how proactive Claude should be (chief of staff who
   pushes back vs. does what is asked).
4. **Never do:** anything Claude must never do, or nothing.

Call 2, only if a chosen area has a module in **Modules**: which modules
to generate.

Propose in the draft, don't ask: kebab-case folder names, and one tool
routing line per connected MCP server or CLI tool the user named. Write
no routing for tools that are not connected.

## 3. Draft and confirm

1. Draft `CLAUDE.local.md` from `templates/claude-local.md`
   (filled example: `examples/sample-claude-local.md`). Aim for about 40
   lines of behavior-changing facts. Areas and projects get one pointer
   line each — status, dates and verdicts belong in the folder's files.
2. Draft each opted-in module (see **Modules**).
3. Show every draft in full, plus the list of files to be created.
   **Stop until the user approves**; apply edits and re-show if asked.

## 4. Write

1. Write the approved `CLAUDE.local.md` and modules.
2. Write `inbox.md`: a one-line capture header plus any actionable items
   that surfaced in the interview.
3. Create `README.md` for each area and project (as in **New area**; a
   project README's frontmatter carries `status: active`) — pointer lines
   and `paths:` globs must point at real folders.
4. If tool routing was confirmed, offer to add those MCP servers'
   read-only tools to `allow` in `.claude/settings.local.json`; write
   tools stay on ask. Write only on a yes.
5. In public mode, run `git check-ignore` on every file written; if any
   is not ignored, stop and warn before anything else. In private mode,
   skip this — the files are versioned by design.
6. Summarize in at most 5 lines what was written where. Offer to process
   the inbox items now, and show three things to try:
   `inbox: renew passport by March`, `what's overdue?`, `/weekly-review`.

## Modules

| Argument | Template | Default area | Covers |
| --- | --- | --- | --- |
| `job-search` | `templates/module-job-search.md` | `job-search` | honest resumes, application pipeline, fit gate, ATS format |
| `system-maintenance` | `templates/module-system-maintenance.md` | `system` | audit reports, runbooks, maintenance log |
| `finance` | `templates/module-finance.md` | `finance` | privacy tiering, subscription inventory |

1. Confirm the area folder (default above, or the one in CLAUDE.local.md).
   Standalone run: if it has no pointer line yet, run **New area** first.
2. Fill the template: every `{{area}}` (in `paths:` and the body) becomes
   the folder name; other placeholders come from the interview or a
   direct question.
3. Check: no `{{` remains, and the `paths:` glob matches the folder in
   CLAUDE.local.md — a wrong glob never loads, silently.
4. Standalone run: show the draft; on approval write
   `.claude/rules/<argument>.md`. In a full setup, sections 3–4 cover this.

## New area

For `/setup area <name>`:
1. Confirm the kebab-case name and a one-line purpose.
2. Show the pointer line for CLAUDE.local.md and the `README.md` draft
   (purpose, what belongs here, links); on approval write both.
3. Offer, never pre-build: a folder `CLAUDE.md` for a multi-step workflow
   (the pointer line then ends "Workflow: read `areas/<name>/CLAUDE.md`
   first"), `agents/` for its subagent prompts, and a module in
   `.claude/rules/` for hard constraints.
