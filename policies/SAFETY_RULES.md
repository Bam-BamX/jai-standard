# JAI Safety Rules — S1–S8 (canonical)

**Standard version:** Canonical home introduced in JAI v1.5 (2026-05-10).
**Lifted from:** [`JAI_GOVERNANCE_STACK.md §6`](../JAI_GOVERNANCE_STACK.md#6--safety-rules) (introduced v1.0). The §6 anchor remains as a redirect; this file is the canonical text.

---

## 0. Purpose

These eight rules are the cross-cutting absolutes that hold across every JAI workflow — Enhancement Intake (`JAI_GOVERNANCE_STACK.md §4`), External Vetting (`JAI_COMMANDS.md §7`), Drift Audit (§4), Migration Pass (§5), Project Sync (`JAI_GOVERNANCE_STACK.md §5`), Standard Update (`JAI_COMMANDS.md §9`), Runtime Advisory Review (§11), Research-Backed Plan Mode (§14), and Governance Review (§15).

This file is the canonical source. Other files in the standard reference these rules by number; they do not re-declare them.

---

## 1. The rules

Absolute. Hold across enhancement intake, external vetting, drift audit, and migration passes.

| # | Rule | Why |
|---|---|---|
| **S1** | **Docs-only unless separately approved.** No code, test, or settings changes inside a JAI workflow. | JAI defines policy; it doesn't enforce policy by editing the codebase. |
| **S2** | **No `.env`, no `backend/.env`, no credentials.** Never read, write, or reference secret files. | Out of scope. Aligns with `Read(**/.env)` deny rules. |
| **S3** | **No Docker.** No start/stop/exec/rebuild, no edits to `docker-compose.yml` or Dockerfiles. | Container changes have user-visible side effects. |
| **S4** | **No migrations.** No `alembic`, no migration file edits, no schema changes. | DB state is irreversible. |
| **S5** | **No provider calls.** No outbound API calls to data providers, brokers, or external services. (Public docs/repos via WebFetch are allowed and read-only.) | Costs money/quota; out of scope. |
| **S6** | **No destructive cleanup.** No `rm -rf`, `git reset --hard`, dropped tables, deleted branches, `git clean -f`. Stale items get proposed in the report; Mitch actions. | Reversibility. |
| **S7** | **Approval before any file write.** Every JAI workflow returns its proposal in chat first; writes only after explicit `apply` / `do it` / `approved`. | Standing instruction. |
| **S8** | **No environment changes.** No `pip install`, no global config edits, no Claude settings edits, no scheduled-task creation, no hook installation. | Scoped extension of S1 to environment. |

---

## 2. Versioning

**Adding** to this list is a **MINOR** version bump (per `JAI_GOVERNANCE_STACK.md §3`) and follows the [Enhancement Intake workflow](../JAI_GOVERNANCE_STACK.md#4--enhancement-workflow).

**Renumbering, removing, or weakening** any rule is a **MAJOR** version bump.

---

## 3. Workflow-specific extensions

Some workflows layer additional execution-context safety rules on top of S1–S8. These extensions live in their respective specs, not in this canonical file:

- [`JAI_PROJECT_IMPLEMENTATION_PLAN.md §3`](../JAI_PROJECT_IMPLEMENTATION_PLAN.md#3--non-negotiable-safety-rules) defines four execution-context additions (E1–E4) covering credential specificity, branch deletion, Claude-settings changes, and hook installs. They apply only when running the implementation plan.
- [`JAI_RUNTIME_ADVISORY_REVIEWER.md §6`](../JAI_RUNTIME_ADVISORY_REVIEWER.md#6-non-negotiable-safety-rules) defines a *reviewer-checklist* of touchable areas the reviewer must verify were not modified. That list overlaps with S1–S8 in subject area but serves a different purpose (reviewer audit) and is not an extension of these canonical rules.

---

## 4. Cross-references (informative)

- `JAI_GOVERNANCE_STACK.md §6` — pointer to this file. The §6 anchor remains stable; cross-references to "S1–S8 from `JAI_GOVERNANCE_STACK.md §6`" still resolve correctly.
- `JAI_RESEARCH_BACKED_PLAN_MODE.md §5` — inherits S1–S8.
- `JAI_GOVERNANCE_REVIEW.md §5` — inherits S1–S8.
- `JAI_COMMANDS.md §2` — universal command safety rules; aligned with S1–S8 by content. Intentionally a recap for command-trigger ergonomics, not a re-declaration.

---

## 5. Maintenance rule

This file is the single source of truth for S1–S8. If a downstream file restates a rule from this list, that's a defect — it should reference instead. Detected via `JAI governance review` (`JAI_COMMANDS.md §15`).
