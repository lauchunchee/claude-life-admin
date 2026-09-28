# claude-life-admin

**A Claude Code starter for everything except code.** A minimal workspace
for running life admin, machine maintenance and personal projects with
[Claude Code](https://code.claude.com). To start: clone it, run `claude`,
then `/setup`. The framework is committed; your life stays gitignored by default.

Markdown files in, useful work out — no build step, no daemon, no plugins.
The same clone is both a publishable repo and your private workspace, and
the two are kept apart.

## Why this one

- **Interview-driven setup.** `/setup` interviews you and *generates* your
  personal layer — a `CLAUDE.local.md` describing who you are, how you work,
  and which tools you use, plus optional domain modules (job search,
  finance, system maintenance). No `YOURNAME` placeholders to hand-edit.
- **Layered safety.** The `.gitignore` is fail-closed: everything is
  personal by default; only whitelisted framework files can be committed.
  `.claude/settings.json` blocks Claude's read and edit tools on `private/`,
  blocks reading `.env` files and force-pushes, and asks before commits and
  pushes. `/visibility` installs a local pre-push guard that refuses any
  push carrying personal paths to a remote you have not confirmed as
  private. Sending, submitting and purchasing are gated by instructions:
  Claude shows the exact action and waits for your yes.
- **OS-agnostic.** Setup detects your OS and shell and records only the
  quirks that matter; permissions cover both Bash and PowerShell.
- **Two visibility modes.** `/visibility` flips between publish-safe
  (framework only) and private-journal (your life is versioned too — git
  history becomes memory), with a history audit guarding the road back to
  public.

## Quick start

1. Get a copy, either way:
   - **Use this template** (green button above) — clean start, your own
     history.
   - **Clone it** — keeps the ability to pull framework updates. Rename the
     template remote to `upstream` so it stays a read-only update channel:

     ```
     git clone https://github.com/<template-owner>/claude-life-admin my-life
     cd my-life
     git remote rename origin upstream
     ```

2. On Windows, also run once: `git config core.longpaths true`
3. Start Claude Code in the folder and run the setup interview (~5 minutes):

   ```
   claude
   /setup
   ```

   Use `/setup`, not `/init` — `/init` is Claude Code's built-in *codebase*
   analyzer and writes the wrong kind of CLAUDE.md for this workspace.

First things to say once setup is done:

- `inbox: renew passport by March` — capture; Claude files it later.
- `process the inbox` — each item filed, one line per item back.
- `what's overdue?` — or `/weekly-review` once a week.

## Privacy model

| Committed (framework) | Gitignored (your life) |
| --- | --- |
| `CLAUDE.md` — generic conventions | `CLAUDE.local.md` — who you are, your tools |
| `.claude/settings.json` — permissions | `.claude/settings.local.json` — your allowlist |
| `.claude/skills/` — setup, weekly-review, visibility | `.claude/rules/` — your generated modules |
| `README.md`, `LICENSE`, `.gitignore`, `.gitattributes` | `inbox.md`, `areas/`, `projects/`, `reference/`, `reviews/`, `archive/` |
|  | `artifacts/`, `tmp/`, `private/` |

The `.gitignore` is a whitelist: `/*` ignores everything, then framework
files are re-included. Anything you create outside the framework is
untracked by default, so publishing doesn't leak your life. Everything in
`.claude/skills/` is published, so keep personal procedures in their area —
the weekly review flags anything extra there.

That table is the default **public** mode. If your repo stays private,
`/visibility private` versions your life content too. `private/`, secrets,
`artifacts/` and `tmp/` stay ignored in both modes.

The `private/` deny rules cover Claude's file tools and shell commands
that name the path, not recursive searches or scripts — keep truly
sensitive documents outside the repo if that matters to you.

## How it works day to day

- **Capture** anything into `inbox.md` — type it in the file, or tell
  Claude `inbox: <thing>`.
- **Areas** (`areas/<name>/`, ongoing) and **projects**
  (`projects/<name>/`, finite) appear on first use or when confirmed in
  `/setup`, never pre-built. Each matter is one markdown file with a
  `status` in its frontmatter.
- **`/weekly-review`** surfaces what is overdue, due soon, waiting on
  someone or stuck, clears the inbox and open questions, proposes archive
  moves, and monthly checks the instruction files for rot. It writes one
  dated file in `reviews/`; you answer in one line: `Q1 yes, C all`.
- **Modules** add domain conventions on demand — `/setup job-search`,
  `/setup system-maintenance`, `/setup finance` — and load only when
  Claude reads files in the matching folder. `/setup area <name>` starts a
  new area.

The full conventions (folder layout, status values, file naming, safety
rules) are in [CLAUDE.md](CLAUDE.md).

## Extending

- Hard constraints for one area → `.claude/rules/<name>.md` with `paths:`
  frontmatter, so it loads only when relevant.
- A workflow for one area or project → that folder's `CLAUDE.md`; its
  subagent prompts → that folder's `agents/`.
- Only generic, workspace-wide procedures → `.claude/skills/<name>/SKILL.md`.
- Corrected Claude twice on the same thing? It proposes a one-line edit to
  CLAUDE.md — that feedback loop is built in.

## Philosophy

Systems like this tend to die of overfit: structure built for an
aspirational self, rotting instructions, fifty skills nobody runs. So this
template ships almost nothing — three skills, one config, one short
CLAUDE.md — and generates specifics for *you* on demand. Wherever a rule
can be enforced (gitignore, permissions, the pre-push guard) it is;
instructions cover the rest.

This template is a best-effort personal artifact, not a supported product:
issues and ideas are welcome, fast responses are not guaranteed.

## License

MIT — see [LICENSE](LICENSE).

Not affiliated with Anthropic. Claude is a trademark of Anthropic, PBC.
