# Intake — Lift S1–S8 into policies/SAFETY_RULES.md

**Date:** 2026-05-10
**Source:** JAI Governance Review 2026-05-10 (STRUCT-2, STRUCT-3)
**Classification:** Universal rule
**Version impact:** MINOR
**Pilot project:** none required (global-only)
**Status:** Implemented — see [`JAI_CHANGELOG.md` v1.5 entry](../JAI_CHANGELOG.md). Standard update applied 2026-05-10 with Option A (impl-plan additions relabeled E1–E4).

## Problem

Three different safety-rule formulations exist in the global standard:

- `JAI_GOVERNANCE_STACK.md §6` defines **S1–S8** (8 rules).
- `JAI_PROJECT_IMPLEMENTATION_PLAN.md §3` defines a **different S1–S10** (different ordering, expanded list — adds S7 branch deletion, S8 Claude settings changes, S9 hook installs, S10 approval before file writes).
- `JAI_RUNTIME_ADVISORY_REVIEWER.md §6` "Non-Negotiable Safety Rules" uses **no S-numbering** at all (prose list).

A reader following one document gets a different mental model than a reader following another. v1.4's new files (`JAI_RESEARCH_BACKED_PLAN_MODE.md`, `JAI_GOVERNANCE_REVIEW.md`) explicitly cite "S1–S8 from `JAI_GOVERNANCE_STACK.md §6`" to dodge the ambiguity, but the underlying drift remains.

The v1.0 plan stated `policies/SAFETY_RULES.md` would lift §6 out for direct reference (see `policies/README.md:8`):

> `SAFETY_RULES.md` — the §6 absolutes from the governance doc, lifted out for direct reference.

Four minor versions later (v1.0 → v1.4) it is still empty.

## Proposal

1. **Create `policies/SAFETY_RULES.md`** containing the canonical S1–S8 list, lifted verbatim from `JAI_GOVERNANCE_STACK.md §6`. This is the canonical source going forward.
2. **Update `JAI_GOVERNANCE_STACK.md §6`** to either:
   - (a) keep §6 as the canonical text and have `policies/SAFETY_RULES.md` reference it; or
   - (b) move the canonical text to `policies/SAFETY_RULES.md` and have §6 reference *that*.
   - Recommendation: **(b)** — the policies directory was created at v1.0 specifically to host cross-cutting rules; this is its first real entry and validates the directory's existence.
3. **Update `JAI_PROJECT_IMPLEMENTATION_PLAN.md §3`** to reference `policies/SAFETY_RULES.md` rather than restating rules. Preserve the implementation-plan-specific additions (S7 branch deletion, S8 Claude settings, S9 hook installs, S10 approval) in a new "Execution-context safety rules" subsection that layers *on top of* the canonical S1–S8.
4. **Update `JAI_RUNTIME_ADVISORY_REVIEWER.md §6`** to reference `policies/SAFETY_RULES.md` rather than restating rules. Keep the prose framing but defer to the canonical list.

## Open decisions

- **OD-1.** Do the implementation plan's additional rules (S7 branch deletion, S8 settings, S9 hooks, S10 approval) get folded into the canonical S1–S8 (would require renumbering — MAJOR per `JAI_GOVERNANCE_STACK.md §3`), or stay in the implementation plan as execution-layer additions (MINOR — additive)?
  - **Recommendation:** keep them in the implementation plan as a separate "Execution-context safety rules" block. MINOR. Renumbering S1–S8 would invalidate v1.4's new files which already cite the current numbering.
- **OD-2.** Does this intake item bundle with `intake/2026-05-10-empty-subfolders-policy.md` (Draft 3 from the same governance review pass) or stay separate?
  - **Recommendation:** stay separate. This item ships the first `policies/` entry; the empty-subfolders item decides the *fate* of the other three subfolders. Different scopes.

## Risk

- **LOW.** Pure docs reorganization. No behavioral change. No safety rule is added, removed, weakened, or renumbered (per OD-1 recommendation).
- **Cross-reference impact:** v1.4's new files cite "S1–S8 from `JAI_GOVERNANCE_STACK.md §6`". If §6 becomes a reference rather than the canonical text, those citations should be updated to "S1–S8 from `policies/SAFETY_RULES.md` (referenced from `JAI_GOVERNANCE_STACK.md §6`)" — small edit, no semantic change.

## Migration impact

**None for any project.** Projects don't reference S-numbers directly in their `/jai/` profiles (verified across `Trend Scanner v1`, `PO3 Scanner v1`, `Solana Command Center v1` — none cite S1–S8 by number). The reorganization is invisible to project conformance checks.

## Pilot

None required. Global-only docs reorganization.

## Approval gate

This intake item is a **proposal**. Acting on it requires:

1. Operator approval of the four-step proposal above (and OD-1, OD-2).
2. Execution via `JAI standard update` (`JAI_COMMANDS.md §9`) — that command writes the actual files and bumps `JAI_VERSION` v1.4 → v1.5.

This intake file is read-only context until then.
