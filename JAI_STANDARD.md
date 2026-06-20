# JAI Standard

**Current version:** v1.8
**Established:** 2026-05-08
**Pilot project:** Trend Scanner v1
**Governing document:** [JAI_GOVERNANCE_STACK.md](./JAI_GOVERNANCE_STACK.md)
**Builder / manual:** [JAI_PROJECT_IMPLEMENTATION_PLAN.md](./JAI_PROJECT_IMPLEMENTATION_PLAN.md) — the official procedure for applying this standard to a new repo.

---

## What this file is

The JAI Standard defines the cross-project shape of a project's `/jai/` Claude-context folder, plus the cross-cutting policies, catalogs, skills, and templates that any conforming project inherits. This document is the spec; the [governance stack](./JAI_GOVERNANCE_STACK.md) defines how the spec evolves.

This file does **not** re-derive the project-level `/jai/` shape. The shape is set by the existing implementation plan, which has been live in Trend Scanner v1 since 2026-05-08. v1.0 of the standard is a pointer to that plan as committed at a specific SHA.

---

## v1.0 baseline (pointer)

| Item | Pointer |
|---|---|
| **Project-level implementation plan** | The `/jai/` folder as committed in Trend Scanner v1 at commit `2f57e65` (Merge PR #5, 2026-05-08). That folder is the empirical baseline for v1.0's required-files list. |
| **Read protocol** | Two-tier read model defined in `<project>/jai/CLAUDE.md`: 5 always-read core docs + task-conditional reads. Per the implementation plan. |
| **Required project files** | `CLAUDE.md`, `PROJECT_CONTEXT.md`, `ARCHITECTURE.md`, `INFRASTRUCTURE.md`, `SECURITY_RULES.md`, `THREAT_MODEL.md`, `WORKFLOW_RULES.md`, `VULNERABILITY_REVIEW.md`, `CURRENT_STATE.md`, `DECISION_LOG.md`, `HANDOFF.md`, `CLEANUP_REGISTER.md`, plus `skills/` and `templates/` directories. |
| **Required project files added by governance** | `PROJECT_EXCEPTIONS.md` (governance v1.0 addition; documents divergence from declared standard version). |
| **Skills (canonical specs)** | `backend_change`, `dependency_review`, `docs_cleanup`, `frontend_change`, `git_sync`, `implementation_review`, `infra_audit`, `secret_scanning`, `security_review`, `threat_modeling` — 10 skills as instantiated in Trend Scanner v1. Canonical specs to be lifted into [`skills/`](./skills/) when first amended; until then, projects use the implementation-plan versions. |
| **Templates** | `dev_prompt_template`, `handoff_template`, `review_report_template`, `security_report_template` — 4 templates, same provenance and migration rule as skills. |

---

## Conformance for a project

A project's `<project>/jai/CLAUDE.md` declares its conformance via YAML front-matter (preferred) or first-section block:

```markdown
---
jai_standard: v1.0
project: <name>
last_synced: YYYY-MM-DD
---
```

A project conforms to v1.0 if:
1. It has every file in the **Required project files** list above.
2. Its `CLAUDE.md` declares `jai_standard: v1.0`.
3. Any divergence is documented in `<project>/jai/PROJECT_EXCEPTIONS.md`.
4. It honors the safety rules from [JAI_GOVERNANCE_STACK.md §6](./JAI_GOVERNANCE_STACK.md#6--safety-rules) when JAI workflows run.

`PROJECT_EXCEPTIONS.md` is a governance addition (not yet present in Trend Scanner v1 as of 2026-05-08); the pilot's first Migration Pass will create it.

---

## Root assistant entrypoints

JAI v1.8 recognizes root-level assistant entrypoints as part of the audited
project surface.

- `CLAUDE.md` remains the canonical Claude/project operating entrypoint when a
  project uses Claude Code.
- `AGENTS.md` is the canonical Codex entrypoint when a project uses Codex. It
  must be thin: point to the project JAI framework, load the project read order,
  and restate only the approval gates needed to prevent Codex from weakening
  Claude/JAI discipline.
- `AGENTS.md` must not blindly copy `CLAUDE.md`. If it repeats rules, it does
  so only to preserve gates: research-only means no code, approval is scoped and
  not transitive, no direct push to `main`, no merge without explicit approval,
  no deploy/rebuild/restart without explicit approval, no flag flip without
  explicit approval, and mandatory closeout after any file/state-changing phase.
- If `AGENTS.md`, `CLAUDE.md`, and project `/jai/` rules overlap, the stricter
  and narrower rule wins. `AGENTS.md` may narrow or add gates, but it may not
  weaken global or project JAI rules.

Drift audits check the root `AGENTS.md` for Codex-enabled projects. A missing
thin entrypoint is migration drift; an entrypoint that weakens approval gates is
divergence.

---

## Cross-cutting inventory (v1.0)

These directories scaffold the standard's cross-cutting content. v1.0 ships **scaffolding only** — the libraries are seeded as the corresponding intake items land.

| Directory | Purpose | v1.0 contents |
|---|---|---|
| [`policies/`](./policies/) | Cross-cutting rules every project inherits | **`SAFETY_RULES.md`** (seeded v1.5 — canonical S1–S8). Other planned files (universal SECURITY_RULES / WORKFLOW_RULES / THREAT_MODEL portions) still pending. |
| [`catalogs/`](./catalogs/) | Reusable detector libraries (regex catalogs, taxonomies) | Empty + README. First seeds: `INJECTION_CATEGORIES.md` (lasso-derived) and `SECRET_REGEX_CATALOG.md` (dotclaude-derived). See `references/repo-vetting-lasso-vs-dotclaude-2026-05-08.md`. |
| [`skills/`](./skills/) | Canonical skill specs | Empty + README. Projects use implementation-plan skills until canonical versions are lifted (lazy migration). |
| [`templates/`](./templates/) | Canonical templates | Empty + README. Same lazy-migration rule as skills. |
| [`intake/`](./intake/) | In-flight enhancement proposals | Empty + README. New entries follow `YYYY-MM-DD-<short-name>.md`. |
| [`references/`](./references/) | External vetting reports + vendor links | `repo-vetting-lasso-vs-dotclaude-2026-05-08.md` (carried in at bootstrap). |

---

## Global runtime advisory review mode

[JAI_RUNTIME_ADVISORY_REVIEWER.md](./JAI_RUNTIME_ADVISORY_REVIEWER.md) defines the **review-only** advisory mode for evaluating dev reports, audit results, implementation summaries, security findings, architecture changes, and governance decisions. It is platform-neutral (Claude / Codex / Gemini / generic) and tells the active agent how to review work without performing it.

Default behavior: read-only, no file edits, no command execution, no Docker, no `.env`, no migrations, no provider calls, no commits, no pushes. Output is one of: **Approved / Approved — follow-up required / Hold / Correction required / Rejected** (or one of the *Proceed with audit-only / doc-only cleanup / implementation* variants), plus a copy-paste reply for the implementation agent.

**Scope: global only.** This file is **not** required inside every project `/jai/`. Conforming projects do not duplicate it; they reference it from the global standard. The companion command is `JAI runtime advisory review` (see [JAI_COMMANDS.md](./JAI_COMMANDS.md) §11).

---

## Global dev response format

[JAI_DEV_RESPONSE_FORMAT.md](./JAI_DEV_RESPONSE_FORMAT.md) (introduced in v1.3) defines the canonical output shape for any JAI-conformant dev/implementation agent. Where `JAI_RUNTIME_ADVISORY_REVIEWER.md` §11 defines reviewer output, this file defines implementer output. Together they form the input/output contract that makes every JAI batch reviewable by a downstream reviewer without translation.

The file also defines **response modes** — opt-in presentation layers the operator can invoke per response. v1.3 ships one mode: **ST-10** ("Simple Terms, 10-year-old clarity") — a plain-English explanation mode that preserves all technical facts, file paths, branch names, commit hashes, risks, and approval gates. ST-10 is a response mode, not a numbered JAI workflow command, and is intentionally not registered in `JAI_COMMANDS.md §3–§11`.

**Scope: global only.** This file is **not** required inside every project `/jai/`. Conforming projects do not duplicate it; they reference it from the global standard.

---

## Global research-backed plan mode

[JAI_RESEARCH_BACKED_PLAN_MODE.md](./JAI_RESEARCH_BACKED_PLAN_MODE.md) (introduced in v1.4) defines a read-only six-phase workflow — clarify, domain research, reference research, capability extraction, architecture, plan — that turns a vague idea into an implementation-ready plan and **stops at an explicit approval gate** before any execution begins. The companion command is `JAI research-backed plan` ([JAI_COMMANDS.md](./JAI_COMMANDS.md) §14).

**Scope: global only.** Not required inside every project `/jai/`. Conforming projects reference it from the global standard.

---

## Global governance review

[JAI_GOVERNANCE_REVIEW.md](./JAI_GOVERNANCE_REVIEW.md) (introduced in v1.4) defines the read-only audit workflow for the global JAI standard itself. Findings route through the existing Enhancement Intake gate (`JAI_GOVERNANCE_STACK.md §4`); the command cannot edit global files, bump `JAI_VERSION`, append `JAI_CHANGELOG.md`, or alter safety rules. The companion command is `JAI governance review` ([JAI_COMMANDS.md](./JAI_COMMANDS.md) §15).

**Scope: global only.** Not duplicated inside any project `/jai/`.

---

## Global agent orchestrator architecture

[JAI_AGENT_ARCHITECTURE.md](./JAI_AGENT_ARCHITECTURE.md) (introduced in v1.7) defines the four-role conceptual model — Orchestrator (Main Claude / project manager), Agent 1 (Builder), Agent 2 (Security / Risk Reviewer), Agent 3 (Test / Validation Reviewer) — under which any JAI-conformant assistant operates when a task crosses build → review → validate. Agent 2 inherits its checklists from `JAI_RUNTIME_ADVISORY_REVIEWER.md` (§1, §5, §6, §8, §9, §11, §11.6–§11.9, §13) in full; Agent 1's output shape is `JAI_DEV_RESPONSE_FORMAT.md §3` (ten sections). The roles are conceptual personas one Main Claude session adopts in sequence — **not** literal Claude Code subagent delegation. No `.claude/agents/` files, no `settings.json` changes, no hooks. S1–S8 from [`policies/SAFETY_RULES.md`](./policies/SAFETY_RULES.md) inherited verbatim. Companion: no new top-level command in `JAI_COMMANDS.md` and no new §16 alias in v1.7.

**Scope: global only.** Not duplicated inside any project `/jai/`.

---

## Versioning rules (summary)

Full rules in [JAI_GOVERNANCE_STACK.md §3](./JAI_GOVERNANCE_STACK.md#3--versioning-model).

- **MAJOR (vN.0)** — breaking structural change only (file removed, renamed without alias, schema reshape, policy reversal).
- **MINOR (vN.M)** — additive. New optional file, new skill, new template, new policy that defaults safe. New required files with skip-if-missing fallback are also MINOR.
- A change is real only when the file is committed here, [JAI_CHANGELOG.md](./JAI_CHANGELOG.md) has an entry, and `JAI_VERSION` is bumped.

---

## How this file evolves

When the standard's shape changes, this file changes alongside the implementation plan. They never disagree without a recorded reason. If they appear to disagree, the implementation plan wins for project-level structural questions; the disagreement gets escalated through the Enhancement Intake workflow ([governance §4](./JAI_GOVERNANCE_STACK.md#4--enhancement-workflow)).
