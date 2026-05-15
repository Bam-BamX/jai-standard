# JAI Changelog

One entry per version of the [JAI Standard](./JAI_STANDARD.md). Newest first.

Entry format:
- **Version + date.**
- **Summary.** One sentence.
- **Added / Changed / Removed.** Concrete file-level deltas.
- **Source.** What triggered the change (intake item, external repo, incident, ad-hoc).
- **Migration.** What conforming projects need to do; "none" for purely additive changes that ship with skip-if-missing fallback.

---

## v1.7 — 2026-05-11

**Summary.** Adds `JAI_AGENT_ARCHITECTURE.md` defining the four-role conceptual model under which any JAI-conformant assistant operates when a task crosses build → review → validate. The four roles — Orchestrator (Main Claude), Agent 1 (Builder), Agent 2 (Security / Risk Reviewer), Agent 3 (Test / Validation Reviewer) — are conceptual personas one Main Claude session adopts in sequence, not literal Claude Code subagent delegation. No `.claude/agents/` files, no `settings.json` changes, no hooks. Agent 2 reuses `JAI_RUNTIME_ADVISORY_REVIEWER.md` checklists in full (§1, §5, §6, §8, §9, §11, §11.6–§11.9, §13); Agent 1 reuses `JAI_DEV_RESPONSE_FORMAT.md §3` ten-section output shape in full; S1–S8 from `policies/SAFETY_RULES.md` inherited verbatim. No new top-level command in `JAI_COMMANDS.md §3–§15` and no new conversational alias in §16 — the architecture is a conceptual layer above the existing command set, not a replacement.

**Added.**
- `JAI_AGENT_ARCHITECTURE.md` — full spec. Sections: §0 Purpose, §1 Relationship to Other JAI Files, §2 When to Use, §3 The Four Roles (Orchestrator + three specialist personas; verbose Builder forbidden-actions list repeated at point of action; Security Reviewer with hard read-prohibition on restricted files; Test/Validation Reviewer review-only by default with three-condition Strict Lane test-execution gate), §4 Handoff Contracts (5 subsections defining each role-to-role boundary; halt on `Rejected` / `Hold` / `Correction required`), §5 Lane Logic (Fast vs Strict with explicit host-binding-changes inclusion; actual Strict triggers cannot be downgraded by operator instruction), §6 Forbidden Actions (pointer-only to `policies/SAFETY_RULES.md`; no S1–S8 restatement to avoid drift), §7 Maintenance Rule.
- `intake/2026-05-11-agent-orchestrator-architecture.md` — operator-approved enhancement intake that proposed the architecture. Recorded with status `Implemented — see JAI_CHANGELOG.md v1.7`.
- `JAI_STANDARD.md` — new "Global agent orchestrator architecture" pointer section added after the v1.4 "Global governance review" pointer (mirrors v1.4's pattern of adding a short pointer for new top-level files).

**Changed.**
- `JAI_VERSION` — `v1.6` → `v1.7`.
- `JAI_STANDARD.md` — `**Current version:**` line bumped to `v1.7`.
- `JAI_COMMANDS.md §1` — file list extended with `JAI_AGENT_ARCHITECTURE.md`. New Roles bullet added describing the file (placed before the v1.6 §16-aliases note since the architecture file is an external standard file, while the §16 note is internal to `JAI_COMMANDS.md`). No changes to §3–§15 formal commands. No changes to §16 conversational aliases.

**Removed.** N/A.

**Source.** Operator session 2026-05-10 — design pass for "JAI as command structure, not just a prompt." Extensive plan-mode review with multiple correction cycles: six substantive content corrections (Builder command-execution gating, Security Reviewer hard read-prohibition on restricted files, halt-set extension to include `Correction required`, removal of S1–S8 summaries from §6 to avoid drift, Borderline-cases anti-downgrade clarification, preflight extension), four collateral consistency edits, and two preflight citation precision fixes (resolving `JAI_DEV_RESPONSE_FORMAT.md §5` → `§3 Section 5` confusion and `§6` parenthetical relabeling). Operator approved 2026-05-10; executed 2026-05-11 per locked date convention (execution date for filenames/headings, approval date for prose).

**Migration.** **None for any existing project.** Section is additive; no project-level `/jai/` shape change. Existing projects continue to declare their existing `jai_standard:` versions; bumping a project's pointer to `v1.7` is an opt-in conformance assertion, not a functional requirement (same pattern as v1.1–v1.6). Pilot observation period (1 week, Trend Scanner v1) per `JAI_GOVERNANCE_STACK.md §4.5` — read-only observation only, no project file changes during the pilot.

---

## v1.6 — 2026-05-10

**Summary.** Adds §16 "Conversational Aliases" to `JAI_COMMANDS.md` — seven conversational navigation frames (Explain, Map Idea, Missing Pieces, Opportunity Scan, Feature Fit, Risk Gate, Dev Handoff). Aliases are not formal commands and do not replace or extend the §3–§15 command set. Overlapping shorthands (Repo Fit, Fleet Sync, Status, Plan / Research-Backed Plan, Reviewer / Advisory) route to their existing formal counterparts and follow those specs verbatim. ST-10 remains a response mode inside `JAI_DEV_RESPONSE_FORMAT.md § Response Modes` per the v1.3 decision and is intentionally NOT promoted to a top-level command and NOT registered as an alias in §16.

**Added.**
- `JAI_COMMANDS.md §16` — Conversational Aliases. Header explicitly labels the section as conversational navigation frames (not formal commands); states that aliases inherit S1–S8 (`policies/SAFETY_RULES.md`) and the universal command safety rules (§2) unchanged; defines the lane vocabulary (Review-only, Strict); provides the overlap-routing table. §16.1–§16.7 define each alias's frame · default lane · allowed actions · forbidden actions · output shape · example prompt · routing target. Closing notes cover ST-10-within-alias usage and unknown-alias confirmation behavior.
- `intake/2026-05-10-conversational-command-aliases.md` — operator-approved enhancement intake that proposed §16. Recorded with status `Implemented — see JAI_CHANGELOG.md v1.6`.

**Changed.**
- `JAI_VERSION` — `v1.5` → `v1.6`.
- `JAI_STANDARD.md` — `**Current version:**` line bumped to `v1.6`.
- `JAI_COMMANDS.md §1` — ST-10 sentence updated from `does not appear in §3–§11` to `does not appear in §3–§15 and is not an alias in §16` to stay accurate after the §14, §15, §16 additions. One line added to the Roles subsection noting that §16 contains conversational aliases.
- `JAI_COMMANDS.md §12` — summary table extended with one row for §16 (review-only by default; aliases that route to a formal command inherit that command's approval rules).

**Removed.** N/A.

**Source.** Operator session 2026-05-10. Conversational shorthand layer was designed during a "JAI Command Menu v1" pass; collision with existing `JAI_COMMANDS.md` surfaced during the install step and was routed through Enhancement Intake (`intake/2026-05-10-conversational-command-aliases.md`). The intake explicitly excluded promoting ST-10 to a top-level command, preserving the v1.3 governance decision recorded in this changelog.

**Migration.** **None for any existing project.** Section is additive; no project-level `/jai/` shape change. Existing projects continue to declare their existing `jai_standard:` versions; bumping a project's pointer to `v1.6` is an opt-in conformance assertion, not a functional requirement (same pattern as v1.1, v1.2, v1.3, v1.4, v1.5). Aliases are inherited globally and need no project-level scaffolding.

---

## v1.5 — 2026-05-10

**Summary.** Lifts the canonical S1–S8 safety rules out of `JAI_GOVERNANCE_STACK.md §6` into a new `policies/SAFETY_RULES.md` (the first real entry in the `policies/` subdirectory since v1.0). Resolves the structural finding STRUCT-2 from the JAI Governance Review on 2026-05-10 — three different safety-rule formulations existed across the standard. After this lift, `policies/SAFETY_RULES.md` is the single source of truth; `JAI_GOVERNANCE_STACK.md §6`, `JAI_PROJECT_IMPLEMENTATION_PLAN.md §3`, and `JAI_RUNTIME_ADVISORY_REVIEWER.md §6` all reference it. No safety rule is added, removed, weakened, or renumbered (canonical S1–S8 unchanged); the implementation-plan-specific additions are relabeled E1–E4 to remove S-number collision with the canonical list.

**Added.**
- `policies/SAFETY_RULES.md` — canonical S1–S8 (verbatim from `JAI_GOVERNANCE_STACK.md §6`), versioning meta-rule, workflow-extensions note, cross-references, maintenance rule. Five sections.
- `JAI_PROJECT_IMPLEMENTATION_PLAN.md §3.1` "Execution-context extensions" — E1–E4 (credential specificity, no branch deletion, no Claude settings changes, no hook installs). These are the impl-plan-specific additions that previously lived as S2/S7/S8/S9 in §3's S1–S10 list.

**Changed.**
- `JAI_VERSION` — `v1.4` → `v1.5`.
- `JAI_STANDARD.md` — `**Current version:**` line bumped to `v1.5`. Cross-cutting inventory table `policies/` row updated to reflect that `SAFETY_RULES.md` is now seeded (the planned v1.1 entry has finally landed at v1.5).
- `JAI_GOVERNANCE_STACK.md §6` — body replaced with a 3-line pointer to `policies/SAFETY_RULES.md`. The §6 anchor name stays; existing cross-references to "S1–S8 from `JAI_GOVERNANCE_STACK.md §6`" continue to resolve correctly.
- `JAI_PROJECT_IMPLEMENTATION_PLAN.md §3` — body replaced: references canonical S1–S8 in `policies/SAFETY_RULES.md`, plus a new §3.1 "Execution-context extensions" listing E1–E4. Renumbering note added explaining the S-to-E rename.
- `JAI_RUNTIME_ADVISORY_REVIEWER.md §6` — single explanatory note added at the top clarifying that this section is a reviewer-audit checklist, not a restatement of S1–S8 (which now live in `policies/SAFETY_RULES.md`). The 26-bullet checklist itself is unchanged.
- `policies/README.md` — updated to reflect that `SAFETY_RULES.md` is now the first seeded entry; planned files list shortened accordingly.
- `intake/2026-05-10-safety-rules-policy-lift.md` — status: `Open — proposal recorded` → `Implemented — see JAI_CHANGELOG.md v1.5`.

**Removed.** N/A. (S1–S8 canonical list is unchanged; only its location moved.)

**Source.** Approved enhancement intake `intake/2026-05-10-safety-rules-policy-lift.md`, generated by JAI Governance Review 2026-05-10 (STRUCT-2, STRUCT-3 findings). Operator-approved 2026-05-10.

**Migration.** **None for any existing project.** Projects do not reference S-numbers directly in their `/jai/` profiles (verified at intake time across `Trend Scanner v1`, `PO3 Scanner v1`, `Solana Command Center v1`). The reorganization is invisible to project conformance checks. Existing projects continue to declare their existing `jai_standard:` versions; bumping a project's pointer to `v1.5` is an opt-in conformance assertion, not a functional requirement (same pattern as v1.1, v1.2, v1.3, v1.4). Other Governance Review intake drafts (2, 3, 4) remain pending operator decision and are not part of this v1.5 batch.

---

## v1.4 — 2026-05-10

**Summary.** Adds two new global, read-only, opt-in workflows: Research-Backed Plan Mode (turns vague ideas into implementation-ready plans through six structured phases, stopping at an explicit approval gate before any execution) and JAI Governance Review (audits the global JAI standard itself for structural gaps, safety regressions, command-coverage gaps, and best-practice drift, routing every finding through the existing Enhancement Intake gate without ever editing the global standard directly).

**Added.**
- `JAI_RESEARCH_BACKED_PLAN_MODE.md` — full spec. Sections: 0 Purpose, 1 Relationship to Other JAI Files, 2 When to Use, 3 Six Phases (Clarify → Domain Research → Reference Research → Capability Extraction → Architecture → Implementation-Ready Plan), 4 Approval Gate (the hard stop), 5 Forbidden Actions, 6 Restricted-Area Discipline, 7 Output Format, 8 Maintenance Rule.
- `JAI_GOVERNANCE_REVIEW.md` — full spec. Sections: 0 Purpose, 1 Relationship to Other JAI Files, 2 When to Use, 3 Six Phases (Inventory → Structural → Safety → Command Coverage → Best-Practice Crosswalk → Gap Classification), 4 Output Routing — the No-Self-Modification Guardrail, 5 Forbidden Actions, 6 Output Format, 7 Maintenance Rule.
- `JAI_COMMANDS.md` §14 — registers `JAI research-backed plan` command.
- `JAI_COMMANDS.md` §15 — registers `JAI governance review` command.

**Changed.**
- `JAI_VERSION` — `v1.3` → `v1.4`.
- `JAI_STANDARD.md` — `**Current version:**` line bumped to `v1.4`. Two short pointer sections added after the existing "Global dev response format" section: "Global research-backed plan mode" and "Global governance review", mirroring the existing pointer-section pattern.
- `JAI_COMMANDS.md` §1 — file list extended with `JAI_RESEARCH_BACKED_PLAN_MODE.md` and `JAI_GOVERNANCE_REVIEW.md`; Roles subsection extended with one role line per new file.
- `JAI_COMMANDS.md` §12 — summary table extended with two new rows for the new commands.

**Removed.** N/A.

**Source.** Operator-driven self-governance audit 2026-05-10 (PO3 Scanner v1 session). Surfaces the upstream-research gap visible across the v1.0–v1.3 onboarding lifecycle: the standard had skills for execution and review but no skill for clarifying vague intent, researching domains and reference repos, extracting capabilities, sketching architecture, and producing implementation-ready plans before coding. The same audit identified the absence of a self-review workflow that could improve the standard without weakening security boundaries. Both gaps are addressed additively.

**Migration.** **None for any existing project.** Both specs are global-only (same scope pattern as `JAI_RUNTIME_ADVISORY_REVIEWER.md` and `JAI_DEV_RESPONSE_FORMAT.md`). No project-level `/jai/` shape change. Existing projects continue to declare their existing `jai_standard:` versions; bumping a project's pointer to `v1.4` is an opt-in conformance assertion, not a functional requirement (same pattern as v1.1, v1.2, and v1.3). Both new workflows are opt-in by trigger phrase and inherit S1–S8 unchanged.

---

## v1.3 — 2026-05-09

**Summary.** Adds the canonical dev/implementation response format spec and introduces the ST-10 ("Simple Terms, 10-year-old clarity") opt-in response mode. Together they give every JAI-conformant dev agent a fixed output shape that downstream reviewers can validate against, plus an operator-invocable plain-English explanation mode that preserves every technical fact, file path, branch name, commit hash, risk, and approval gate.

**Added.**
- `JAI_DEV_RESPONSE_FORMAT.md` — full spec. Sections: 0 Purpose, 1 Relationship to Other JAI Files, 2 When to Use, 3 Standard Section List (ten required sections — Plain-English Summary, What This Means, What Was Touched, What Was Not Touched, Verification Performed, Risk/Scope Assessment, Pending Decisions, Recommended Next Action, Copy-Paste Operator Reply, Closeout Statement), 4 Section Omission Rules, 5 Visual Conventions, 6 Length Guidance, 7 Maintenance Rule, plus a top-level `§ Response Modes` section that defines ST-10 as the first (and only) mode in v1.3.
- ST-10 response mode inside `JAI_DEV_RESPONSE_FORMAT.md § Response Modes` — opt-in only (operator must invoke via `ST-10`, `Explain this ST-10`, `Break this down ST-10`, `ST-10 this dev update`, or `Use ST-10 clarity`). Includes a section-heading crosswalk table, jargon-pair rule, length rule, explicit "must NOT do" list (no removal of paths/branches/hashes/commands/risks/approval gates/restricted-area confirmations), and explicit non-status as a top-level numbered JAI command.

**Changed.**
- `JAI_VERSION` — `v1.2` → `v1.3`.
- `JAI_STANDARD.md` — `**Current version:**` line bumped (was stale at `v1.0` through v1.1 and v1.2 because the file documents the v1.0 baseline pointer; the header field now reflects the current global version per its stated purpose). New short section "Global dev response format" added pointing at `JAI_DEV_RESPONSE_FORMAT.md`, mirroring the existing "Global runtime advisory review mode" section.
- `JAI_COMMANDS.md` §1 — added `JAI_DEV_RESPONSE_FORMAT.md` to the file-list bullet and to the Roles subsection; added one sentence noting that every command's `Required output` is styled per `JAI_DEV_RESPONSE_FORMAT.md` when emitted by a dev/implementation agent; added a short note that ST-10 lives inside `JAI_DEV_RESPONSE_FORMAT.md § Response Modes` as a response mode, intentionally not as a top-level numbered command in §3–§11.
- `JAI_RUNTIME_ADVISORY_REVIEWER.md` §5 + §11 — cross-reference notes pointing reviewers at `JAI_DEV_RESPONSE_FORMAT.md` so missing required sections from the dev format can be treated as `Hold — insufficient evidence` per §5.

**Removed.** N/A.

**Source.** Operator request 2026-05-09. Codifies the response cadence in active use during the JAI v1.0–v1.2 onboarding lifecycle and the PO3 Scanner v1.0 → v1.2 migration pass (PRs #1–#6). ST-10 mode addresses the operator's stated need for ADHD/dyslexia-friendly scanning without compromising security posture, audit trail, or restricted-area discipline.

**Migration.** **None for any existing project.** Section is additive; no project-level `/jai/` shape change. Existing projects continue to declare their existing `jai_standard:` versions; bumping a project's pointer to `v1.3` is an opt-in conformance assertion, not a functional requirement (same pattern as v1.1 and v1.2). The dev response format applies to any dev/implementation agent in any JAI session regardless of which version the touched project declares; ST-10 applies only when the operator invokes it.

---

## v1.2 — 2026-05-09

**Summary.** Adds JAI Fleet Sync Audit infrastructure as part of the global JAI standard. Defines the read-only procedural spec for auditing every known project repository against the current standard, plus a machine-local registry of project paths to walk.

**Added.**
- `JAI_FLEET_SYNC_AUDIT.md` — workflow spec (12 sections: purpose, relationship to other JAI files, when to use, required reads, forbidden actions, per-project audit procedure, drift classification, output format, recommended next action per row, approval gate, privacy/safety, output cache).
- `JAI_PROJECT_REGISTRY.md` — machine-local registry. Initial active projects: `trend-scanner-v1`, `po3-scanner-v1`, `solana-command-center-v1`. Initial candidate: `trend-scanner-v2` (not active, not audited by default).

**Changed.**
- `JAI_COMMANDS.md` §6 (`JAI fleet sync audit` command) — Required reads section extended with two new entries pointing at `JAI_FLEET_SYNC_AUDIT.md` (procedural spec) and `JAI_PROJECT_REGISTRY.md` (project list).
- `JAI_VERSION` — `v1.1` → `v1.2`.

**Removed.** N/A.

**Source.** Operator request 2026-05-09 (post-PO3-Scanner-v1 onboarding). The JAI v1.1 §17 workflows codified per-project cadence; v1.2 codifies the fleet-level meta-workflow that operates across projects. Pilot is intended to be the first actual run after this version bump (3-row report for `trend-scanner-v1`, `po3-scanner-v1`, `solana-command-center-v1`, plus the candidate row for `trend-scanner-v2`). The actual audit is not invoked as part of this version-bump batch.

**Migration.** **None for any existing project.** Section is additive; no project-level `/jai/` shape change. Existing projects continue to declare their existing `jai_standard:` versions; the fleet audit will simply classify each project's drift against global `v1.2` (which equals `v1.1` for any project that hasn't migrated, since no project-level shape changed between v1.1 and v1.2). The registry's "Onboarded" column for non-PO3 projects is annotated `unknown — verify at first audit` and will be filled in by the first actual `JAI fleet sync audit` run.

**Note: machine-local registry.** `JAI_PROJECT_REGISTRY.md` contains operator-specific paths and is intended to be excluded from version control if the global standard is ever promoted to a git repo. The file documents this scope explicitly.

---

## v1.1 — 2026-05-09

**Summary.** Adds Standard Repo-Change Workflows section (§17) to `JAI_RUNTIME_ADVISORY_REVIEWER.md`, codifying the branch/PR cadence patterns proven in PO3 Scanner Phase 3.3b–4.0 + JAI Pass 2 lifecycle. Reviewers can now validate workflow shape, not just diff safety.

**Added.**
- `JAI_RUNTIME_ADVISORY_REVIEWER.md` §17 — Standard Repo-Change Workflows. Five subsections: §17.1 full branch/PR flow for foundational/risky changes (17 stages); §17.2 bundled low-risk docs cleanup (acceptance criteria + reject conditions); §17.3 direct-main exception for tiny doc-only changes (5 conditions, opt-in per change); §17.4 memory-only vs repo-tracked update separation (verify both halves separately); §17.5 post-merge closeout requirements (9-step sequence including `--ff-only` sync, safe branch deletion, and bookkeeping for `jai/CURRENT_STATE.md`, `HANDOFF.md`, `CLEANUP_REGISTER.md`, `THREAT_MODEL.md`, `DECISION_LOG.md`).
- `JAI_RUNTIME_ADVISORY_REVIEWER.md` §11.7 cross-reference pointing reviewers at §17 for workflow-shape validation during the standard review pass.

**Changed.**
- `JAI_RUNTIME_ADVISORY_REVIEWER.md` — current §17 "Final Rule" renumbered to §18 to make room for the new §17. No content change to Final Rule.
- `JAI_VERSION` — `v1.0` → `v1.1`.

**Removed.** N/A.

**Source.** Pilot retrospective on PO3 Scanner v1's commit history `038cc34..9d6da4a` (2026-05-08 / 09): 4 PRs merged (initial /jai profile, README pointer, .gitignore env-bak, JAI docs-cleanup) plus 1 direct-main commit (CLEANUP_REGISTER #13 close), plus 1 memory-only refresh. The cadence shapes proved in that lifecycle are codified into §17. Wording adjustment to §17.1 step 14 (merge strategy language) made global-safe per operator request 2026-05-09.

**Migration.** **None for any existing project.** Section is additive; no project-level `/jai/` shape change. Existing projects continue to conform to v1.0; the new §17 applies to any reviewer running `JAI runtime advisory review` on or after the v1.1 update. Project-level `jai/CLAUDE.md` version pointers remain at `v1.0` until each project chooses to bump (no functional reason to bump unless they want to assert v1.1 conformance).

---

## v1.0 — 2026-05-08

**Summary.** Initial JAI Standard. Defines the cross-project meta-layer above the existing project-level `/jai/` implementation plan. Bootstrap is global-only; no project files were edited.

**Added.**
- `JAI_VERSION` (`v1.0`).
- `JAI_STANDARD.md` — points to the project-level implementation plan as committed in Trend Scanner v1 at `2f57e65` (Merge PR #5, 2026-05-08) for the v1.0 required-files list.
- `JAI_GOVERNANCE_STACK.md` — governance draft v3, locked-in 2026-05-08.
- `policies/`, `catalogs/`, `skills/`, `templates/`, `intake/`, `references/` — directory scaffolding with README placeholders. Libraries seed lazily as intake items land.
- `references/repo-vetting-lasso-vs-dotclaude-2026-05-08.md` — first vetting report, carried in from chat at bootstrap. Recommends Reference-only for both repos at v1.0; defers Adapt for lasso to a future intake item.

**Clarification.** 2026-05-08 — `JAI_PROJECT_IMPLEMENTATION_PLAN.md` added in-place at v1.0 as the builder/manual. No version bump; it codifies the existing procedure.

**Clarification.** 2026-05-08 — `JAI_RUNTIME_ADVISORY_REVIEWER.md` added as the global review-only advisory mode. No version bump; it codifies reviewer behavior. Companion command `JAI runtime advisory review` registered in [JAI_COMMANDS.md](./JAI_COMMANDS.md) §11.

**Changed.** N/A (initial version).

**Removed.** N/A (initial version).

**Source.** Bootstrap pass following approval of governance draft v3, 2026-05-08.

**Migration.** **None for any existing project.** Trend Scanner v1's `/jai/` is the empirical baseline for v1.0; it already conforms by construction. The pilot's first Migration Pass (a separate, approved task) will add `jai_standard: v1.0` to its `jai/CLAUDE.md` front-matter and create an empty-but-headered `jai/PROJECT_EXCEPTIONS.md`. No other project changes.

**Locked decisions (recorded once at v1.0; do not repeat in later entries).**
- Canonical location: `~/.claude/jai-standard/` local folder; promotion to dedicated git repo deferred until ≥2 machines or ≥3 projects need it.
- Project version pointer: `jai/CLAUDE.md` front-matter or first section.
- Project exceptions: separate file `jai/PROJECT_EXCEPTIONS.md`.
- Pilot project: Trend Scanner v1.
- Vetting reports: global, under `references/`.
- MAJOR bump = breaking structural change only; additive changes (incl. new required files with skip-if-missing fallback) are MINOR; no PATCH level.
- Implementation plan stays authoritative for project-level `/jai/` shape; governance does not re-derive it.
