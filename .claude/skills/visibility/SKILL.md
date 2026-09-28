---
name: visibility
description: Switches the workspace between public mode (only framework files tracked — safe to publish) and private mode (life content versioned too — git history becomes memory). Use when the user wants to share or publish the repo, make it private, commit more of their content, or check whether the repo is safe to publish.
argument-hint: "[public|private|status]"
---

# Visibility — govern what git is allowed to see

The active `.gitignore` defines the mode; its first line names it. Each
mode's `.gitignore` is a copy of `templates/gitignore-<mode>`.

- **public** (default): fail-closed whitelist — only framework files can be
  committed. Safe to attach a public remote.
- **private**: everything except the never-tracked list is versioned —
  `areas/`, `projects/`, `reference/`, `archive/`, `reviews/`, `inbox.md`,
  `CLAUDE.local.md`, `.claude/rules/`, nested `CLAUDE.md` files — so git
  history becomes memory. `private/`, secrets, `artifacts/`, `tmp/`, and
  `.claude/settings.local.json` stay ignored in both modes.

`$ARGUMENTS` is `public`, `private`, or `status`. With no argument, report
status and ask which mode the user wants.

## status

Read-only: write nothing, change nothing. Report the current mode (first
line of `.gitignore`), `git remote -v`, whether the pre-push guard is
installed, and the **History audit** counts. Recommend nothing unless asked.

## Switching to private

1. Check remotes. A remote named `upstream` that is the template's source
   is a read-only update channel, not a leak: say so and continue. If any
   other remote could be public (for GitHub, verify with
   `gh repo view <remote-url> --json visibility` when available), stop
   here — private mode must never feed a public remote. Continue only after the
   user confirms it is private, removes it, or renames the template remote
   to `upstream`.
2. Copy `templates/gitignore-private` over `.gitignore`.
3. Install the pre-push guard if it is missing.
4. Show what is now trackable (`git status --short`) and offer to commit
   the personal layer. Stage nothing until the user says yes.
5. If no remote exists, suggest — do not create unasked — a private GitHub
   remote as an off-machine backup. Once the user confirms it is private,
   run `git config lifeadmin.privateRemote <name>` so the guard lets pushes
   to it through.

## Switching to public

1. Preview: list tracked files the public template would ignore —
   `git ls-files -ci --exclude-from=.claude/skills/visibility/templates/gitignore-public`.
   Tell the user these leave the index but stay on disk, and that their
   history is untouched (step 4).
2. Copy `templates/gitignore-public` over `.gitignore`.
3. After the user confirms the step-1 list, untrack exactly those files:
   `git ls-files -ci --exclude-standard -z | git --literal-pathspecs rm --cached --quiet --pathspec-from-file=- --pathspec-file-nul`.
   Run it in Bash (Git Bash on Windows) or PowerShell 7.4+; Windows
   PowerShell 5.1 re-encodes piped native output and corrupts the
   NUL-separated list. Show `git status --short`; commit only after a second yes. Never use
   `git rm -r --cached .` + `git add .` — it stages unrelated edits.
4. Run the **History audit**. Write its full path list to
   `tmp/history-audit.txt`; show the count and the first 10 paths.
5. Report "safe to publish" only when both audit checks come back clean.
   Otherwise say plainly:
   - This branch's history still contains personal content; untracking
     does not remove it. Never push this branch anywhere public.
   - The safe publish path is an orphan branch holding only framework
     files (`git checkout --orphan public` → commit framework → publish
     that), while the full-history branch stays private. Offer it; never
     rewrite history unasked.

## History audit

1. Paths: every path ever added on any branch that fails the `ok`
   pattern (set `ok` to its value in **Pre-push guard** first — unset, the
   grep matches nothing and the audit falsely reads clean) —
   `git log --all --no-renames --diff-filter=A --name-only --format= | sort -u | grep -Ev "$ok"`
   (`--no-renames` so a rename's new path is counted).
2. Content: for each of the user's name, email and phone in
   CLAUDE.local.md, `git log --all -i -S"<term>" --oneline` — personal text
   inside framework files counts.

## Pre-push guard

A local hook at `git rev-parse --git-path hooks/pre-push` (never committed)
refuses any push carrying non-framework paths, unless the remote is the one
named in `git config lifeadmin.privateRemote`. Install it in either mode
when missing, and make it executable. `ok` mirrors the public template's
whitelist; change both together.

```sh
#!/bin/sh
# claude-life-admin: block personal content from reaching a public remote.
remote="$1"
[ "$(git config --get lifeadmin.privateRemote)" = "$remote" ] && exit 0
ok='^(\.gitignore|\.gitattributes|CLAUDE\.md|README\.md|LICENSE|\.claude/settings\.json|\.claude/skills/.+)$'
while read -r lref lsha rref rsha; do
  case "$lsha" in *[!0]*) ;; *) continue ;; esac
  bad=$(git log --format= --name-only --no-renames "$lsha" --not --remotes="$remote" \
    | sed '/^$/d' | sort -u | grep -Ev "$ok")
  if [ -n "$bad" ]; then
    printf 'pre-push: non-framework paths bound for %s:\n%s\n' "$remote" "$bad" >&2
    exit 1
  fi
done
exit 0
```

## Rules

- Never push, change a remote's visibility, untrack files, or rewrite
  history without explicit confirmation — show each irreversible step
  first.
- Line 1 of `.gitignore` names the mode. If the file differs from that
  mode's template, report it as drifted: show the diff and ask before
  overwriting — hand edits can weaken the never-tracked list. No
  recognizable line 1 means the mode is unknown; same diff-and-ask.
