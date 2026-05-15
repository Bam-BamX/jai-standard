# [DRAFT] JAI_OVERRIDE_CONTRACT.md

**Status:** [DRAFT] — sandbox prototype for operator review
**Target version:** vNEXT-draft (additive)
**Plan reference:** [`role-developer-task-research-shimmering-anchor.md`](file:///C:/Users/Mitch/.claude/plans/role-developer-task-research-shimmering-anchor.md) §"Override / Narrow Contract"
**Inheritance:** Inherits S1–S8 from [`policies/SAFETY_RULES.md`](file:///C:/Users/Mitch/.claude/jai-standard/policies/SAFETY_RULES.md). Defines the override grammar referenced by [`JAI_OVERLAY_SCHEMA.md`](./JAI_OVERLAY_SCHEMA.md) and [`JAI_DRIFT_DETECTION.md`](./JAI_DRIFT_DETECTION.md).
**Scope:** Global only. Defines what a project's `PROJECT_EXCEPTIONS.md` may declare and what the audit treats as a violation.

## 0. Purpose

Today, a project's [`PROJECT_EXCEPTIONS.md`](file:///C:/Users/Mitch/Projects/Solana-Command-Centerv1/jai/PROJECT_EXCEPTIONS.md) is a free-form text file with no machine-checkable grammar. The operator may not know whether a given exception entry is *legitimate* (project narrows a global rule for stricter scope) or *illegitimate* (project weakens a global rule, breaking conformance).

This contract defines:
- Four **allowed** override categories with explicit grammar
- Four **forbidden** override categories with explicit audit failures
- A resolution order for conflicting global+local rules
- An entry shape that the audit can mechanically classify

## 1. Allowed Operations

A project MAY perform these four operations on global rules without losing conformance:

### 1.1 Narrow

**Definition:** Apply a stricter version of a global rule for this project.

**Always allowed.** Narrowing strengthens; never weakens.

**Entry shape (in `PROJECT_EXCEPTIONS.md`):**
```
### Exception: <slug>
- Rule: <id from policies/ — e.g., SECURITY_RULES.md §3.2>
- Action: NARROW
- Global wording: <quoted from global policy>
- Stricter constraint: <project's tighter constraint>
- Justification: <why this project needs the tighter constraint>
- Approved at: YYYY-MM-DD
```

**Examples:**
- Global allows `Bash(git fetch *)`; project narrows to `Bash(git fetch origin)` only.
- Global allows `Bash(npm install *)`; project narrows to `Bash(npm install --no-fund --no-audit)`.

### 1.2 Augment

**Definition:** Add a new project-specific rule on top of global, with no conflict.

**Always allowed.** Augmentation extends; never replaces.

**Entry shape:**
```
### Exception: <slug>
- Rule: NEW (project-specific)
- Action: AUGMENT
- New constraint: <text>
- Reference (if extends a category): <e.g., extends SECURITY_RULES.md §X>
- Justification: <why this project adds this constraint>
- Approved at: YYYY-MM-DD
```

**Examples:**
- Project adds: "Pre-change report must include a Telegram-rate-limit check before any consumer-wiring change."
- Project adds: "Migrations may only be run between 02:00–05:00 UTC."

### 1.3 Adopt-Optional

**Definition:** Adopt a global file marked optional in `JAI_OVERLAY_SCHEMA.md` MAY list.

**Always allowed.** Recorded in the project's `JAI_CONFORMANCE.md` adopted-optional list, not in `PROJECT_EXCEPTIONS.md`.

**Entry shape (in `JAI_CONFORMANCE.md`, not `PROJECT_EXCEPTIONS.md`):**
```yaml
adopted_optional_files:
  - PROCESS_CONVENTIONS.md       # SCC-pioneered Fast/Review/Blocked categorization
  - CLEANUP_REGISTER.md
```

### 1.4 Replace-with-Exception

**Definition:** Substitute a global rule with a different rule that does not weaken S1–S8.

**Conditionally allowed** — requires explicit operator approval AND a sunset date AND the substitution must not weaken S1–S8.

**Entry shape:**
```
### Exception: <slug>
- Rule: <id>
- Action: REPLACE_WITH_EXCEPTION
- Global wording: <quoted>
- Project wording: <substitution>
- Why replace (not narrow/augment): <rationale>
- Sunset date: YYYY-MM-DD (after which exception expires unless renewed)
- Operator approval reference: <commit hash, decision log entry, or chat record>
- Conformance impact: <does this affect any required-file presence? any frontmatter declaration?>
```

**Examples (rare):**
- Project uses `master` branch instead of `main` (replace global's main-branch assumption); no S1–S8 impact; sunset = "until next major repo restructure."
- Project uses a project-specific commit-message format that diverges from any global convention.

**Replace MUST NOT weaken S1–S8.** A Replace entry that touches S1–S8 wording is automatically `INVALID_WEAKENING` regardless of operator claim.

## 2. Forbidden Operations

A project MUST NOT perform these operations. The audit flags any attempt as a violation.

### 2.1 Weaken

**Definition:** Relax a global rule.

**Always forbidden.** Audit flags as `INVALID_WEAKENING` and the project loses conformance status until the entry is removed or rewritten.

**Examples:**
- Project allows `Read(**/.env)` (weakens S2). REJECTED.
- Project removes the operator-approval gate for migrations (weakens S4). REJECTED.
- Project allows `Bash(git push --force *)` to protected branches (weakens Blocked Lane). REJECTED.
- Project sets confidence threshold to <50% to "catch more issues" (weakens noise discipline). REJECTED.

### 2.2 Remove

**Definition:** Drop a globally-required file.

**Always forbidden.** Audit reports `DRIFT_BLOCKING` per [`JAI_DRIFT_DETECTION.md`](./JAI_DRIFT_DETECTION.md). Required files per [`JAI_OVERLAY_SCHEMA.md §1`](./JAI_OVERLAY_SCHEMA.md) cannot be removed without a MAJOR version fork (which is itself a separate operator-approved project).

### 2.3 Contradict

**Definition:** Establish a project rule that conflicts with global without an exception entry.

**Always forbidden.** Audit reports `DIVERGENT`. The contradiction must be reconciled before any non-trivial work proceeds. Reconciliation = Narrow, Augment, Replace-with-Exception (with operator approval), or remove the project rule.

### 2.4 Silent Override

**Definition:** Behavior different from global without declaring it.

**Always forbidden.** All overrides MUST be explicit in `PROJECT_EXCEPTIONS.md` (or `JAI_CONFORMANCE.md` `adopted_optional_files:` frontmatter for adopted-optional per §1.3). Implicit overrides are audit failures even if the underlying intent is legitimate.

**Common silent overrides to watch for:**
- A project's local skill that diverges from canonical without `PROJECT_EXCEPTIONS.md` Replace entry.
- A project's `WORKFLOW_RULES.md` allowing a command not in the global universal portion without an Augment entry.
- A `CLAUDE.md` that contradicts a global policy without exception declaration.

## 3. Resolution Order

When global and local appear to conflict, the audit applies this order:

1. **Both apply, local takes precedence on overlapping scope:** if local is `Narrow` or `Augment` consistent with global → both apply, local wins where they overlap.
2. **Local wins for named scope, global elsewhere:** if local is `Replace-with-Exception` with valid entry → local for the named scope, global everywhere else.
3. **Halt → operator:** if local conflicts with global without an exception entry → `DIVERGENT`. Halts work; routes to operator per [`JAI_GOVERNANCE_STACK.md §4`](file:///C:/Users/Mitch/.claude/jai-standard/JAI_GOVERNANCE_STACK.md).
4. **Local stands alone:** if global file does not exist for the topic → local stands alone (still audited against S1–S8 universal floor).

## 4. Audit Semantics

`JAI fleet sync audit` (per [`JAI_FLEET_SYNC_AUDIT.md`](file:///C:/Users/Mitch/.claude/jai-standard/JAI_FLEET_SYNC_AUDIT.md)) reads each project's `PROJECT_EXCEPTIONS.md` and:

1. Parses each entry for the `Action:` keyword (`NARROW` / `AUGMENT` / `REPLACE_WITH_EXCEPTION`).
2. Validates the entry shape (required fields present per §1).
3. Cross-checks the `Rule:` reference against the named global file/section.
4. Confirms the constraint genuinely narrows/augments and does not weaken (heuristic check; ambiguity routes to operator).
5. Confirms `REPLACE_WITH_EXCEPTION` entries have operator approval reference and a sunset date that has not passed.
6. Reports per-project conformance level as a **sub-finding** that maps to a drift class per [`JAI_DRIFT_DETECTION.md §2`](./JAI_DRIFT_DETECTION.md):

   | Sub-finding | Drift class | Notes |
   |---|---|---|
   | `CONFORMANT` | ALIGNED | No exceptions, all required files present |
   | `CONFORMANT_WITH_NARROWING` | ALIGNED | Exceptions exist, all valid Narrow/Augment |
   | `CONFORMANT_WITH_REPLACE` | ALIGNED | Replace exceptions exist, all approved and unexpired |
   | `DRIFT_DETECTED` | DRIFT_ACCEPTABLE or DRIFT_BLOCKING | Required files missing → BLOCKING; recommended files missing → ACCEPTABLE |
   | `EXPIRED_EXCEPTION` | DRIFT_ACCEPTABLE | Sub-finding: Replace exception past sunset date; needs renewal or removal. Escalates to DRIFT_BLOCKING after 3 consecutive monthly audits with no action (per Open Question 17 default; operator override available). |
   | `INVALID_WEAKENING` | **DIVERGENT (always)** | Sub-finding: at least one entry weakens a global rule. Always blocking regardless of operator claim; sub-finding never downgrades. |
   | `SILENT_OVERRIDE` | DIVERGENT | Silent override or contradiction without exception entry |

7. **AUDIT_IGNORE_BLOCK sentinel.** Lines between `<!-- AUDIT_IGNORE_BLOCK -->` and `<!-- /AUDIT_IGNORE_BLOCK -->` HTML comment markers are skipped by the entry parser. This lets `PROJECT_EXCEPTIONS.md` carry teaching demonstrations (e.g., what an `INVALID_WEAKENING` entry would look like) without the parser treating them as live entries. The sentinels MUST appear as literal HTML comments; whitespace-only variation is tolerated.

## 5. Practical Examples (for SCC reference)

SCC's [`PROJECT_EXCEPTIONS.md`](file:///C:/Users/Mitch/Projects/Solana-Command-Centerv1/jai/PROJECT_EXCEPTIONS.md) is currently empty. If SCC adopted vNEXT-draft, hypothetical example entries it might add:

**Example NARROW** (none currently needed for SCC):
```
### Exception: ban-fetch-from-non-origin
- Rule: WORKFLOW_RULES.md (universal §3 — git fetch)
- Action: NARROW
- Global wording: "Bash(git fetch *) is allowed"
- Stricter constraint: SCC restricts to "Bash(git fetch origin)" only
- Justification: SCC has a single canonical remote; fetching from forks bypasses the operator's fleet review
- Approved at: 2026-05-14
```

**Example AUGMENT:**
```
### Exception: pre-change-includes-bullmq-check
- Rule: NEW (SCC-specific)
- Action: AUGMENT
- New constraint: Pre-change reports for any consumer-wiring change MUST include a check that BullMQ queue state is observable via "docker compose ps" output (read-only; pre-documented in WORKFLOW_RULES.md §X)
- Reference: extends WORKFLOW_RULES.md §13 (pre-change report fields)
- Justification: BullMQ wiring is the most common SCC failure mode; explicit check reduces incident response time
- Approved at: 2026-05-14
```

**Example REPLACE_WITH_EXCEPTION:**
```
### Exception: master-branch-not-main
- Rule: WORKFLOW_RULES.md (universal §1 — branch naming)
- Action: REPLACE_WITH_EXCEPTION
- Global wording: "Default branch is 'main'"
- Project wording: "Default branch is 'master' (legacy; rename deferred per DECISION_LOG D-N)"
- Why replace: Repo predates the main-branch convention; renaming has non-trivial CI/script impact deferred to a separate restructure project
- Sunset date: 2026-12-31 (re-evaluate at year-end)
- Operator approval reference: DECISION_LOG D-3 (commit a829198)
- Conformance impact: None (S1–S8 untouched; protected-branch list updated to include "master")
```

**Example INVALID_WEAKENING (would be flagged):**
```
### Exception: allow-env-reads
- Rule: SAFETY_RULES.md S2
- Action: REPLACE_WITH_EXCEPTION
- Global wording: "No .env reads or writes"
- Project wording: "Allow Read(**/.env) for debugging"
- Justification: speeds up debug cycle
```
This entry is `INVALID_WEAKENING` regardless of approval — S2 cannot be weakened. Audit flags it; project loses conformance.

## 6. Maintenance Rule

Changes to this contract follow the standard JAI governance process. Changes that *add* an allowed operation are MINOR. Changes that *remove* a forbidden category are MAJOR (would loosen the override grammar).
