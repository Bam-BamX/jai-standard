# Enhancement Intake — JAI Agent Orchestrator Architecture (v1.7)

**Date:** 2026-05-11
**Status:** Implemented — see `JAI_CHANGELOG.md` v1.7
**Source:** Operator design session 2026-05-10 ("JAI as command structure, not just a prompt"). Plan-mode workflow at `~/.claude/plans/you-want-jai-to-enchanted-alpaca.md` served as consolidated Enhancement Intake + Standard Update review per operator confirmation at Gate 2.
**Classification:** global
**Risk level:** low

## 1. Problem Statement

JAI as of v1.6 is a docs-only command layer (`JAI_COMMANDS.md`: 11 formal commands in §3–§15 plus seven conversational aliases in §16). It tells *what* to do but does not formally separate *who* does each part of a multi-step change. In practice a single Main Claude does build, security review, and verification in one pass — collapsing roles that benefit from separation. The operator wants JAI to act as a command structure with explicit role separation, handoff contracts, and lane-aware approval gates.

## 2. Proposed Architecture (Conceptual Personas)

Four roles, conceptual personas one Main Claude session adopts in sequence (not literal Claude Code subagent delegation):

- **Orchestrator** (Main Claude) — owns plan, lane classification, restricted-area declaration, handoff contracts, final operator gate.
- **Agent 1 — Builder** — implementation; output shape per `JAI_DEV_RESPONSE_FORMAT.md §3`; verbose forbidden-actions repeated at point of action; no implicit command-execution permission (dual-approval gate: Orchestrator scope statement + project `/jai/WORKFLOW_RULES.md` pre-documentation).
- **Agent 2 — Security / Risk Reviewer** — checklists per `JAI_RUNTIME_ADVISORY_REVIEWER.md` §1, §5, §6 (Non-Negotiable Safety Rules), §8, §9, §11, §11.6 (Restricted-Area Verification), §11.7, §11.8, §11.9, §13; **hard read-prohibition** on restricted files (`.env`, credentials, secrets, mnemonics, tokens) — flags violations from diff/metadata, never opens restricted files to confirm.
- **Agent 3 — Test / Validation Reviewer** — review-only by default; may run tests under three-condition Strict Lane gate (operator explicit approval + WORKFLOW_RULES.md pre-documentation + no S1–S8 violation).

## 3. What This Reuses (No Duplication)

- Agent 2 = full reuse of `JAI_RUNTIME_ADVISORY_REVIEWER.md` checklists.
- Agent 1 = full reuse of `JAI_DEV_RESPONSE_FORMAT.md §3` ten-section output shape (Section 4 "What Was Not Touched", Section 5 "Verification Performed", etc.).
- S1–S8 = inherited verbatim from `policies/SAFETY_RULES.md` (no restatement in v1.7 to avoid drift; §6 of `JAI_AGENT_ARCHITECTURE.md` is pointer-only).
- v1.6 execution cadence = exact precedent for the v1.7 standard-update process.

## 4. What This Does NOT Add

- No new top-level command (§3–§15 unchanged).
- No new conversational alias (§16 unchanged).
- No `test_runner` skill (deferred to a future v1.x).
- No `.claude/agents/` files, `settings.json` changes, or hooks (operator chose conceptual-personas-only).
- No change to `JAI_RUNTIME_ADVISORY_REVIEWER.md` or `JAI_DEV_RESPONSE_FORMAT.md` (both referenced unchanged).
- No change to `policies/SAFETY_RULES.md` (S1–S8 unchanged).

## 5. Lane Logic Summary

- **Fast Lane** — docs-only, review-only, low-risk; single-role collapse permitted.
- **Strict Lane (explicit, non-exhaustive)** — source code, Docker, `.env`, migrations, providers, schedulers, dependencies, DB, Redis/Postgres, runtime state, deployment, **host-binding changes** (any port newly bound or rebound; any move from `127.0.0.1` toward `0.0.0.0` / `:::` is treated as Strict by default per the operator's local-dev IP/port binding safety baseline). Full Agent 1 → Agent 2 → Agent 3 → Orchestrator chain required with operator gate.
- **Borderline** — default Strict; operator may *confirm* Fast only when no actual Strict trigger applies; **actual triggers cannot be silently or operator-instructively downgraded**.

## 6. Files Created or Modified

**Created:**
- `JAI_AGENT_ARCHITECTURE.md` — the spec.
- `intake/2026-05-11-agent-orchestrator-architecture.md` — this file.

**Modified:**
- `JAI_VERSION` — `v1.6` → `v1.7`.
- `JAI_STANDARD.md` — version line bump + new "Global agent orchestrator architecture" pointer section.
- `JAI_COMMANDS.md §1` — file list + Roles bullet for the new architecture file.
- `JAI_CHANGELOG.md` — prepend v1.7 entry.

**NOT modified:** `JAI_RUNTIME_ADVISORY_REVIEWER.md`, `JAI_DEV_RESPONSE_FORMAT.md`, `policies/SAFETY_RULES.md`, `JAI_GOVERNANCE_STACK.md`, `JAI_PROJECT_IMPLEMENTATION_PLAN.md`, `JAI_RESEARCH_BACKED_PLAN_MODE.md`, `JAI_GOVERNANCE_REVIEW.md`, `JAI_FLEET_SYNC_AUDIT.md`, `JAI_PROJECT_REGISTRY.md`. All project `/jai/` files in Trend Scanner v1, PO3 Scanner v1, Solana Command Center v1.

## 7. Migration Impact

**None for any existing project.** Section is additive; no project-level `/jai/` shape change. Bumping a project's `jai_standard:` pointer to `v1.7` is opt-in conformance assertion only (same pattern as v1.1–v1.6).

## 8. Pilot

Trend Scanner v1, 1-week observation per `JAI_GOVERNANCE_STACK.md §4.5`. **Read-only observation only** — no project file changes during pilot. If friction surfaces (ambiguity, rule conflicts, role-boundary disputes), log as follow-up intake item `intake/2026-05-XX-v1.7-pilot-retro.md`.

## 9. Approval Trail

- Operator approved plan 2026-05-10 (with conditions: conceptual personas only, verbose Builder forbidden-actions, host-binding in Strict Lane, preflight verification gate).
- Operator approved revised draft 2026-05-10 (six substantive corrections + four collateral consistency edits applied).
- Operator approved citation precision fixes 2026-05-11 (after preflight surfaced `JAI_DEV_RESPONSE_FORMAT.md §5` mismatch — `§5` of file is "Visual Conventions" not "Verification Performed"; corrected to `§3 Section 5`).
- Operator gave explicit "go" 2026-05-11; v1.7 executed same day.

## 10. Approval Gate

This intake is closed. The implementation is documented in `JAI_CHANGELOG.md v1.7`. Future amendments to `JAI_AGENT_ARCHITECTURE.md` follow the standard JAI Enhancement Intake → Standard Update process per `JAI_COMMANDS.md §8` and `§9`.
