---
paths:
  - "areas/{{area}}/**"
---

<!--
Module template → .claude/rules/system-maintenance.md. Fill {{area}}
(default "system") and {{shell_specifics}} from the detected OS and shell.
-->

# System-maintenance conventions

- The findings report of CLAUDE.md's two-phase rule is the approval
  interface: `areas/{{area}}/reports/YYYY-MM-DD-<topic>.md` with a
  findings table (item, impact, risk, recommendation) and an empty
  `## Approved actions` section the user fills in. Execute only the items
  listed there.
- Runbooks: `areas/{{area}}/runbooks/<name>.md` — reusable read-only
  discovery checklists (pending updates, disk usage, disk health, failed
  services, startup items). Keep changes out of runbooks so an audit can
  run them safely.
- Log every executed action in `areas/{{area}}/maintenance-log.md` in the
  same session: date, what, why, result; appended in date order.
- Touch only on explicit request: system configuration stores, drivers,
  disk encryption, boot or restore settings, other users' folders.
- {{shell_specifics — e.g. "Discovery uses read-only PowerShell Get-*
  cmdlets", or the package-manager and service commands for macOS/Linux}}
