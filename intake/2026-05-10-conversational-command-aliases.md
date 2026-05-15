# Enhancement Intake — Conversational Command Aliases

**Date:** 2026-05-10
**Status:** Implemented — see `JAI_CHANGELOG.md` v1.6
**Source:** Operator design session 2026-05-10 ("JAI Command Menu v1"). Collision with existing `JAI_COMMANDS.md` surfaced during the install step and routed here per `JAI_COMMANDS.md §8`.
**Classification:** global
**Risk level:** low

## 1. Problem Statement
The current formal JAI command set (`JAI_COMMANDS.md` §3–§15, 11 numbered commands) is rigorous and well-governed, but the trigger phrases are long and prescriptive (e.g., `Run JAI external repo vetting on <repo-url>.`). For light navigation during early-stage exploration — explaining concepts, mapping raw ideas, surfacing gaps, scanning opportunities, classifying risk pre-action, drafting handoffs — there is no shorthand layer. Operators currently improvise framing language, which weakens consistency and creates room for unintended scope creep.

## 2. Why Existing Formal Commands Are Not Enough for Quick Navigation
- The existing 11 commands are designed for **work units**, not navigation. Each has its own required reads list, forbidden actions list, required output shape, and approval gate.
- Several common review-only needs are not covered as a first-class frame: plain-English explanation, idea-to-structure mapping, gap-finding in a draft doc, opportunity scanning, feature scope-fit evaluation, pre-action risk classification, and dev-handoff briefing.
- For these the operator must either invoke a heavier formal command (overkill) or restate the desired frame in free text every time (inconsistent, bypasses review-only defaults).
- New shorthands are not meant to replace formal commands. They are **conversational navigation frames** that route any work touching real artifacts back through the existing §3–§15 formal commands.

## 3. Proposed Conversational Aliases
Format: `JAI: <Alias> — <input>`. All review-only by default. None authorize actions restricted by S1–S8 (`policies/SAFETY_RULES.md`).

| Alias | Frame | Default lane | Routes work to |
|---|---|---|---|
| `JAI: Explain` | Plain-English explanation of a JAI concept, rule, project, or term. | Review-only | n/a — terminal frame |
| `JAI: Map Idea` | Map a raw idea onto JAI structure: nearest project, lane suggestion, dependencies, conflicts, open questions. | Review-only | If user pursues → `JAI research-backed plan` (§14) |
| `JAI: Missing Pieces` | Compare a draft doc/plan/handoff against JAI completeness criteria; list gaps with severity tags. | Review-only | n/a — terminal frame |
| `JAI: Opportunity Scan` | Surface improvements, reusable patterns, missing systems, workflow gaps. Output per opportunity: Opportunity · Why it matters · Candidate project/layer · Risk class · Safest next action. | Review-only | If pursued → `JAI enhancement intake` (§8) or `JAI research-backed plan` (§14) |
| `JAI: Feature Fit` | Evaluate whether a feature fits a project's scope/lane/governance. Verdict: accept · defer · reject · split. | Review-only | If accepted → `JAI research-backed plan` (§14), then per-project skill |
| `JAI: Risk Gate` | Pre-action risk classification. Output: risk class (A/B/C/D) · applicable S1–S8 rules · required approvals · safer alternatives. | Strict (review-only by definition; runs *before* action) | n/a — terminal frame; downstream action via the appropriate formal command |
| `JAI: Dev Handoff` | Produce a structured handoff brief in the standard JAI format. Inline only — file write is a separate explicit ask. | Strict (output freezes scope) | If executed → per-project skill |

**Lane definitions:**
- **Review-only** — analysis/text only; no implied execution path.
- **Strict** — output freezes scope or precedes risky action; explicit confirmation required before any downstream write or implementation.

## 4. Mapping to Existing Formal Commands

| Conversational shorthand | Existing formal equivalent | Resolution |
|---|---|---|
| `JAI: Repo Fit` | `JAI external repo vetting` (§7) | **Not a separate alias.** Routes to and follows §7 verbatim. |
| `JAI: Fleet Sync` | `JAI fleet sync audit` (§6) | **Not a separate alias.** Routes to and follows §6 verbatim. |
| `JAI: Status` | `JAI status report` (§10) | Same routing pattern. |
| `JAI: Plan` / `JAI: Research-Backed Plan` | `JAI research-backed plan` (§14) | Same routing pattern. |
| `JAI: Reviewer` / `JAI: Advisory` | `JAI runtime advisory review` (§11) | Same routing pattern. |

**Net new aliases:** 7. **Aliases routed to existing commands:** all overlapping shorthands.

## 5. Collision Analysis
- **Naming.** None of the seven new aliases collide with an existing §3–§15 trigger phrase.
- **Behavior.** Each new alias is review-only by default. None authorize actions that fall under S1–S8. None create a parallel write path.
- **Governance.** No new alias modifies the global standard, project `/jai/`, `JAI_VERSION`, or `JAI_CHANGELOG.md`. No new approval gates conflict with existing ones.
- **§-numbering.** Aliases live in a single new §16 "Conversational Aliases" clearly labeled as conversational navigation frames, not formal commands.
- **Operator-confusion mitigation.** §16 header explicitly states aliases are navigation frames, not formal commands; aliases inherit S1–S8 unchanged; for any work that would touch real artifacts, route to the named formal command in §3–§15.

## 6. ST-10 Resolution
**ST-10 is preserved as a response mode inside `JAI_DEV_RESPONSE_FORMAT.md § Response Modes`.** It is **not** promoted to a top-level numbered command and **not** registered as an alias in §16. This preserves the v1.3 governance decision recorded in `JAI_CHANGELOG.md`.

Allowed conversational usage:
- `ST-10` — operator invocation, existing v1.3 behavior, unchanged.
- `JAI: Explain — ST-10 …` — Explain alias rendered in ST-10 mode.
- `JAI: Map Idea — explain in ST-10 format` — same pattern; any alias may carry an ST-10 modifier.

Not proposed:
- Registering `JAI: Simplify / ST-10` as a formal top-level command. That would directly contradict `JAI_COMMANDS.md §1` and `JAI_CHANGELOG.md v1.3`. **Excluded from this intake.**

## 7. Safety Boundaries
This intake and any future implementation of it inherit S1–S8 (`policies/SAFETY_RULES.md`) unchanged. Each alias's `forbidden actions` mirrors the universal safety rules in `JAI_COMMANDS.md §2` and adds no new permissions:
- No `.env` reads/writes. No credentials/tokens/keys/secrets/mnemonics/provider-config inspection unless explicitly approved.
- No Docker lifecycle commands. No migrations. No provider calls. No scheduler edits. No app-code changes.
- No hook installs. No Claude settings changes. No dependency installs.
- No destructive cleanup. No branch deletion, commits, pushes, or force operations.
- No file writes until explicitly approved.

## 8. Recommendation — Should This Become JAI v1.6?
**Recommend: yes, ship as v1.6** on the same additive pattern as v1.1–v1.5.
- Purely additive — no existing rule, command, or section is changed.
- No project-level `/jai/` shape change → no project migration required.
- Inherits S1–S8 unchanged; weakens nothing.
- Resolves a real friction point without competing with existing formal commands.
- Preserves the v1.3 ST-10 decision; routes overlapping shorthands to existing formal commands.

**Version impact:** v1.6 minor (additive).
**Pilot project recommendation:** none — global-only standard change. Same scope pattern as v1.3 and v1.4.
**Migration impact for existing projects:** none. Bumping a project's pointer to `v1.6` is opt-in conformance assertion only.

## 9. Required Files If Approved Later
**Modified:**
- `JAI_COMMANDS.md` — add §16 (seven aliases). One-sentence and one-line edits in §1. One row added to §12.
- `JAI_VERSION` — `v1.5` → `v1.6`.
- `JAI_CHANGELOG.md` — prepend `v1.6 — 2026-05-10` entry.
- `JAI_STANDARD.md` — bump `**Current version:**` line to `v1.6`.

**New files:** `intake/2026-05-10-conversational-command-aliases.md` (this file).

**Files NOT changed:** `JAI_DEV_RESPONSE_FORMAT.md`, `policies/SAFETY_RULES.md`, `JAI_GOVERNANCE_STACK.md`, `JAI_RUNTIME_ADVISORY_REVIEWER.md`, `JAI_RESEARCH_BACKED_PLAN_MODE.md`, `JAI_GOVERNANCE_REVIEW.md`, `JAI_PROJECT_IMPLEMENTATION_PLAN.md`, `JAI_FLEET_SYNC_AUDIT.md`, `JAI_PROJECT_REGISTRY.md`. All project `/jai/` files in Trend Scanner v1, PO3 Scanner v1, Solana Command Center v1.

## 10. Changelog / Version-Bump Implications
See `JAI_CHANGELOG.md` v1.6 entry for the executed text.

## Approval Gate
Operator approved this intake on 2026-05-10. Implementation executed via `Run JAI standard update for approved enhancement conversational-command-aliases.` (§9). Status updated from `Open — proposal recorded` to `Implemented — see JAI_CHANGELOG.md v1.6` at the time of the v1.6 batch.
