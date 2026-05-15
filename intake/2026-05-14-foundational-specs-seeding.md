**Status:** Promoted (seed batch) on 2026-05-14; born in promoted location (consolidated Batch 2 intake record).

# Intake — Foundational specs seeding (JAI_OVERLAY_SCHEMA, JAI_OVERRIDE_CONTRACT, JAI_DRIFT_DETECTION, JAI_AUTOMATION_LANES)

**Date:** 2026-05-14
**Source:** Plan `role-developer-task-research-shimmering-anchor.md` §"Proposed Global JAI Standard Structure". Consolidates the four per-spec sandbox intake drafts:

- [`lab/intake-drafts/2026-05-14-overlay-schema.md`](file:///C:/Users/Mitch/Projects/Repo_Lab/lab/intake-drafts/2026-05-14-overlay-schema.md)
- [`lab/intake-drafts/2026-05-14-override-contract.md`](file:///C:/Users/Mitch/Projects/Repo_Lab/lab/intake-drafts/2026-05-14-override-contract.md)
- [`lab/intake-drafts/2026-05-14-drift-detection.md`](file:///C:/Users/Mitch/Projects/Repo_Lab/lab/intake-drafts/2026-05-14-drift-detection.md)
- [`lab/intake-drafts/2026-05-14-automation-lanes.md`](file:///C:/Users/Mitch/Projects/Repo_Lab/lab/intake-drafts/2026-05-14-automation-lanes.md)

**Classification:** Top-level governance specs (4 new files)
**Version impact:** Deferred under seed-batch policy. `JAI_VERSION` held at `v1.7` across all seed batches; version cut planned at end of full vNEXT promotion sequence.
**Pilot project:** Sandbox-only validations to date — see Pilot § below. No live-project pilot in this batch.
**Status:** Sandbox sources at [`Repo_Lab/lab/jai-standard-prototype/`](file:///C:/Users/Mitch/Projects/Repo_Lab/lab/jai-standard-prototype/). **Spec files not yet copied.** This intake record establishes Batch 2 scope; Phase 3 (the actual copy) requires separate explicit operator approval.

---

## Problem

`~/.claude/jai-standard/` at v1.7 lacks four foundational governance specs called for in the vNEXT-draft redesign:

1. **`JAI_OVERLAY_SCHEMA.md`** — formal contract for what a project's `<project>/jai/` MUST/MAY/MUST-NOT contain. Without it, projects keep inventing per-project overlay shapes (observed: SCC, Trend, PO3 are not version-aligned).
2. **`JAI_OVERRIDE_CONTRACT.md`** — NARROW/AUGMENT/WEAKEN semantics for project deviations from universal rules. Without it, deviations are ad-hoc and hard to audit.
3. **`JAI_DRIFT_DETECTION.md`** — Drift Manifest schema + audit semantics. Without it, the Drift Manifest template promoted in **Batch 1** has no specifying authority.
4. **`JAI_AUTOMATION_LANES.md`** — Manual / Fast / Review / Strict / Blocked lane contract. Without it, automation-friction decisions live in operators' heads, not docs.

## Proposal

Promote the four sandbox sources verbatim into `~/.claude/jai-standard/`:

| Sandbox source | Target | Size |
|---|---|---|
| [`lab/jai-standard-prototype/JAI_OVERLAY_SCHEMA.md`](file:///C:/Users/Mitch/Projects/Repo_Lab/lab/jai-standard-prototype/JAI_OVERLAY_SCHEMA.md) | `~/.claude/jai-standard/JAI_OVERLAY_SCHEMA.md` | 9 545 B |
| [`lab/jai-standard-prototype/JAI_OVERRIDE_CONTRACT.md`](file:///C:/Users/Mitch/Projects/Repo_Lab/lab/jai-standard-prototype/JAI_OVERRIDE_CONTRACT.md) | `~/.claude/jai-standard/JAI_OVERRIDE_CONTRACT.md` | 12 175 B |
| [`lab/jai-standard-prototype/JAI_DRIFT_DETECTION.md`](file:///C:/Users/Mitch/Projects/Repo_Lab/lab/jai-standard-prototype/JAI_DRIFT_DETECTION.md) | `~/.claude/jai-standard/JAI_DRIFT_DETECTION.md` | 11 321 B |
| [`lab/jai-standard-prototype/JAI_AUTOMATION_LANES.md`](file:///C:/Users/Mitch/Projects/Repo_Lab/lab/jai-standard-prototype/JAI_AUTOMATION_LANES.md) | `~/.claude/jai-standard/JAI_AUTOMATION_LANES.md` | 17 300 B |

Total payload ≈ 50 341 B. Pure addition — **no edits to existing global files in this batch.**

## Batch 0 operator decisions baked into Batch 2 specs

| OQ | Decision | Where reflected |
|---|---|---|
| OQ-1 | Drift audit cadence = monthly + on-demand | `JAI_DRIFT_DETECTION.md §4` |
| OQ-4 | Push approval always separate per batch; never coupled to commit approval | `JAI_AUTOMATION_LANES.md §2.2` (Fast Lane) |
| OQ-5 | Confidence ≥80% default; Critical/High surfaced regardless | `JAI_AUTOMATION_LANES.md` (Strict Lane gating) |
| OQ-9 | Hooks deferred until `JAI_HOOK_CONTRACT.md` is separately drafted and approved | No hook installation in any Batch 2 spec; references to hooks are marked "(deferred until `JAI_HOOK_CONTRACT.md` is approved)" |
| OQ-17 | Expired-exception escalation = 3 consecutive monthly audits → blocking | `JAI_DRIFT_DETECTION.md` exception-lifecycle section |

## Cross-batch context

**Batch 1 (closed 2026-05-14):** Seeded `catalogs/` (5 files), `templates/` (2 files), and `intake/` (2 archived per-topic intake docs). `JAI_VERSION` held at `v1.7`. The Drift Manifest template promoted in Batch 1 depends on `JAI_DRIFT_DETECTION.md` (this batch) for its specifying authority; closing that loop is one of Batch 2's goals.

**Batch 3 (not yet planned, scoped here for visibility):** Must resolve the **four policy phantom references** that Batch 2 specs introduce. The Batch 2 specs cross-link to these policy files as if they exist at `~/.claude/jai-standard/policies/`:

| Phantom reference | Referenced from (Batch 2) |
|---|---|
| `policies/SECURITY_RULES.md` (universal portion) | `JAI_AUTOMATION_LANES.md` (Blocked Lane), `JAI_OVERRIDE_CONTRACT.md` (NARROW examples) |
| `policies/WORKFLOW_RULES.md` (universal portion) | `JAI_OVERRIDE_CONTRACT.md` (AUGMENT examples) |
| `policies/THREAT_MODEL_BASELINE.md` | `JAI_DRIFT_DETECTION.md` (threat-coverage cross-check), `JAI_AUTOMATION_LANES.md` (Strict Lane threats) |
| `policies/RESTRICTED_FILE_PATTERNS.md` | `JAI_AUTOMATION_LANES.md` (Blocked Lane glob list); indirect cross-ref from `policies/SAFETY_RULES.md` S2 once Batch 3 lands |

Until Batch 3 promotes those four policy files, Batch 2 specs will contain **phantom references** — links to files that do not yet exist in `~/.claude/jai-standard/policies/`. This is acceptable under the seed-batch sequencing strategy, but operators reading Batch 2 specs in the interim should know that "links resolve once Batch 3 lands." This intake doc records the obligation so Batch 3 cannot be inadvertently skipped.

**Subsequent seed batches (scoped here for visibility only, not authorized):**

- **Batch 4** — 6 lifted/new skills (`planning`, `security_review`, `secret_scanning`, `threat_modeling`, `context_budget`, `drift_audit`). Depends on Batch 1 catalogs + Batch 3 policies being live.
- **Batch 5** — remaining sandbox intake-draft archives (`policies-universal-portions`, `overlay-schema`, `override-contract`, `drift-detection`, `automation-lanes`, `skills-templates-lift`, `skill-eval-framework`).

## Deferred items (mentioned only — no separate intake stub created)

- **`JAI_MIGRATION_PLAYBOOK.md`** — explicit per-project (SCC, Trend, PO3) migration playbook for adopting vNEXT specs. **Deferred.** Per operator direction, this intake doc **mentions** the deferral but does **not** create a separate migration-playbook intake stub. The playbook will be filed as its own intake item only after Batches 2–5 close and at least one live project (likely Trend Scanner v1) attempts migration in practice — at which point real-world gaps will inform the playbook. Recording here for traceability so the obligation is not lost.

## Open decisions

- **OD-1.** None for Batch 2 — all gating decisions are already covered by Batch 0 (OQ-1, OQ-4, OQ-5, OQ-9, OQ-17). Sandbox specs already reflect them.
- **OD-2.** Sequencing safety: should Batch 3 (policies) plan be drafted **before** Phase 3 of Batch 2 (the spec copy) executes, in order to shorten the phantom-reference window? *Recommendation: yes — draft Batch 3 plan (read-only) before Batch 2 Phase 3 cp.* Operator decision.

## Risk

- **LOW–MEDIUM.** Pure addition; no edits to existing global files in this batch. The phantom-reference window opens at Batch 2 Phase 3 close and closes at Batch 3 close.
- **Re-vetting risk.** Batch 2 specs encode the Batch 0 operator decisions. If any Batch 0 decision is later revised, the corresponding spec section must update. Mitigation: each spec section that depends on a Batch 0 decision is annotated with its OQ number.

## Migration impact

- **No project required to migrate in this batch.** SCC, Trend, PO3 continue operating against their current `<project>/jai/` overlays. Live-project migration is the subject of the deferred `JAI_MIGRATION_PLAYBOOK.md`.
- **No edit to existing global files in this batch.** Three downstream amendments are foreseeable but **not scoped here** (each will require its own separate intake later):
  1. `JAI_STANDARD.md` required-files list grows by 4 once Batch 2 closes.
  2. `JAI_FLEET_SYNC_AUDIT.md` needs to learn Drift Manifest reading.
  3. `JAI_RUNTIME_ADVISORY_REVIEWER.md` adopts the confidence-threshold + "what NOT to flag" patterns surfaced in `JAI_AUTOMATION_LANES.md`.

## Pilot

- **`JAI_OVERLAY_SCHEMA.md`** validated against [`Repo_Lab/lab/jai-overlay-prototype/`](file:///C:/Users/Mitch/Projects/Repo_Lab/lab/jai-overlay-prototype/) (synthetic overlay).
- **`JAI_OVERRIDE_CONTRACT.md`** NARROW/AUGMENT/WEAKEN semantics walked through in [`lab/findings/architecture-walkthrough.md`](file:///C:/Users/Mitch/Projects/Repo_Lab/lab/findings/architecture-walkthrough.md).
- **`JAI_DRIFT_DETECTION.md`** audit-loop walked through in [`lab/findings/edge-cases.md`](file:///C:/Users/Mitch/Projects/Repo_Lab/lab/findings/edge-cases.md) (EC-1 through EC-8).
- **`JAI_AUTOMATION_LANES.md`** lane-routing walked through in [`lab/findings/architecture-walkthrough.md`](file:///C:/Users/Mitch/Projects/Repo_Lab/lab/findings/architecture-walkthrough.md).

No live-project pilot in Batch 2; live-project pilot is reserved for the deferred `JAI_MIGRATION_PLAYBOOK.md`.

## Approval gate

Phase 3 of Batch 2 (the actual `cp` of the four spec files) requires **separate, explicit operator approval** per the per-phase approval pattern established by OQ-4 and applied across Batch 1. This intake doc on its own does **not** authorize the copy.

Recommended sequencing (per OD-2 above): draft Batch 3 plan (read-only pre-promotion checks) **before** Batch 2 Phase 3 executes, so the phantom-reference window is minimized.

---

**Format note.** This is a born-in-place intake doc; no sandbox original exists at `lab/intake-drafts/`. The four per-spec sandbox drafts listed under **Source** above remain in the lab as the cited inputs and are not modified by this batch.
