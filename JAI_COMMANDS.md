# JAI_COMMANDS.md

## 0. Purpose

`JAI_COMMANDS.md` is the command-trigger layer for the global JAI system. It defines short, repeatable phrases that trigger the correct JAI workflow without restating the whole process every time.

These commands do **not** automatically authorize file writes, commits, pushes, Docker actions, installs, migrations, provider calls, or destructive operations.

All commands are **read-only by default** unless the user explicitly approves the apply/write step.

---

## 1. Relationship to the Global JAI Standard

This file works alongside the rest of the global standard at `~/.claude/jai-standard/`:

- `JAI_VERSION`
- `JAI_STANDARD.md`
- `JAI_GOVERNANCE_STACK.md`
- `JAI_PROJECT_IMPLEMENTATION_PLAN.md`
- `JAI_RUNTIME_ADVISORY_REVIEWER.md`
- `JAI_DEV_RESPONSE_FORMAT.md`
- `JAI_RESEARCH_BACKED_PLAN_MODE.md`
- `JAI_GOVERNANCE_REVIEW.md`
- `JAI_AGENT_ARCHITECTURE.md`
- `JAI_CHANGELOG.md`

Roles:

- `JAI_STANDARD.md` defines what the current standard is.
- `JAI_PROJECT_IMPLEMENTATION_PLAN.md` explains how to apply JAI to a new repo.
- `JAI_GOVERNANCE_STACK.md` explains versioning, enhancement intake, drift audits, and migrations.
- `JAI_COMMANDS.md` defines the user-facing trigger phrases for running those workflows.
- `JAI_RUNTIME_ADVISORY_REVIEWER.md` defines the global review-only advisory mode used by the `JAI runtime advisory review` command (§11).
- `JAI_DEV_RESPONSE_FORMAT.md` defines the canonical output shape for any JAI-conformant dev/implementation agent. Every command's `Required output` section in this file is styled per that spec when emitted by a dev agent. The file also defines response modes (opt-in presentation layers); v1.3 ships the `ST-10` ("Simple Terms, 10-year-old clarity") response mode inside `JAI_DEV_RESPONSE_FORMAT.md § Response Modes`. ST-10 is a response mode, not a top-level numbered command — it does not appear in §3–§15 and is not an alias in §16.
- `JAI_RESEARCH_BACKED_PLAN_MODE.md` defines the read-only six-phase workflow used by the `JAI research-backed plan` command (§14). The mode stops at an approval gate before any execution; plan approval is not execution approval.
- `JAI_GOVERNANCE_REVIEW.md` defines the read-only audit workflow for the global JAI standard itself, used by the `JAI governance review` command (§15). All findings route through Enhancement Intake (§8) and `JAI standard update` (§9); the command never edits global files directly.
- `JAI_AGENT_ARCHITECTURE.md` defines the four-role conceptual model (Orchestrator + Builder + Security/Risk Reviewer + Test/Validation Reviewer) under which any JAI-conformant assistant operates when a task crosses build → review → validate (added v1.7). Agent 2 reuses `JAI_RUNTIME_ADVISORY_REVIEWER.md` checklists in full; Agent 1 reuses `JAI_DEV_RESPONSE_FORMAT.md §3` ten-section output shape in full; S1–S8 from `policies/SAFETY_RULES.md` inherited verbatim. Conceptual personas only — not Claude Code subagent delegation. No new top-level command in §3–§15 and no new alias in §16 are added by v1.7.
- §16 of this file contains **conversational aliases** (added v1.6) — short navigation frames that route to formal commands; not formal commands themselves; inherit S1–S8 and §2 universal safety rules unchanged.

---

## 2. Universal Command Safety Rules

Every command in this file inherits these safety rules:

- Read-only by default.
- No `.env` reads or writes.
- No credential, token, key, secret, mnemonic, or provider config inspection unless explicitly approved.
- No Docker lifecycle commands.
- No migrations.
- No provider calls.
- No scheduler edits.
- No app-code changes.
- No hook installs.
- No Claude settings changes.
- No dependency installs.
- No destructive cleanup.
- No branch deletion.
- No commits.
- No pushes.
- No force operations.
- No file writes until the user explicitly approves the proposed changes.

If a workflow needs to write files, it must first return a proposal in chat.

---

## 3. Command: `JAI new-repo intake`

**Purpose.** Use this when downloading, cloning, or starting a new repo and you want to evaluate it before running or modifying anything.

**Trigger phrase.** `Run JAI new-repo intake on this repo.`

**Required reads.**
- `~/.claude/jai-standard/JAI_VERSION`
- `~/.claude/jai-standard/JAI_STANDARD.md`
- `~/.claude/jai-standard/JAI_GOVERNANCE_STACK.md`
- `~/.claude/jai-standard/JAI_PROJECT_IMPLEMENTATION_PLAN.md`

**Allowed actions.**
- Inspect repo structure.
- Inventory docs.
- Inventory package files.
- Inspect safe config files.
- Inspect Docker/Compose files read-only.
- Inspect scripts read-only.
- Check for unsafe port bindings.
- Check for secret-handling risks without reading restricted secret files.
- Produce a Pass 1 audit report.

**Forbidden actions.**
- Do not run the app.
- Do not install dependencies.
- Do not run Docker.
- Do not run migrations.
- Do not call providers.
- Do not create `/jai` yet.
- Do not edit files.
- Do not commit or push.

**Required output.**
- repo purpose summary
- docs inventory
- infrastructure inventory
- security exposure findings
- dependency/package findings
- script risk findings
- suspected restricted areas
- recommended `/jai` structure
- risks and cautions
- restricted areas untouched
- git status
- next recommended step

**Approval gate.** Creating `/jai` requires separate approval after the Pass 1 audit.

---

## 4. Command: `JAI drift audit`

**Purpose.** Use this on an existing project that already has `/jai` to compare it against the current global JAI standard.

**Trigger phrase.** `Run JAI drift audit on this repo.`

**Required reads.**
- Global `JAI_VERSION`
- Global `JAI_STANDARD.md`
- Global `JAI_GOVERNANCE_STACK.md`
- Project `jai/CLAUDE.md`
- Project `jai/CURRENT_STATE.md`
- Project `jai/PROJECT_CONTEXT.md`
- Project `jai/SECURITY_RULES.md`
- Project `jai/WORKFLOW_RULES.md`
- Project `jai/PROJECT_EXCEPTIONS.md`, if present

**Allowed actions.**
- Compare declared project JAI version against global version.
- Identify missing files.
- Identify stale sections.
- Identify undocumented project exceptions.
- Identify conflicts between project rules and global rules.
- Propose a Migration Pass.

**Forbidden actions.**
- Do not edit files.
- Do not update project `/jai`.
- Do not touch app code.
- Do not run Docker.
- Do not run migrations.
- Do not call providers.
- Do not commit or push.

**Required output.**
- current project JAI version
- current global JAI version
- missing files or sections
- stale rules
- project-specific exceptions
- conflicts
- proposed Migration Pass
- exact files that would be changed
- approval needed

**Approval gate.** Any Migration Pass requires explicit approval.

---

## 5. Command: `JAI migration pass`

**Purpose.** Use this after a drift audit has already proposed changes and the user approves applying them.

**Trigger phrase.** `Run JAI migration pass for this repo.`

**Required reads.**
- Drift audit report
- Global standard files
- Project `/jai` files being updated

**Allowed actions.**
- Apply only the approved docs-only changes.
- Update project `jai/CLAUDE.md` version pointer if approved.
- Create or update `jai/PROJECT_EXCEPTIONS.md` if approved.
- Append a project `jai/DECISION_LOG.md` entry if approved.
- Report final git status.

**Forbidden actions.**
- No app-code changes.
- No Docker.
- No migrations.
- No provider calls.
- No `.env`.
- No branch deletion.
- No commit unless separately approved.
- No push unless separately approved.

**Required output.**
- files changed
- exact diff summary
- project JAI version before/after
- restricted areas untouched
- git status
- commit hash if committed
- push status if pushed
- remaining drift, if any

**Approval gate.** Commit and push require separate approval unless explicitly included.

---

## 6. Command: `JAI fleet sync audit`

**Purpose.** Use this when checking many projects and identifying which ones are missing JAI or behind the current standard.

**Trigger phrase.** `Run JAI fleet sync audit.`

**Required reads.**
- Global `JAI_VERSION`
- Global `JAI_STANDARD.md`
- Global `JAI_GOVERNANCE_STACK.md`
- Global `JAI_FLEET_SYNC_AUDIT.md` (procedural spec)
- Global `JAI_PROJECT_REGISTRY.md` (project list)
- Known project folders only

**Allowed actions.**
- Check whether each folder is a git repo.
- Check whether `/jai` exists.
- Check whether `jai/CLAUDE.md` exists.
- Check declared JAI version.
- Check git branch/status.
- Identify projects that are not onboarded.
- Identify projects that are behind the current standard.

**Forbidden actions.**
- Do not edit files.
- Do not run apps.
- Do not run Docker.
- Do not touch `.env`.
- Do not run migrations.
- Do not call providers.
- Do not commit or push.
- Do not delete branches.
- Do not recursively inspect restricted files.

**Required output.** A table with:

| project path | repo name | current branch | JAI status | declared JAI version | target JAI version | drift level | recommended next action |
|---|---|---|---|---|---|---|---|

`drift level` is one of: `none` / `minor` / `major` / `not onboarded`.

**Approval gate.** Fleet audit is report-only. Updating any project requires a separate project-specific Migration Pass.

---

## 7. Command: `JAI external repo vetting`

**Purpose.** Use this when finding a third-party GitHub repo and deciding whether its logic should be adopted, adapted, referenced, rejected, or deferred.

**Trigger phrase.** `Run JAI external repo vetting on <repo-url>.`

**Required reads.**
- Global `JAI_GOVERNANCE_STACK.md`
- Global references policy
- The third-party repo, read-only

**Allowed actions.**
- Inspect repo structure.
- Inspect README/docs.
- Inspect install scripts.
- Inspect hooks/scripts.
- Inspect dependencies.
- Inspect license.
- Identify network calls.
- Identify secret-handling risks.
- Identify `.claude` or settings modifications.
- Extract useful concepts.

**Forbidden actions.**
- Do not install the repo.
- Do not run install scripts.
- Do not execute hooks.
- Do not copy files.
- Do not modify `.claude`.
- Do not modify Claude settings.
- Do not touch project repos.
- Do not commit or push.

**Required output.**
- purpose
- repo structure
- security review
- dependency/install review
- network behavior
- secret-handling risk
- JAI compatibility
- risk level: `low` / `medium` / `high`
- recommendation: `adopt` / `adapt` / `reference only` / `reject` / `defer`
- extractable value
- what should not be adopted

**Approval gate.** Any adoption, install, copying, or global-standard update requires separate approval.

---

## 8. Command: `JAI enhancement intake`

**Purpose.** Use this when a new idea, security pattern, repo lesson, incident lesson, or workflow improvement might become part of the global JAI standard.

**Trigger phrase.** `Run JAI enhancement intake for <idea/source>.`

**Required reads.**
- `JAI_VERSION`
- `JAI_STANDARD.md`
- `JAI_GOVERNANCE_STACK.md`
- `JAI_CHANGELOG.md`
- Relevant reference/vetting report if applicable

**Allowed actions.**
- Define the problem.
- Classify the enhancement.
- Decide whether it is global, project-specific, or reference-only.
- Propose affected global files.
- Propose version impact.
- Propose pilot project.
- Propose migration impact.

**Forbidden actions.**
- Do not edit global files yet.
- Do not edit project files.
- Do not bump version.
- Do not create catalogs/policies unless approved.
- Do not install tools.
- Do not change Claude settings.

**Required output.**
- problem statement
- source
- classification: `global` / `project-specific` / `reference only`
- risk level
- proposed global standard changes
- version impact: `none` / `v1.x minor` / `major`
- pilot project recommendation
- migration impact for existing projects
- approval needed

**Approval gate.** Any global standard change requires explicit approval.

---

## 9. Command: `JAI standard update`

**Purpose.** Use this only after an enhancement intake is approved and the user wants to update the global JAI standard.

**Trigger phrase.** `Run JAI standard update for approved enhancement <name>.`

**Required reads.**
- Approved enhancement intake
- Global standard files
- Changelog
- Version file

**Allowed actions.**
- Apply approved docs-only edits under `~/.claude/jai-standard/`.
- Update `JAI_CHANGELOG.md`.
- Update `JAI_VERSION` if approved.
- Create approved catalog/policy/skill/template files.

**Forbidden actions.**
- Do not touch project repos.
- Do not run migrations.
- Do not install hooks.
- Do not change Claude settings.
- Do not touch app code.
- Do not make unapproved version bumps.

**Required output.**
- files changed
- version before/after
- changelog entry
- migration notes
- whether projects need drift audits
- restricted areas untouched

**Approval gate.** Project migrations happen separately after global standard update.

---

## 10. Command: `JAI status report`

**Purpose.** Use this when checking the current global JAI status and project alignment status without changing anything.

**Trigger phrase.** `Run JAI status report.`

**Allowed actions.**
- Read global JAI files.
- Report current JAI version.
- Report known references.
- Report current command list.
- Optionally check current project JAI version if run inside a project.

**Forbidden actions.**
- No edits.
- No Docker.
- No `.env`.
- No installs.
- No commits.
- No pushes.

**Required output.**
- global JAI version
- global files present
- current project JAI version, if applicable
- open intake items
- known pending migrations
- recommended next action

---

## 11. Command: `JAI runtime advisory review`

**Purpose.** Use this when reviewing a dev/audit/security/implementation/governance report and you want the agent to act as a senior reviewer (engineer, security, architecture, infra/DevOps, governance, or docs-consistency) and return an explicit Approve / Hold / Correction / Reject decision — without doing the work itself.

**Trigger phrase.** `Run JAI runtime advisory review on <report or thread>.`

**Required reads.**
- `~/.claude/jai-standard/JAI_RUNTIME_ADVISORY_REVIEWER.md` (canonical spec for the mode — sections 1, 2, 4, 6, 11, 12, 13, and 17 are the load-bearing rules)
- The report or thread under review
- Any project `/jai/` files explicitly cited by the report, read-only

**Allowed actions.**
- Restate the objective being reviewed.
- Inspect the evidence the report provides.
- Identify what evidence is missing.
- Run the security review checklist (`JAI_RUNTIME_ADVISORY_REVIEWER.md` §8).
- Run the architecture review checklist (§9).
- Verify whether restricted areas were touched (§6, §11.6).
- Identify documentation/governance updates the report implies (§11.7).
- Choose one advisory decision (§11.8): `Approved` / `Approved — follow-up required` / `Hold` / `Correction required` / `Rejected` / `Proceed with audit-only` / `Proceed with doc-only cleanup` / `Proceed with implementation`.
- Provide a copy-paste reply for the implementation agent (§11.9).
- Assign a risk rating (§13).

**Forbidden actions.**
- Do not edit files.
- Do not run commands.
- Do not run Docker.
- Do not touch `.env` or any credential.
- Do not run migrations.
- Do not call providers.
- Do not commit.
- Do not push.
- Do not install dependencies.
- Do not change Claude settings.
- Do not install hooks.
- Do not modify the global JAI standard.
- Do not modify any project `/jai/`.

**Required output.** Standard runtime review format from `JAI_RUNTIME_ADVISORY_REVIEWER.md` §11:

1. Restated objective
2. Plain-English summary
3. Evidence reviewed (incl. what was not provided)
4. Security review
5. Architecture / engineering review
6. Restricted-area verification
7. Documentation / governance review
8. Advisory decision (one of the eight options above)
9. Copy-paste reply for the implementation agent

**Approval gate.** This command is review-only by design. It never writes files. If the review identifies follow-up edits, commits, or pushes, those must be approved separately and run via the appropriate workflow command (Migration Pass, etc.).

---

## 14. Command: `JAI research-backed plan`

**Purpose.** Use this when an idea is vague and you want a researched, capability-mapped, architected, batch-sequenced implementation plan **before** any code is written.

**Trigger phrase.** `Run JAI research-backed plan on <idea/feature>.`

**Required reads.**
- `~/.claude/jai-standard/JAI_RESEARCH_BACKED_PLAN_MODE.md`
- `~/.claude/jai-standard/JAI_DEV_RESPONSE_FORMAT.md`
- `~/.claude/jai-standard/JAI_GOVERNANCE_STACK.md` (S1–S8)
- Project `jai/CLAUDE.md`
- Project `jai/PROJECT_CONTEXT.md`
- Project `jai/ARCHITECTURE.md`
- Project `jai/INFRASTRUCTURE.md`
- Project `jai/SECURITY_RULES.md`
- Project `jai/THREAT_MODEL.md`
- Project `jai/WORKFLOW_RULES.md`

**Allowed actions.**
- Restate operator intent and surface open questions.
- WebFetch on public docs and reference repos (≤5 sources per pass; URL + access date cited).
- Build a domain map.
- Build a reference shortlist.
- Extract capabilities and tag them.
- Sketch architecture against existing project files.
- Produce a sequenced, batch-shaped plan emitted per `JAI_DEV_RESPONSE_FORMAT.md`.

**Forbidden actions.**
- Do not write files (the plan is returned in chat).
- Do not clone or run external repos.
- Do not install dependencies.
- Do not edit `.claude` settings or hooks.
- Do not call provider APIs.
- Do not start or stop Docker.
- Do not modify any project `/jai/`.
- Do not propose modifications to the project's `Hard NOT-touch list` — surface the conflict and stop.
- Do not authorize execution. Plan approval ≠ execution approval.

**Required output.** Per `JAI_DEV_RESPONSE_FORMAT.md` (ten sections), with the plan body containing:
- Confirmed problem statement.
- Domain map.
- Reference shortlist with adopt/adapt/reference/reject tags.
- Capability list with risk signals.
- Architecture sketch with restricted-area honor check.
- Batch-sequenced plan with per-batch validation and approval gates.

**Approval gate.** The mode stops after the plan is returned. Execution requires the operator to invoke the appropriate project-level skill (e.g. `frontend_change`, `backend_change`) with a separate explicit "go" per batch.

---

## 15. Command: `JAI governance review`

**Purpose.** Use this when auditing the global JAI standard itself for structural gaps, safety regressions, command-coverage gaps, or best-practice drift. Findings route through Enhancement Intake — this command cannot edit the standard.

**Trigger phrase.** `Run JAI governance review.`

**Required reads.**
- Every file under `~/.claude/jai-standard/` (top level + subfolders).
- `JAI_GOVERNANCE_STACK.md §4` (Enhancement workflow).
- `JAI_GOVERNANCE_STACK.md §6` (Safety rules).
- `JAI_VERSION`.
- `JAI_CHANGELOG.md`.

**Allowed actions.**
- Inventory the global tree.
- Run the structural audit (Phase 2).
- Run the safety audit (Phase 3) — flag SAFETY-CRITICAL findings up front.
- Run the command-coverage audit (Phase 4).
- Run the best-practice crosswalk (Phase 5) using existing `references/*.md` and (read-only) WebFetch on public sources, citing URL + access date only.
- Classify gaps and produce **draft** intake items in chat.

**Forbidden actions.**
- Do not edit any file under `~/.claude/jai-standard/`.
- Do not edit any project `/jai/`.
- Do not bump `JAI_VERSION`.
- Do not append `JAI_CHANGELOG.md`.
- Do not write `intake/*.md` files — only return drafts.
- Do not register new commands in `JAI_COMMANDS.md`.
- Do not invoke `JAI standard update` automatically.
- Do not weaken or rewrite S1–S8.

**Required output.**
- Inventory.
- Structural findings.
- Safety findings (with SAFETY-CRITICAL flagged at top if any).
- Command coverage findings.
- Best-practice crosswalk table.
- Gap classification table with version impact.
- Proposed intake item drafts (returned in chat, not written).
- Approval gate for converting drafts into real intake items.

**Approval gate.** This command is review-only by design. Converting a draft into a real `intake/YYYY-MM-DD-<name>.md` runs through `JAI enhancement intake` (§8). Standard updates run through `JAI standard update` (§9).

---

## 12. Command Summary Table

| Command | Use when | Writes files? | Requires approval to apply? |
|---|---|---:|---:|
| JAI new-repo intake | New repo needs JAI audit | No | Yes for Pass 2 |
| JAI drift audit | Existing repo may be behind standard | No | Yes for migration |
| JAI migration pass | Apply approved project JAI update | Yes, docs only | Yes |
| JAI fleet sync audit | Check many repos | No | Yes for individual migrations |
| JAI external repo vetting | Evaluate third-party repo | No | Yes for adoption |
| JAI enhancement intake | Decide if idea becomes global standard | No | Yes for update |
| JAI standard update | Apply approved global standard change | Yes, global docs only | Yes |
| JAI status report | Check current JAI status | No | No |
| JAI runtime advisory review | Review a dev/audit/security/implementation report | No | No (review-only by design) |
| JAI research-backed plan | Vague idea needs clarified, researched, architected, batch-shaped plan | No | Yes for execution (separate skill, separate "go") |
| JAI governance review | Audit the global JAI standard itself | No | Yes for intake/standard updates (separate commands) |
| JAI: <Alias> conversational frame (§16) | Quick navigation between review modes; not formal commands | No | No (review-only by default; aliases that route to a formal command inherit that command's approval rules) |

---

## 16. Conversational Aliases (introduced v1.6)

**What this section is.**
A set of short conversational navigation frames operators (and assistants) can use to switch into a clearly-named review-only mode without restating long trigger phrases. Aliases are **not** formal commands. They do not replace, override, or extend the §3–§15 command set. Any work that would touch real artifacts is routed to the appropriate §3–§15 formal command.

**Inherited safety rules.**
Every alias inherits the universal command safety rules in §2 unchanged and the canonical S1–S8 rules in `policies/SAFETY_RULES.md` unchanged. Aliases add no new permissions and authorize no new actions.

**Format.**
`JAI: <Alias> — <input>`

**Lane definitions.**
- **Review-only** — analysis/text only; no implied execution path.
- **Strict** — output freezes scope or precedes risky action; explicit confirmation required before any downstream write or implementation.

**Overlap routing.**
For areas already covered by a §3–§15 formal command, the conversational shorthand routes to and follows the formal command verbatim. The shorthand does not introduce a parallel review path.

| Conversational shorthand | Routed to |
|---|---|
| `JAI: Repo Fit` | `JAI external repo vetting` (§7) |
| `JAI: Fleet Sync` | `JAI fleet sync audit` (§6) |
| `JAI: Status` | `JAI status report` (§10) |
| `JAI: Plan` / `JAI: Research-Backed Plan` | `JAI research-backed plan` (§14) |
| `JAI: Reviewer` / `JAI: Advisory` | `JAI runtime advisory review` (§11) |

**Net new aliases (seven), defined below.** Each is review-only by default unless marked Strict.

---

### 16.1 `JAI: Explain`
- **Frame.** Plain-English explanation of a JAI concept, rule, project, or term, grounded in JAI memory + active context, with a pointer to the source rule/doc when one exists.
- **Default lane.** Review-only.
- **Allowed actions.** Read JAI standard files; project `/jai/` files explicitly cited; recent conversation context. Cite source.
- **Forbidden actions.** Inherits §2. No file writes. No execution. No restricted-area reads.
- **Output shape.** 1–5 sentences + source reference.
- **Example prompt.** `JAI: Explain — difference between Phase 1 and Phase 2 in the External Repo Governance Rule.`
- **Routes work to.** None — terminal frame.

### 16.2 `JAI: Map Idea`
- **Frame.** Map a raw idea ("I want to build X") onto JAI structure: nearest existing project, suggested lane, dependencies, conflicts with active fleet, open questions.
- **Default lane.** Review-only.
- **Allowed actions.** Read JAI standard files, `JAI_PROJECT_REGISTRY.md`, project `/jai/CLAUDE.md` and `/jai/PROJECT_CONTEXT.md` if cited, recent conversation. No restricted-area reads.
- **Forbidden actions.** Inherits §2.
- **Output shape.** Project fit · Lane suggestion · Dependencies · Conflicts · Open questions.
- **Example prompt.** `JAI: Map Idea — overlay a momentum signal on top of PO3 Scanner output.`
- **Routes work to.** If pursued → `JAI research-backed plan` (§14).

### 16.3 `JAI: Missing Pieces`
- **Frame.** Compare a draft doc, plan, or handoff against JAI completeness criteria (rollback, restricted files, lane class, exact next action, what-not-to-touch, etc.) and list gaps with severity tags.
- **Default lane.** Review-only.
- **Allowed actions.** Read the supplied artifact, `JAI_DEV_RESPONSE_FORMAT.md`, applicable JAI standard files. No restricted-area reads.
- **Forbidden actions.** Inherits §2.
- **Output shape.** Gap list with severity tags — `blocker` / `should-have` / `nice`.
- **Example prompt.** `JAI: Missing Pieces — here's the Trend Scanner v2 handoff, what's missing?`
- **Routes work to.** None — terminal frame.

### 16.4 `JAI: Opportunity Scan`
- **Frame.** Survey supplied context (project, fleet area, workflow, or doc) and surface concrete improvement opportunities with rationale, candidate location, risk class, and a safe first move.
- **Default lane.** Review-only.
- **Allowed actions.** Read supplied context, JAI standard files, `JAI_PROJECT_REGISTRY.md`. No restricted-area reads.
- **Forbidden actions.** Inherits §2.
- **Output shape.** For each opportunity — Opportunity · Why it matters · Candidate project/layer · Risk class (A/B/C/D) · Safest next action.
- **Example prompt.** `JAI: Opportunity Scan — improvements across the scanner fleet (Trend v1/v2, PO3 v1).`
- **Routes work to.** If pursued → `JAI enhancement intake` (§8) for global improvements; `JAI research-backed plan` (§14) for project-scoped features.

### 16.5 `JAI: Feature Fit`
- **Frame.** Evaluate whether a proposed feature fits a project's stated scope, lane policy, restricted areas, and fleet dependencies. Recommend accept · defer · reject · split.
- **Default lane.** Review-only.
- **Allowed actions.** Read project `/jai/CLAUDE.md`, `/jai/PROJECT_CONTEXT.md`, `/jai/WORKFLOW_RULES.md`, `/jai/PROJECT_EXCEPTIONS.md` if present. No restricted-area reads.
- **Forbidden actions.** Inherits §2.
- **Output shape.** Verdict · reasoning · suggested lane · flags.
- **Example prompt.** `JAI: Feature Fit — add backtest replay to Trend Scanner v2.`
- **Routes work to.** If accepted → `JAI research-backed plan` (§14), then per-project skill (`backend_change`, `frontend_change`, etc.).

### 16.6 `JAI: Risk Gate`
- **Frame.** Pre-action risk classification. Run before any action that could be destructive, hard-to-reverse, or touch shared/runtime state.
- **Default lane.** Strict (review-only by definition since it runs *before* action).
- **Allowed actions.** Read `policies/SAFETY_RULES.md`; applicable project `/jai/SECURITY_RULES.md`, `/jai/THREAT_MODEL.md`. Identify which S1–S8 rules apply. No restricted-area reads.
- **Forbidden actions.** Inherits §2. By definition this frame runs pre-action and never authorizes the downstream action itself.
- **Output shape.** Risk class (A/B/C/D) · applicable S1–S8 rules · required approvals · safer alternatives.
- **Example prompt.** `JAI: Risk Gate — drop and recreate the postgres volume on PO3 Scanner.`
- **Routes work to.** Downstream action runs via the appropriate §3–§15 formal command after explicit approval.

### 16.7 `JAI: Dev Handoff`
- **Frame.** Produce a structured handoff brief in the standard JAI format. Inline only — file write is a separate, explicit ask.
- **Default lane.** Strict (output freezes scope and propagates downstream).
- **Allowed actions.** Read project `/jai/CURRENT_STATE.md`, `/jai/HANDOFF.md`, `/jai/DECISION_LOG.md`, `/jai/CLEANUP_REGISTER.md` if cited, recent conversation. No restricted-area reads.
- **Forbidden actions.** Inherits §2. Inline output only — no write to `<project>/jai/HANDOFF.md` (or any other project file) without separate explicit approval.
- **Output shape.** Handoff doc styled per `JAI_DEV_RESPONSE_FORMAT.md`: goal · current status · decisions made · constraints/preferences · restricted files & areas · lane class · rollback expectations · exact next action · what not to touch.
- **Example prompt.** `JAI: Dev Handoff — SCC v1 wallet-tracking module, ready for next session.`
- **Routes work to.** If executed → per-project skill.

---

**ST-10 within aliases.**
Any alias may carry an `ST-10` modifier (e.g., `JAI: Explain — ST-10 …`). ST-10 stays a response mode inside `JAI_DEV_RESPONSE_FORMAT.md § Response Modes` per the v1.3 governance decision and is intentionally **not** registered as an alias in §16 and **not** a top-level numbered command in §3–§15.

**Unknown alias.**
If the operator types `JAI: <something not in §16 and not routed via the table above>`, the assistant confirms before answering rather than improvising a frame.

---

## 13. Final rule

If a command is ambiguous, default to read-only audit and ask for approval before writing, installing, executing, committing, pushing, or deleting anything.
