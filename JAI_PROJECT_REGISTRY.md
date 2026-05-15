# JAI Project Registry

Local registry of project repositories this machine audits via the
`JAI fleet sync audit` command (see [JAI_FLEET_SYNC_AUDIT.md](./JAI_FLEET_SYNC_AUDIT.md)).

**Scope:** machine-local state, not versioned spec content. If the
global standard at `~/.claude/jai-standard/` is ever promoted to a git
repository, this file should be excluded from version control because
its contents are operator-specific.

**Format:** one row per project. Active projects are audited every
fleet-sync run; candidates are listed for visibility but not audited
until promoted to Active.

## Active projects

| Name | Path | Onboarded | Declared JAI version | Last audited |
|---|---|---|---|---|
| trend-scanner-v1 | `C:\Users\Mitch\Projects\Trend Scanner v1` | yes (pilot baseline at 2f57e65, 2026-05-08) | v1.3 | 2026-05-10 |
| po3-scanner-v1 | `C:\Users\Mitch\Projects\PO3_Scannerv1` | yes (Pass 2 merged 2026-05-09 as PR #1) | v1.3 | 2026-05-10 |
| solana-command-center-v1 | `C:\Users\Mitch\Projects\Solana-Command-Centerv1` | yes (first migration pass merged 2026-05-10 as PR #1) | v1.3 | 2026-05-10 |

## Candidates (known but not onboarded)

| Name | Path | Notes |
|---|---|---|
| trend-scanner-v2 | `C:\Users\Mitch\Projects\Trend Scannerv2` | Per PO3 Scanner memory: exists on disk but is NOT a reference unless the operator opens that scope explicitly. Not active and not audited by default. |

## Procedure for adding a project

1. Confirm the project is one you want fleet auditing on.
2. Add a row to "Active projects" with: name, absolute path, onboarded
   status, declared version (or `(none)` if pre-onboarding), `never`
   for last audited.
3. Save the file. The next `JAI fleet sync audit` run picks it up.

## Procedure for retiring a project

1. Move the row from "Active projects" to a "Retired projects" section
   below (create the section if it doesn't yet exist).
2. Annotate why and when.
3. Retired rows stay in the file for audit trail; do not delete.
