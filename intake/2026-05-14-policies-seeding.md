**Status:** Intake recorded (seed batch) on 2026-05-14; born in promoted location (consolidated Batch 3 intake record). **Spec files not yet copied.** Phase 3 requires separate explicit operator approval.

# Intake — Universal policies seeding (SECURITY_RULES, WORKFLOW_RULES, THREAT_MODEL_BASELINE, RESTRICTED_FILE_PATTERNS)

**Date:** 2026-05-14
**Source:** Sandbox sources at [`Repo_Lab/lab/jai-standard-prototype/policies/`](file:///C:/Users/Mitch/Projects/Repo_Lab/lab/jai-standard-prototype/policies/). Resolves the four policy phantom references introduced by Batch 2 ([`2026-05-14-foundational-specs-seeding.md`](./2026-05-14-foundational-specs-seeding.md) §"Cross-batch context" → Batch 3 row).
**Classification:** Universal policy files (4 new files in `policies/`)
**Version impact:** Deferred under seed-batch policy. `JAI_VERSION` held at `v1.7` across all seed batches; version cut planned at end of full vNEXT promotion sequence. **`JAI_CHANGELOG.md` is not edited in this batch.**
**Pilot project:** Sandbox-only validations to date — see Pilot § below. No live-project pilot in this batch.
**Status:** Sandbox sources at [`Repo_Lab/lab/jai-standard-prototype/policies/`](file:///C:/Users/Mitch/Projects/Repo_Lab/lab/jai-standard-prototype/policies/). **Policy files not yet copied.** This intake record establishes Batch 3 scope; Phase 3 (the actual copy) requires separate explicit operator approval.

---

## Problem

Batch 2 ([`2026-05-14-foundational-specs-seeding.md`](./2026-05-14-foundational-specs-seeding.md)) introduced four foundational governance specs (`JAI_OVERLAY_SCHEMA`, `JAI_OVERRIDE_CONTRACT`, `JAI_DRIFT_DETECTION`, `JAI_AUTOMATION_LANES`) that cross-link to four universal policy files at `~/.claude/jai-standard/policies/` which do not yet exist:

| Phantom reference | Referenced from (Batch 2) |
|---|---|
| `policies/SECURITY_RULES.md` (universal portion) | `JAI_AUTOMATION_LANES.md` (Blocked Lane), `JAI_OVERRIDE_CONTRACT.md` (NARROW examples) |
| `policies/WORKFLOW_RULES.md` (universal portion) | `JAI_OVERRIDE_CONTRACT.md` (AUGMENT examples) |
| `policies/THREAT_MODEL_BASELINE.md` | `JAI_DRIFT_DETECTION.md` (threat-coverage cross-check), `JAI_AUTOMATION_LANES.md` (Strict Lane threats) |
| `policies/RESTRICTED_FILE_PATTERNS.md` | `JAI_AUTOMATION_LANES.md` (Blocked Lane glob list); indirect cross-ref from `policies/SAFETY_RULES.md` S2 once Batch 3 lands |

Batch 2 Phase 3 (the spec copy) has not yet executed. Per Batch 2 OD-2 recommendation — "draft Batch 3 plan (read-only) before Batch 2 Phase 3 cp" — **Batch 3 should be sequenced before Batch 2 Phase 3** so the phantom-reference window never opens. Batch 3 Phase 1 read-only pre-promotion checks have passed; this intake record (Phase 2) authorizes scope only, not the copy.

## Proposal

Promote the four sandbox sources verbatim into `~/.claude/jai-standard/policies/`:

| Sandbox source | Target | Size |
|---|---|---|
| [`lab/jai-standard-prototype/policies/SECURITY_RULES.md`](file:///C:/Users/Mitch/Projects/Repo_Lab/lab/jai-standard-prototype/policies/SECURITY_RULES.md) | `~/.claude/jai-standard/policies/SECURITY_RULES.md` | 6 061 B |
| [`lab/jai-standard-prototype/policies/WORKFLOW_RULES.md`](file:///C:/Users/Mitch/Projects/Repo_Lab/lab/jai-standard-prototype/policies/WORKFLOW_RULES.md) | `~/.claude/jai-standard/policies/WORKFLOW_RULES.md` | 6 636 B |
| [`lab/jai-standard-prototype/policies/THREAT_MODEL_BASELINE.md`](file:///C:/Users/Mitch/Projects/Repo_Lab/lab/jai-standard-prototype/policies/THREAT_MODEL_BASELINE.md) | `~/.claude/jai-standard/policies/THREAT_MODEL_BASELINE.md` | 6 857 B |
| [`lab/jai-standard-prototype/policies/RESTRICTED_FILE_PATTERNS.md`](file:///C:/Users/Mitch/Projects/Repo_Lab/lab/jai-standard-prototype/policies/RESTRICTED_FILE_PATTERNS.md) | `~/.claude/jai-standard/policies/RESTRICTED_FILE_PATTERNS.md` | 3 904 B |

Total payload ≈ 23 458 B. Pure addition — **no edits to existing global files in this batch.**

## Source disambiguation (important)

Two candidate source trees exist in the sandbox. **Only the standard-prototype tree is the promotion source.**

| Path | Role | Promotion source? |
|---|---|---|
| [`lab/jai-standard-prototype/policies/`](file:///C:/Users/Mitch/Projects/Repo_Lab/lab/jai-standard-prototype/policies/) | **Universal policy drafts** intended for `~/.claude/jai-standard/policies/`. | **YES.** |
| [`lab/jai-overlay-prototype/`](file:///C:/Users/Mitch/Projects/Repo_Lab/lab/jai-overlay-prototype/) | Synthetic **project overlay** sample (`SECURITY_RULES.md` 1 303 B, `WORKFLOW_RULES.md` 2 427 B, `THREAT_MODEL.md` 1 826 B). Demonstrates the NARROW/AUGMENT/WEAKEN overlay shape against a hypothetical project. | **NO.** Overlay-shaped, project-scoped, smaller-by-design — not the universal portion. Promoting the overlay files would invert the policy hierarchy. |

The overlay-prototype tree retains its sandbox role as the worked example for `JAI_OVERLAY_SCHEMA.md` validation (per Batch 2 Pilot §) and is not modified by this batch.

## Cross-batch context

**Batch 1 (closed 2026-05-14):** Seeded `catalogs/` (5 files), `templates/` (2 files), `intake/` archives. `JAI_VERSION` held at `v1.7`.

**Batch 2 (intake recorded 2026-05-14, Phase 3 pending):** Scoped four foundational governance specs. Phase 3 copy not yet executed. The four phantom references this intake resolves originate in Batch 2 specs; resolving them **before** Batch 2 Phase 3 closes the phantom-reference window before it ever opens.

**Recommended sequencing:** Batch 3 Phase 3 → Batch 2 Phase 3. Both require separate explicit operator approval; this intake doc authorizes neither.

**Subsequent seed batches (scoped here for visibility only, not authorized):**

- **Batch 4** — 6 lifted/new skills (`planning`, `security_review`, `secret_scanning`, `threat_modeling`, `context_budget`, `drift_audit`). Depends on Batch 1 catalogs + Batch 3 policies (this batch) being live.
- **Batch 5** — remaining sandbox intake-draft archives.

## Open decisions

- **OD-1.** None. Batch 3 scope is fixed by the four phantom references catalogued in Batch 2. No new policy file is added beyond closing those references.
- **OD-2.** Confirm Batch 3 Phase 3 executes **before** Batch 2 Phase 3, per Batch 2 OD-2 recommendation. *Recommendation: yes.* Operator decision at the next approval gate.

## Risk

- **LOW.** Pure addition; no edits to existing global files in this batch. `JAI_VERSION` unchanged. `JAI_CHANGELOG.md`, `JAI_STANDARD.md`, `JAI_COMMANDS.md` unchanged. No existing policy file is overwritten (none exist in `policies/` yet beyond the existing `SAFETY_RULES.md` which this batch does not touch).
- **Source-confusion risk.** Documented above (Source disambiguation §). The smaller `lab/jai-overlay-prototype/` files are an overlay sample, not the universal portion. Mitigation: this intake doc names the correct source paths explicitly; any operator approving Phase 3 should verify the byte counts in the Proposal § match the standard-prototype sources, not the overlay-prototype counterparts.
- **Re-vetting risk.** Policies encode universal rules that may evolve as new attack patterns or workflow constraints emerge. Mitigation: each policy will carry its own re-vetting cadence note (covered in the sandbox sources).

## Migration impact

- **No project required to migrate in this batch.** SCC, Trend Scanner, and PO3 Scanner continue operating against their current `<project>/jai/` overlays. Project-specific narrowing/augmenting/weakening of these universal policies is the subject of the deferred `JAI_MIGRATION_PLAYBOOK.md` (Batch 2 Deferred items §).
- **No edit to existing global files in this batch.** Once Batch 3 closes, three follow-on amendments become foreseeable (each requires its own separate intake later):
  1. `JAI_STANDARD.md` required-files list grows by 4.
  2. `policies/SAFETY_RULES.md` S2 cross-references `RESTRICTED_FILE_PATTERNS.md` (the indirect ref noted in the phantom-reference table).
  3. `JAI_CHANGELOG.md` records the `policies/` seeding at the next version cut.

## Pilot

- **`SECURITY_RULES.md`** validated against [`lab/jai-overlay-prototype/SECURITY_RULES.md`](file:///C:/Users/Mitch/Projects/Repo_Lab/lab/jai-overlay-prototype/SECURITY_RULES.md) as the NARROW/AUGMENT example for `JAI_OVERRIDE_CONTRACT.md`.
- **`WORKFLOW_RULES.md`** validated against [`lab/jai-overlay-prototype/WORKFLOW_RULES.md`](file:///C:/Users/Mitch/Projects/Repo_Lab/lab/jai-overlay-prototype/WORKFLOW_RULES.md) as the AUGMENT example for `JAI_OVERRIDE_CONTRACT.md`.
- **`THREAT_MODEL_BASELINE.md`** cross-referenced from `JAI_DRIFT_DETECTION.md` threat-coverage cross-check; sandbox walkthrough in [`lab/findings/edge-cases.md`](file:///C:/Users/Mitch/Projects/Repo_Lab/lab/findings/edge-cases.md).
- **`RESTRICTED_FILE_PATTERNS.md`** consumed by `JAI_AUTOMATION_LANES.md` Blocked Lane glob list; sandbox walkthrough in [`lab/findings/architecture-walkthrough.md`](file:///C:/Users/Mitch/Projects/Repo_Lab/lab/findings/architecture-walkthrough.md).

No live-project pilot in Batch 3; live-project pilot is reserved for the deferred `JAI_MIGRATION_PLAYBOOK.md`.

## Approval gate

Phase 3 of Batch 3 (the actual `cp` of the four policy files into `~/.claude/jai-standard/policies/`) requires **separate, explicit operator approval** per the per-phase approval pattern established by OQ-4 and applied across Batch 1 and Batch 2. This intake doc on its own does **not** authorize the copy.

This batch follows the same seed-batch pattern as Batch 1 and Batch 2: intake-record-first, copy-second, version-cut-deferred to the end of the full vNEXT promotion sequence.

---

**Format note.** This is a born-in-place intake doc; no sandbox original exists at `lab/intake-drafts/`. The four sandbox policy sources listed under **Proposal** above remain in the lab as the cited inputs and are not modified by this batch.
