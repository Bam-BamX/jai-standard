# [DRAFT] JAI_OVERLAY_SCHEMA.md

**Status:** [DRAFT] — sandbox prototype for operator review
**Target version:** vNEXT-draft (additive)
**Plan reference:** [`role-developer-task-research-shimmering-anchor.md`](file:///C:/Users/Mitch/.claude/plans/role-developer-task-research-shimmering-anchor.md) §"Proposed Local Project Overlay Structure"
**Inheritance:** Inherits S1–S8 from [`policies/SAFETY_RULES.md`](file:///C:/Users/Mitch/.claude/jai-standard/policies/SAFETY_RULES.md). Companion contracts: [`JAI_OVERRIDE_CONTRACT.md`](./JAI_OVERRIDE_CONTRACT.md), [`JAI_DRIFT_DETECTION.md`](./JAI_DRIFT_DETECTION.md), [`JAI_AUTOMATION_LANES.md`](./JAI_AUTOMATION_LANES.md).
**Scope:** Global only. Defines the shape every project's `<project>/jai/` MUST conform to.

## 0. Purpose

`JAI_OVERLAY_SCHEMA.md` is the **formal contract** for what a local `<project>/jai/` overlay MUST contain, MAY contain, and MUST NOT contain. Today this is implicit in [`JAI_PROJECT_IMPLEMENTATION_PLAN.md`](file:///C:/Users/Mitch/.claude/jai-standard/JAI_PROJECT_IMPLEMENTATION_PLAN.md). The schema makes it mechanical: a project's surface can be audited against the schema, and conformance reported as `ALIGNED` / `DRIFT_ACCEPTABLE` / `DRIFT_BLOCKING` / `DIVERGENT` per [`JAI_DRIFT_DETECTION.md`](./JAI_DRIFT_DETECTION.md).

## 1. Required Files (MUST)

A conformant `<project>/jai/` MUST contain:

| File | Purpose | Notes |
|---|---|---|
| `JAI_CONFORMANCE.md` | Drift Manifest — declares standard version + lists each required file with present/absent/divergent status | Schema in [`JAI_DRIFT_DETECTION.md`](./JAI_DRIFT_DETECTION.md); template in [`templates/drift_manifest.template.md`](./templates/drift_manifest.template.md) |
| `CLAUDE.md` | Mandatory operating rules; first read before any change | Must declare conformance version in frontmatter; target <50 lines, hard cap 100 |
| `PROJECT_CONTEXT.md` | What this project is and is not; scope; integrations | Project-specific only; no global content |
| `SECURITY_RULES.md` | Project-specific security extensions | Extends `policies/SECURITY_RULES.md` via Narrow/Augment per [`JAI_OVERRIDE_CONTRACT.md`](./JAI_OVERRIDE_CONTRACT.md); never weakens |
| `WORKFLOW_RULES.md` | Project-specific workflow extensions; lists all pre-documented commands per Lane | Extends `policies/WORKFLOW_RULES.md`; the canonical list of approved commands for the project's Fast/Review/Strict lanes |
| `THREAT_MODEL.md` | Project-specific threats, assets, boundaries | Extends `policies/THREAT_MODEL_BASELINE.md`; never duplicates universal threats |

**Audit semantic:** A project missing any required file is `DRIFT_BLOCKING` per [`JAI_DRIFT_DETECTION.md`](./JAI_DRIFT_DETECTION.md). Non-trivial work is halted until the missing file is created (Review Lane batch).

## 2. Recommended Files (SHOULD)

A conformant project SHOULD contain (absence is a DRIFT_ACCEPTABLE warning, not blocking):

| File | Purpose | When to skip |
|---|---|---|
| `DECISION_LOG.md` | Append-only project decisions | Skip only for trivial single-author one-shot repos |
| `CURRENT_STATE.md` | Living project status; "this file wins" on stale-conflict | Skip only if the project is purely a library with no operational state |
| `HANDOFF.md` | Session-to-session continuity | Skip only for solo never-handoff workflows |
| `VULNERABILITY_REVIEW.md` | CVE/advisory check log | Skip only if project has zero external dependencies |
| `ARCHITECTURE.md` | Project shape (or pointer to docs/) | Skip if project has no non-obvious architecture |
| `INFRASTRUCTURE.md` | Compose/ports/volumes (or pointer) | Skip if project has no infrastructure footprint |

## 3. Optional Files (MAY)

A conformant project MAY contain (presence is acceptable; absence is normal):

| File | Purpose |
|---|---|
| `PROJECT_EXCEPTIONS.md` | Structured Narrow/Augment/Replace overrides per [`JAI_OVERRIDE_CONTRACT.md`](./JAI_OVERRIDE_CONTRACT.md). Empty file is acceptable; missing file means "no exceptions declared" |
| `PROCESS_CONVENTIONS.md` | Project-specific Fast/Review/Blocked categorization (SCC pioneered this; candidate for global lift if pattern stabilizes) |
| `CLEANUP_REGISTER.md` | Tracks files slated for deletion/archival |
| `UNIVERSAL_SELF_POINTER.md` | Pointer to operator's universal-self file (operator-personal layer, separate from JAI) |
| `rules/*.md` | Project-specific rule pilots before propagation to global |
| `skills/*.skill.md` | Project-local skill overrides only when project needs to extend a global skill |
| `templates/*.template.md` | Project-local template overrides only when project needs an augmented copy declared in `PROJECT_EXCEPTIONS.md` |
| `archive/` | Deprecated artifacts (with README explaining what was moved and why) |

## 4. Forbidden Files (MUST NOT)

A conformant project MUST NOT contain:

| Forbidden item | Reason |
|---|---|
| Copies of global JAI spec files | `JAI_STANDARD.md`, `JAI_GOVERNANCE_STACK.md`, `JAI_AGENT_ARCHITECTURE.md`, `JAI_DEV_RESPONSE_FORMAT.md`, `JAI_RUNTIME_ADVISORY_REVIEWER.md`, `JAI_PROJECT_IMPLEMENTATION_PLAN.md`, `JAI_FLEET_SYNC_AUDIT.md`, `JAI_RESEARCH_BACKED_PLAN_MODE.md`, `JAI_GOVERNANCE_REVIEW.md`, `JAI_COMMANDS.md`, `JAI_CHANGELOG.md`, `JAI_OVERLAY_SCHEMA.md`, `JAI_OVERRIDE_CONTRACT.md`, `JAI_DRIFT_DETECTION.md`, `JAI_AUTOMATION_LANES.md` — all referenced by version in `JAI_CONFORMANCE.md`, never copied |
| Copies of global policies | `policies/SAFETY_RULES.md`, `policies/SECURITY_RULES.md`, `policies/WORKFLOW_RULES.md`, `policies/THREAT_MODEL_BASELINE.md`, `policies/RESTRICTED_FILE_PATTERNS.md` — referenced by version, extended in project's own SECURITY_RULES.md / WORKFLOW_RULES.md / THREAT_MODEL.md |
| Copies of global catalogs | `catalogs/SECRET_REGEX_CATALOG.md`, etc. — referenced by version |
| External vetting reports | These live only in global `references/` |
| Enhancement intake items | These live only in global `intake/` |
| `.env*` files | Even examples — use `.env.example` outside `jai/` |

## 5. Frontmatter Requirements

`<project>/jai/CLAUDE.md` MUST declare conformance via frontmatter:

```yaml
---
jai_standard_version: vX.Y         # the version this project conforms to
declared_at: YYYY-MM-DD             # matches JAI_CONFORMANCE.md frontmatter per JAI_DRIFT_DETECTION.md §1
last_audit: YYYY-MM-DD              # matches JAI_CONFORMANCE.md frontmatter
project_name: <name>
---
```

Other files MAY include frontmatter. Optional frontmatter for path-scoped skill files (per dotclaude pattern adopted in vNEXT-draft):

```yaml
---
paths: ["src/api/**", "src/auth/**"]   # optional; if present, skill loads only when matched paths in scope
alwaysApply: false                       # default; set true for always-loaded behavior
---
```

## 6. Layer Inheritance

A project inherits from global in this strict order:

1. **`policies/SAFETY_RULES.md`** (S1–S8) — verbatim, no modification, no exception
2. **`policies/RESTRICTED_FILE_PATTERNS.md`** — verbatim, project may *add* patterns via `SECURITY_RULES.md` Augment, never remove
3. **`policies/SECURITY_RULES.md`** — universal portion; project's `<project>/jai/SECURITY_RULES.md` extends via Narrow/Augment per [`JAI_OVERRIDE_CONTRACT.md`](./JAI_OVERRIDE_CONTRACT.md)
4. **`policies/WORKFLOW_RULES.md`** — universal portion; project extends in `<project>/jai/WORKFLOW_RULES.md`
5. **`policies/THREAT_MODEL_BASELINE.md`** — universal threat catalog; project extends in `<project>/jai/THREAT_MODEL.md`
6. **`catalogs/*`** — referenced by skills/agents; never inherited as project files
7. **`skills/*`** — canonical specs lifted to global; project may run them as-is, override only via `PROJECT_EXCEPTIONS.md` Replace entry with explicit operator approval
8. **`templates/*`** — same as skills

## 7. Audit Procedure

A project's surface is audited by:

1. Read `<project>/jai/JAI_CONFORMANCE.md`. If absent → `DRIFT_BLOCKING`.
2. Read `jai_standard_version` from the manifest. Look up the corresponding global version's required-file list (this schema, scoped to that version).
3. For each required file in the schema: confirm presence in `<project>/jai/`. Missing → `DRIFT_BLOCKING`.
4. For each recommended file: confirm presence. Missing → `DRIFT_ACCEPTABLE` (warning).
5. For each forbidden file: confirm absence. Present → `DIVERGENT` (audit failure).
6. For each frontmatter requirement: confirm declaration. Missing → `DRIFT_BLOCKING` if required, `DRIFT_ACCEPTABLE` if optional.
7. Check `PROJECT_EXCEPTIONS.md` (if present): every entry must be `Narrow`, `Augment`, or `Replace-with-Exception` per [`JAI_OVERRIDE_CONTRACT.md`](./JAI_OVERRIDE_CONTRACT.md). Any `Weaken` is flagged as the `INVALID_WEAKENING` sub-finding, which always maps to drift class `DIVERGENT` per [`JAI_OVERRIDE_CONTRACT.md §4`](./JAI_OVERRIDE_CONTRACT.md).
8. Compute lifted-file checksums for any `<project>/jai/skills/*.skill.md` declared as lifted-from-global; compare against current global SHA. Drift → log in audit report (not blocking unless project's `JAI_CONFORMANCE.md` declares it should match).
9. Output: per-project status report.

## 8. Maintenance Rule

Changes to this schema follow the standard JAI governance process:
1. Enhancement Intake (`JAI_COMMANDS.md §8`)
2. Standard Update (`JAI_COMMANDS.md §9`)
3. Version bump + JAI_CHANGELOG entry

A change that *removes* a required file or *adds* a new required file is MAJOR (forces every project to re-audit). A change that adds a recommended/optional file or relaxes a forbidden rule is MINOR.
