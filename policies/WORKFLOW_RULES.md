# [DRAFT] policies/WORKFLOW_RULES.md (universal portion)

**Status:** [DRAFT] — sandbox prototype for operator review
**Target version:** vNEXT-draft (additive — fills a gap noted as planned in [`JAI_STANDARD.md`](file:///C:/Users/Mitch/.claude/jai-standard/JAI_STANDARD.md))
**Inheritance:** Inherits S1–S8 from [`SAFETY_RULES.md`](file:///C:/Users/Mitch/.claude/jai-standard/policies/SAFETY_RULES.md). Defines the universal workflow rules every project inherits. Projects extend in `<project>/jai/WORKFLOW_RULES.md`.
**Scope:** Universal.

## 0. Purpose

Universal workflow rules every JAI-conformant project inherits. Defines the change cadence, audit-before-change discipline, and pre-change report structure that every project follows. Project-specific commands and lane permissions live in `<project>/jai/WORKFLOW_RULES.md`.

## 1. Audit Before Change

Every change starts with **read-only inspection** of the surface to be changed:

- Use `Read`, `Glob`, `Grep` (file-system-level) to inspect existing code, configs, and docs
- Do not assume; confirm via inspection
- For multi-file changes, the audit phase is its own step before any write

This applies to every Lane except Manual.

## 2. Explain Before Changing

Before making any change, the Builder names:

1. The files that will be touched
2. The rule the change follows (S1–S8, project SECURITY_RULES, project WORKFLOW_RULES)
3. The rollback path
4. The verification that will be run

This is the structure of the **pre-change report** (per `JAI_DEV_RESPONSE_FORMAT.md` Sections 3, 4, 5, 6).

## 3. One Change at a Time

For Strict Lane work:

1. Save a dated ops note before each change (project-specific location, e.g., `docs/ops/YYYY-MM-DD-<topic>.md`)
2. Make one small change at a time
3. Run pre-documented verification (e.g., `npm test`, `tsc -b --noEmit`) per Lane
4. Save verification output in the dated note
5. Include rollback instructions

For Fast Lane work, batched analogous changes are permitted per [`JAI_AUTOMATION_LANES.md §2.2`](../JAI_AUTOMATION_LANES.md).

## 4. Do Not Expand Scope

- A bug fix does not include unrelated cleanup
- A one-shot change does not include helpers, abstractions, or refactors
- A documentation fix does not include rewording adjacent content
- The Builder halts and round-trips to the Orchestrator if scope appears insufficient (per [`JAI_AGENT_ARCHITECTURE.md §3.2`](file:///C:/Users/Mitch/.claude/jai-standard/JAI_AGENT_ARCHITECTURE.md))

## 5. Plan Required for Non-Trivial Work

For any change that:
- Touches more than one file in source code, OR
- Touches root `AGENTS.md` or the project's `jai/` core (CLAUDE.md, SECURITY_RULES, WORKFLOW_RULES, THREAT_MODEL), OR
- Crosses any Strict Lane trigger

A plan is written before the work begins. The plan format is defined per project in `<project>/jai/skills/planning.skill.md` (lifted from canonical at `~/.claude/jai-standard/skills/planning.skill.md` per lazy-migration). The plan goes to operator review before execution.

## 6. Banned Phrases in Plans

The canonical banned-phrases list lives in [`skills/planning.skill.md`](../skills/planning.skill.md) §"Banned phrases — mechanical fail at review". This file does not duplicate it; updates land in the skill file via the standard intake → update path.

Plans containing any banned phrase from that list are not approvable. Reviewer (per [`JAI_RUNTIME_ADVISORY_REVIEWER.md`](file:///C:/Users/Mitch/.claude/jai-standard/JAI_RUNTIME_ADVISORY_REVIEWER.md)) treats a hit as a mechanical fail.

## 7. Pre-Documented Commands

Each project's `<project>/jai/WORKFLOW_RULES.md` lists every command that may be run by an agent under any Lane. Commands not in the list cannot be invoked. Adding to the list is a Review Lane action; the addition flows through the standard pre-change → approval → change → post-change cadence.

The list typically includes:

- Read-only git: `git status`, `git diff`, `git log`, `git branch`
- Build/test/lint per project stack: `npm test`, `tsc -b --noEmit`, `npm run lint`, `cargo build`, `pytest`
- Read-only Docker: `docker compose ps` (only)
- Pre-documented Bash patterns per project's stack

Commands NOT in the list cannot be invoked even if they "appear read-only" (e.g., `gh pr view` is invokable only if the project explicitly lists it).

## 8. Approval Gates (universal)

These changes require explicit operator approval at the Orchestrator gate (per [`JAI_AGENT_ARCHITECTURE.md §4.5`](file:///C:/Users/Mitch/.claude/jai-standard/JAI_AGENT_ARCHITECTURE.md)):

- Any commit
- Any push (separate approval from commit)
- Any direct push to `main` (must be named explicitly; never inferred from ordinary push approval)
- Any branch creation, deletion, force-push, or merge
- Any merge (separate approval from push or PR approval)
- Any dependency add/upgrade/downgrade
- Any Docker change (compose, Dockerfile, lifecycle)
- Any migration
- Any provider config change
- Any scheduler change
- Any deploy, rebuild, restart, runtime-state change, or flag flip
- Any host-binding change
- Any change to `.claude/settings*.json`
- Any change crossing a Strict Lane trigger per [`JAI_AGENT_ARCHITECTURE.md §5`](file:///C:/Users/Mitch/.claude/jai-standard/JAI_AGENT_ARCHITECTURE.md)

Approval is **per-batch** for Fast/Review Lanes and **per-action** for Strict Lane. Approval is **not transitive** across batches or actions: commit approval does not imply push, push does not imply merge, merge does not imply deploy/rebuild/restart, and implementation approval does not imply flag-flip approval.

## 9. Forbidden Without Operator Approval (universal)

These actions are blocked regardless of any Lane until the operator explicitly approves them:

- `git push --force` to any branch
- `git config` (init or global)
- Branch deletion
- Tag deletion
- `git reset --hard`
- `git clean -f`
- `rm -rf` matching any pattern in [`catalogs/DANGEROUS_COMMANDS.md §3`](../catalogs/DANGEROUS_COMMANDS.md) (root `/`, home `~` / `$HOME`, unresolved variables, system directories such as `/usr` `/etc` `/var` `/bin` `/sbin` `/lib` `/opt` `/root` `/boot`, paths crossing project root, Docker volume mounts). The catalog is the canonical pattern list; if the summary above and the catalog diverge, the catalog wins. Other `rm -rf` invocations follow the Lane that the touched files trigger; operator handles ambiguous destructive removals in Manual Lane
- Database schema changes
- Docker volume operations against `pgdata`, `redisdata`, or any production volume

## 10. Project Extensions

Each project's `<project>/jai/WORKFLOW_RULES.md` extends this file via Narrow/Augment per [`JAI_OVERRIDE_CONTRACT.md`](../JAI_OVERRIDE_CONTRACT.md). Examples:

- **Narrow:** Project restricts `git fetch *` to `git fetch origin` only.
- **Augment:** Project requires every consumer-wiring pre-change report to include a BullMQ queue-state check.

## 11. Maintenance Rule

Changes to this file are MINOR if additive (new universal rule, new approval gate). MAJOR if removing or weakening any existing rule.
