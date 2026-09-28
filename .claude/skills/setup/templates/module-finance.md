---
paths:
  - "areas/{{area}}/**"
---

<!--
Module template → .claude/rules/finance.md. Fill {{area}} (default
"finance") and {{currency_and_locale}}.
-->

# Finance conventions

- Statements, tax documents and anything else carrying account or card
  numbers go to `private/`. Tracked files hold categories, dates,
  merchants and amounts only.
- `areas/{{area}}/subscriptions.md` — one table: service, cost, billing
  cycle, renewal date, cancel-by date, last reviewed.
- Answer money questions from tracked aggregates. If an answer needs a
  document in `private/`, ask the user for the numbers.
- {{currency_and_locale — e.g. "Amounts in EUR, dates YYYY-MM-DD"}}

## Weekly checks

- `areas/{{area}}/subscriptions.md`: renewals and cancel-by dates in the
  next 14 days.
