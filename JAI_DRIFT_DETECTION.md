# [DRAFT] JAI_DRIFT_DETECTION.md

**Status:** [DRAFT] — sandbox prototype for operator review
**Target version:** vNEXT-draft (additive)
**Plan reference:** [`role-developer-task-research-shimmering-anchor.md`](file:///C:/Users/Mitch/.claude/plans/role-developer-task-research-shimmering-anchor.md) §"Drift Detection"
**Inheritance:** Reads from [`JAI_OVERLAY_SCHEMA.md`](./JAI_OVERLAY_SCHEMA.md), [`JAI_OVERRIDE_CONTRACT.md`](./JAI_OVERRIDE_CONTRACT.md), and [`JAI_FLEET_SYNC_AUDIT.md`](file:///C:/Users/Mitch/.claude/jai-standard/JAI_FLEET_SYNC_AUDIT.md). Defines the manifest schema and audit semantics that make `JAI fleet sync audit` mechanical.
**Scope:** Global only.

## 0. Purpose

Today, project conformance is **declarative**: a project's `CLAUDE.md` mentions "JAI Standard v1.3" and the operator hopes the surface matches. Drift is invisible until someone notices.

This spec replaces declaration-only with a **machine-readable Drift Manifest** at `<project>/jai/JAI_CONFORMANCE.md` that:
- Lists every required file from the project's declared version
- Lists root assistant entrypoints such as `CLAUDE.md` and Codex `AGENTS.md`
- Records present/absent/divergent status per file
- Records lifted-file checksums vs current global
- Surfaces all `PROJECT_EXCEPTIONS.md` entries with their classifications

`JAI fleet sync audit` reads each project's manifest and produces a fleet conformance report.

## 1. Drift Manifest Schema

The Drift Manifest is `<project>/jai/JAI_CONFORMANCE.md`. Required structure:

````markdown
---
jai_standard_version: vX.Y           # what this project conforms to
declared_at: YYYY-MM-DD              # when this version was declared
last_audit: YYYY-MM-DD               # when last fleet-sync-audit ran
last_migration: YYYY-MM-DD           # date of last completed migration pass
project_name: <name>
adopted_optional_files:              # files the project opts into per JAI_OVERLAY_SCHEMA.md MAY
  - PROCESS_CONVENTIONS.md
  - CLEANUP_REGISTER.md
---

# JAI Conformance — <project name>

## Required Files (per JAI_OVERLAY_SCHEMA.md §1 at vX.Y)
- [x] CLAUDE.md (present, frontmatter declares vX.Y)
- [x] PROJECT_CONTEXT.md (present)
- [x] SECURITY_RULES.md (present, extends policies/SECURITY_RULES.md@vX.Y)
- [x] WORKFLOW_RULES.md (present, extends policies/WORKFLOW_RULES.md@vX.Y)
- [x] THREAT_MODEL.md (present, extends policies/THREAT_MODEL_BASELINE.md@vX.Y)
- [x] JAI_CONFORMANCE.md (this file)

## Root Assistant Entrypoints (per JAI_OVERLAY_SCHEMA.md §1A at vX.Y)
- [x] ../CLAUDE.md (present / not applicable)
- [x] ../AGENTS.md (present for Codex-enabled project; thin pointer; no weakened gates)

## Recommended Files (per JAI_OVERLAY_SCHEMA.md §2)
- [x] DECISION_LOG.md (present)
- [x] CURRENT_STATE.md (present)
- [x] HANDOFF.md (present)
- [x] VULNERABILITY_REVIEW.md (present)
- [ ] ARCHITECTURE.md (skipped — pointer in CLAUDE.md to docs/PHASE2_PLAN.md)
- [x] INFRASTRUCTURE.md (present)

## Optional Files Adopted (per JAI_OVERLAY_SCHEMA.md §3)
- [x] PROCESS_CONVENTIONS.md (Fast/Review/Blocked lanes)
- [x] CLEANUP_REGISTER.md
- [x] PROJECT_EXCEPTIONS.md (currently empty — no overrides declared)

## Forbidden Files (per JAI_OVERLAY_SCHEMA.md §4)
None present (verified at last_audit).

## Lifted-File Checksums (per JAI_OVERLAY_SCHEMA.md §6)
| File | Local SHA | Global SHA (at vX.Y) | Status |
|---|---|---|---|
| skills/planning.skill.md | <hash> | <hash> | match / drift / local-fork |
| skills/security_review.skill.md | <hash> | <hash> | match |
| skills/secret_scanning.skill.md | <hash> | <hash> | match |
| skills/threat_modeling.skill.md | <hash> | <hash> | match |
| skills/context_budget.skill.md | <hash> | <hash> | match |
| skills/drift_audit.skill.md | <hash> | <hash> | match |

## Declared Exceptions (per JAI_OVERRIDE_CONTRACT.md)
See PROJECT_EXCEPTIONS.md.

Summary:
- Narrow: <count>
- Augment: <count>
- Replace-with-Exception: <count> (next sunset: YYYY-MM-DD)
- Invalid-Weakening: 0 (would block conformance)
````

## 2. Drift Classes

The audit assigns one of five classes per project. The first four are drift classes (degrees of divergence from declared conformance); `UNAUDITED_SANDBOX` is a terminal non-drift class for sandbox/draft tiers that are not subject to standard drift audit.

| Class | Definition | Action |
|---|---|---|
| **ALIGNED** | Surface matches declared version's manifest exactly. No drift on lifted files. All exceptions valid. | None. Continue. |
| **DRIFT_ACCEPTABLE** | Project is behind global, but declared version is still supported (within last N versions, where N defined by `JAI_STANDARD.md`). Or: recommended files missing (warning only). | Surface as informational. Schedule migration via [`JAI_MIGRATION_PLAYBOOK.md`](file:///C:/Users/Mitch/.claude/jai-standard/JAI_MIGRATION_PLAYBOOK.md) when convenient. Continue work. |
| **DRIFT_BLOCKING** | Project's declared version is unsupported (older than min-supported), or required files missing for declared version, or required frontmatter declarations missing. | Halt non-trivial work. Migrate before next Strict Lane batch. Fast Lane and Review Lane work that doesn't touch the missing surface may proceed. |
| **DIVERGENT** | Project's surface contains rules that contradict global without exception entries (silent override per [`JAI_OVERRIDE_CONTRACT.md §2.4`](./JAI_OVERRIDE_CONTRACT.md)), claims a future version, or has `INVALID_WEAKENING` entries in `PROJECT_EXCEPTIONS.md`. | Block all work. Reconcile via `PROJECT_EXCEPTIONS.md` entry, roll back the local change, or operator-approved fork. |
| **UNAUDITED_SANDBOX** | Declared `jai_standard_version` matches a sandbox/draft pattern (e.g., `vNEXT-*`, `*-draft`). Project is a sandbox tier per [`lab/README.md`](../README.md), not subject to standard drift audit. | Informational only. Continue. Promotion (per [`lab/README.md §11`](../README.md)) flips the declared version to a released label and re-audits. |

## 3. Audit Algorithm

`JAI fleet sync audit` runs this procedure per project:

```
Step 1: Read <project>/jai/JAI_CONFORMANCE.md
  - If absent → DRIFT_BLOCKING (manifest is required per JAI_OVERLAY_SCHEMA.md §1)
  - Parse frontmatter; extract jai_standard_version

Step 2: Validate declared version
  - If matches sandbox/draft pattern (vNEXT-*, *-draft) → UNAUDITED_SANDBOX (informational; sandbox tier per lab/README.md). Skip remaining steps; aggregate class = UNAUDITED_SANDBOX.
  - Look up jai_standard_version in JAI_CHANGELOG.md
  - If unknown version → DIVERGENT (claims a future version)
  - If older than min-supported version → DRIFT_BLOCKING
  - Else → continue

Step 3: Read JAI_OVERLAY_SCHEMA.md §1 required files for declared version
  - For each required file: check presence in <project>/jai/
  - Missing → DRIFT_BLOCKING (record which files)

Step 3A: Read JAI_OVERLAY_SCHEMA.md §1A root assistant entrypoints
  - For Codex-enabled projects, check <project>/AGENTS.md
  - If AGENTS.md is missing after the project declares a version that requires it → DRIFT_BLOCKING
  - If AGENTS.md weakens approval gates, direct-main protection, merge gates, deploy/rebuild/restart gates, flag-flip gates, or mandatory closeout → DIVERGENT
  - Confirm AGENTS.md is thin and points to project CLAUDE.md and/or <project>/jai/ rather than blindly copying another agent file

Step 4: Read JAI_OVERLAY_SCHEMA.md §2 recommended files for declared version
  - For each recommended file: check presence
  - Missing → DRIFT_ACCEPTABLE (record which files; emit warning)

Step 5: Read JAI_OVERLAY_SCHEMA.md §4 forbidden files
  - For each forbidden file: confirm absence
  - Present → DIVERGENT (record which files)

Step 6: Validate frontmatter declarations
  - <project>/jai/CLAUDE.md must declare jai_standard_version matching the manifest
  - Mismatch → DIVERGENT

Step 7: Read PROJECT_EXCEPTIONS.md (if present; absence is OK)
  - Skip any content between <!-- AUDIT_IGNORE_BLOCK --> and <!-- /AUDIT_IGNORE_BLOCK --> sentinels per JAI_OVERRIDE_CONTRACT.md §4 item 7
  - For each remaining entry: parse Action keyword
  - Validate entry shape per JAI_OVERRIDE_CONTRACT.md §1
  - Classify each entry: NARROW / AUGMENT / REPLACE_WITH_EXCEPTION / INVALID
  - INVALID_WEAKENING anywhere → sub-finding maps to drift class DIVERGENT (always blocking, per JAI_OVERRIDE_CONTRACT.md §4)
  - Expired Replace-with-Exception sunset → sub-finding EXPIRED_EXCEPTION maps to drift class DRIFT_ACCEPTABLE (warn, ask operator to renew or remove). Escalates to DRIFT_BLOCKING after 3 consecutive monthly audits with no action (per Open Question 17 default; operator override available).

Step 8: Compute lifted-file checksums (if any declared in manifest §"Lifted-File Checksums")
  - For each lifted file: hash local content, compare to current global SHA
  - Mismatch + no Replace-with-Exception entry → flag for operator (not auto-blocking; lifted files may legitimately drift while waiting for migration)

Step 9: Compute aggregate class
  - UNAUDITED_SANDBOX from Step 2 → UNAUDITED_SANDBOX (terminal; further classes not evaluated)
  - Any DIVERGENT (including INVALID_WEAKENING sub-finding) → DIVERGENT
  - Any DRIFT_BLOCKING → DRIFT_BLOCKING
  - Any DRIFT_ACCEPTABLE (including EXPIRED_EXCEPTION sub-finding) → DRIFT_ACCEPTABLE
  - Else → ALIGNED

Step 10: Output per-project report
  - Class
  - Specific findings per step
  - Recommended migration target (if drifted)
  - Recommended remediation per finding
```

## 4. Trigger Cadence

Drift detection fires:

1. **Session start** (informational only). Surface the project's class to the operator without blocking.
2. **Before any Strict Lane batch** (mandatory). Block on `DRIFT_BLOCKING` or `DIVERGENT`.
3. **Manual via `JAI fleet sync audit`** (existing command per [`JAI_COMMANDS.md`](file:///C:/Users/Mitch/.claude/jai-standard/JAI_COMMANDS.md)).
4. **After a global version bump** — sweep all projects in [`JAI_PROJECT_REGISTRY.md`](file:///C:/Users/Mitch/.claude/jai-standard/JAI_PROJECT_REGISTRY.md) and produce a fleet drift report.
5. **Operator-set cadence** (proposed: monthly) — runs all four drift classes against the registry. (Projects in `UNAUDITED_SANDBOX` are listed informationally; the standard audit does not score them.)

## 5. Concrete Current State (as of 2026-05-14, before vNEXT-draft promotion)

Snapshot of each registered project (audited from read-only inspection during plan research):

| Project | Declared version | Manifest present | Required files | Drift class |
|---|---|---|---|---|
| solana-command-center-v1 (SCC) | v1.3 | No (predates vNEXT-draft) | All v1.3 required files present | DRIFT_ACCEPTABLE (4 versions behind global v1.7) |
| trend-scanner-v1 | unknown | No | not audited | UNAUDITED — first-audit pending |
| po3-scanner-v1 | unknown | No | not audited | UNAUDITED — first-audit pending |

After vNEXT-draft promotion, each project gets a `JAI_CONFORMANCE.md` as part of its migration pass. Projects without a manifest are treated as `DRIFT_BLOCKING` until one is created.

## 6. Drift Report Format

Per-project audit output (markdown, paste-ready for operator review):

```markdown
## Audit: <project name>
- Date: YYYY-MM-DD
- Declared version: vX.Y
- Class: ALIGNED / DRIFT_ACCEPTABLE / DRIFT_BLOCKING / DIVERGENT

### Findings
- [Step 3] Required file <name>: <status>
- [Step 4] Recommended file <name>: <status>
- ...

### Exceptions
- <count> Narrow / <count> Augment / <count> Replace-with-Exception
- <count> Invalid-Weakening (always blocking)
- Next sunset: YYYY-MM-DD

### Lifted-file drift
- <file>: local SHA <hash> vs global SHA <hash> — <match / drift / local-fork>

### Recommended action
- <one of: continue / file migration intake / reconcile divergence via PROJECT_EXCEPTIONS / halt and route to operator>
```

## 7. Maintenance Rule

Changes to this spec follow the standard JAI governance process. Changes to the Drift Manifest schema are **MAJOR** (every project must update its manifest). Changes that add a new optional field, a new audit step, or a new class transition are MINOR.
