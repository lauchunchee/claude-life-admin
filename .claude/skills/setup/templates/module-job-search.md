---
paths:
  - "areas/{{area}}/**"
---

<!--
Module template → .claude/rules/job-search.md. Fill {{area}} (default
"job-search"). The fit threshold lives in profile/preferences.md, not here.
-->

# Job-search conventions

- Never invent experience, skills or metrics in job documents — every
  claim traces to `areas/{{area}}/profile/master-resume.md`.
- NEVER automate logged-in LinkedIn (scraping, connecting, Easy Apply) —
  ban risk. Job discovery: public ATS career pages (e.g. Greenhouse,
  Lever, Ashby) first, web search second, LinkedIn last and by hand.
- Tailored resumes are generated from `profile/master-resume.md`, one-way.
  Leave master untouched while tailoring; fold new facts into it as a
  separate, deliberate step.
- Fit gate before tailoring: score the posting 1–5 against
  `profile/preferences.md` and record the score in the application's
  `notes.md`. Below the threshold in preferences.md (ask once if unset),
  stop after scoring.
- Application folders: `applications/YYYY-MM-DD-company-role/` with
  `jd.md` (verbatim snapshot, URL and retrieval date — postings vanish),
  `resume.md`, and `notes.md` whose frontmatter is `company, role, url,
  status, applied, updated, next_action`, plus `waiting_on` and `check`
  while waiting.
- `status` pipeline in `notes.md`, mapped to the core values:
  researching, tailoring → `active`; applied, screen, interview-N →
  `waiting`; offer, rejected, ghosted → `done`.
- Resume format: single column, standard section headers, no tables, text
  boxes or images — a broken text layer is the most common ATS failure.
- Cover letters ≤250 words, problem–solution–impact, built on a real
  anecdote the user supplies. Banned: "leveraged", "spearheaded",
  "I am excited to", motivational-poster tone.
- Generated PDF/DOCX go to `artifacts/`.
- From about 5 applications on, keep `applications-index.md`: a table
  regenerated from the `notes.md` frontmatter, never hand-edited.
