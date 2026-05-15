# [DRAFT] JAI_AUTOMATION_LANES.md

**Status:** [DRAFT] — sandbox prototype for operator review
**Target version:** vNEXT-draft (additive)
**Plan reference:** [`role-developer-task-research-shimmering-anchor.md`](file:///C:/Users/Mitch/.claude/plans/role-developer-task-research-shimmering-anchor.md) §"Automation Lanes"
**Inheritance:** All five lanes inherit S1–S8 from [`policies/SAFETY_RULES.md`](file:///C:/Users/Mitch/.claude/jai-standard/policies/SAFETY_RULES.md) verbatim. No lane weakens, renames, or renumbers any S-rule.
**Scope:** Global only. Not duplicated inside any project `/jai/`.

## 0. Purpose

`JAI_AUTOMATION_LANES.md` defines the JAI-native automation contract: which operations may be automated under what approval, which remain manual-only, and which are explicitly forbidden regardless of approval claim. The contract reduces approval friction for **clean, low-risk, reversible work** (typo fixes, conformance refresh, drift docs, batched docs commits) without weakening S1–S8 or letting automation reach `.env`, Docker volumes, migrations, providers, runtime state, Redis/Postgres, API keys, or active source code without explicit approval.

**Key framing:** JAI is **not** anti-automation. JAI is anti-*uncontrolled* automation.

## 1. Three Categories of Automation

| Category | Definition | Examples | Status |
|---|---|---|---|
| **Allowed — JAI-native controlled** | Owned by the JAI standard, governed by an explicit lane contract, never weakens S1–S8, fully reversible, fully auditable | Conceptual 4-role personas in one session per [`JAI_AGENT_ARCHITECTURE.md`](file:///C:/Users/Mitch/.claude/jai-standard/JAI_AGENT_ARCHITECTURE.md); batched Fast Lane approval (1 approval / N analogous changes); read-only drift audit; conformance manifest auto-update after approved migration; pre-flight `git status`/`git diff`/`git log` per WORKFLOW_RULES.md | **Allowed** |
| **Rejected — third-party / uncontrolled** | Imported automation that bypasses JAI's lane contract or runs without explicit operator gates | dotclaude marketplace plugins; lasso `install.sh` + `uv` curl-bootstrap; `/setupdotclaude` auto-customize; `format-on-save.sh` writing files without lane approval; `auto-test.sh` running commands without lane approval; literal Claude Code Task subagents that spawn child sessions; Morph LLM API/MCP routing code through external SaaS | **Rejected** |
| **Deferred — future hooks under contract** | Defensive automation that may be considered later, only after operator approval and a strict `JAI_HOOK_CONTRACT.md` | PowerShell-portable session-start hook injecting branch+dirty status (read-only); pre-write hook rejecting `.env` writes (defensive only, never approving); cron-style drift detection (read-only reporting); secret-pattern pre-write scanner (warn-or-block, never silent-allow); Anthropic security-guidance pre-tool hook | **Deferred** until `JAI_HOOK_CONTRACT.md` is drafted and approved |

The rest of this file governs the **Allowed** category. The **Rejected** category remains rejected; concept-only extraction may inform JAI-native equivalents but never adoption. The **Deferred** category waits on `JAI_HOOK_CONTRACT.md`.

## 2. The Five Lanes

The lane is determined by **what the change touches**, not by what the operator wants to call it. Borderline cases default to the stricter lane. **Operator preference cannot downgrade a lane that an actual touched-file or command triggers.**

### 2.1 Manual Lane

**Definition:** Operator does the work directly; no agent action.

| Aspect | Spec |
|---|---|
| Allowed file types | Anything operator chooses |
| Forbidden file types | None (operator's discretion) |
| Allowed commands | All operator-issued |
| Forbidden commands | None (operator's discretion) |
| Commit allowed | Yes, by operator |
| Push allowed | Yes, by operator |
| Required approval | Self (operator) |
| Required verification | Operator self-verifies |
| Rollback expectations | Operator-managed |
| Use cases | `.env` edits; `.claude/settings*.json` edits; Docker lifecycle; migrations; production deploys; signing-key handling; any Blocked Lane operation that the operator handles personally |
| Triggers | Always available; operator-invoked |

### 2.2 Fast Lane

**Definition:** Low-risk, reversible, docs-only or governance-doc work; **one batched operator approval covers N analogous changes**.

| Aspect | Spec |
|---|---|
| Allowed file types | `<project>/jai/*.md` (governance docs); `<project>/docs/*.md`; project-root `README.md`; `<project>/jai/JAI_CONFORMANCE.md` updates; `PROJECT_EXCEPTIONS.md` Narrow/Augment entries; `CLEANUP_REGISTER.md` close-outs; `DECISION_LOG.md` append-only entries; `CURRENT_STATE.md` / `HANDOFF.md` status refreshes |
| Forbidden file types | Source code (any language); tests; `package.json` / `package-lock.json`; `pyproject.toml`; `Cargo.toml`; `go.mod`; `.env*`; Docker compose; `Dockerfile`; migration files; CI/CD configs; `.claude/settings*.json`; `secrets/*`; `credentials/*`; `*.pem`; `*.key` |
| Allowed commands | `git status`, `git diff`, `git log`, `git branch`, `git add <docs-paths-only>`, `git commit -m "..."`, `git push` (only if explicitly named in approved batch) — all pre-documented in `<project>/jai/WORKFLOW_RULES.md` |
| Forbidden commands | Anything not pre-documented; `npm install`; `pip install`; `docker *`; migrations; `git push --force*`; `git config`; `rm -rf`; `git reset --hard`; `git clean -f`; branch deletion; provider API calls |
| Commit allowed | **Yes**, batched per approved Fast Lane batch |
| Push allowed | **Yes, only when** the operator's batch approval explicitly includes push (default = local commit only; push is a separate per-batch opt-in) |
| Required approval | One batched operator approval covers N analogous changes; batch must list all touched files; approval is **not transitive** across batches |
| Required verification | Byte-visible diff before commit; post-commit confirmation of commit hash + `git status`; touched-file count matches batch declaration |
| Rollback expectations | Single `git revert <commit>` returns to pre-batch state; entry in `DECISION_LOG.md`; revert of a pushed commit is itself a Fast Lane action requiring its own batched approval |
| Use cases | Typo / grammar fixes; stale phase-counter wording; cross-link additions; banned-phrase removal; markdown link repair; CURRENT_STATE / HANDOFF status updates; conformance file refresh; CLEANUP_REGISTER close-outs; **version-drift docs cleanup**; **`JAI_CONFORMANCE.md` content updates** |
| Triggers | All Fast Lane criteria above hold AND no Strict / Review / Blocked lane category triggers |

### 2.3 Review Lane

**Definition:** Non-trivial governance/docs changes that need full reviewer chain but touch no source/runtime; per-batch approval (not Fast-Lane-batched across unrelated work).

| Aspect | Spec |
|---|---|
| Allowed file types | Governance core (`<project>/jai/CLAUDE.md`, `WORKFLOW_RULES.md`, `SECURITY_RULES.md`, `THREAT_MODEL.md`, `PROCESS_CONVENTIONS.md`, `PROJECT_CONTEXT.md`); key project docs (`docs/RUNBOOK.md`, `docs/SETUP.md`, phase plans); JAI standard files; new files in `<project>/jai/`; cross-repo movement of any skill/rule/non-trivial doc |
| Forbidden file types | Source code; tests; `.env*`; Docker; migrations; CI configs; `.claude/settings*.json`; secrets; runtime configs |
| Allowed commands | All Fast Lane commands plus pre-documented build/typecheck/lint commands per `<project>/jai/WORKFLOW_RULES.md` (e.g., `npm test`, `tsc -b --noEmit`, `npm run lint`) |
| Forbidden commands | Anything not pre-documented in WORKFLOW_RULES.md; Strict Lane commands; Blocked Lane commands |
| Commit allowed | **Yes**, after Builder → Agent 2 review → operator approval at Orchestrator gate |
| Push allowed | **Yes**, separate explicit per-batch approval (push is **never** coupled to commit approval) |
| Required approval | Per-batch operator approval; batch may bundle related changes but not unrelated ones |
| Required verification | 10-section dev report (per [`JAI_DEV_RESPONSE_FORMAT.md`](file:///C:/Users/Mitch/.claude/jai-standard/JAI_DEV_RESPONSE_FORMAT.md)); Agent 2 reviewer report (per [`JAI_RUNTIME_ADVISORY_REVIEWER.md §11`](file:///C:/Users/Mitch/.claude/jai-standard/JAI_RUNTIME_ADVISORY_REVIEWER.md)); restricted-area negative receipts; Agent 3 invoked if relevant tests exist |
| Rollback expectations | Per-action revert plan documented in dev report Section 6 (Risk/Scope); rollback is itself a Review Lane action |
| Use cases | Adding a new JAI standard file; substantive `SECURITY_RULES.md` change; `THREAT_MODEL.md` residual addition; new skill or template file; cross-repo doc port; `JAI_CONFORMANCE.md` schema changes (versus content updates which are Fast Lane) |
| Triggers | Touches Review Lane file types AND no Strict / Blocked category triggers |

### 2.4 Strict Lane

**Definition:** Source code, infrastructure, dependencies, schema — anything that changes runtime behavior; **per-action operator approval (not batched)**.

| Aspect | Spec |
|---|---|
| Allowed file types | Source code (with explicit Orchestrator scope statement); tests; configs; `package.json` (if approved); `Dockerfile` (if approved); compose changes (if approved); CI configs (if approved) |
| Forbidden file types | `.env*` (still S2); `secrets/`; `credentials/`; `*.pem`; `*.key`; live DB files; runtime state files; Docker volume mount paths (`pgdata`, `redisdata`); signing materials; mnemonic files |
| Allowed commands | Pre-documented in `<project>/jai/WORKFLOW_RULES.md` AND named in Orchestrator handoff (dual approval gate per [`JAI_AGENT_ARCHITECTURE.md §3.2`](file:///C:/Users/Mitch/.claude/jai-standard/JAI_AGENT_ARCHITECTURE.md)) |
| Forbidden commands | Anything not dual-approved; Docker lifecycle without explicit approval; migration commands without explicit approval; outbound provider API calls without explicit approval; `git push --force` to protected branches; `git config`; `rm -rf` on system paths |
| Commit allowed | **Yes**, per-action operator approval at Orchestrator gate |
| Push allowed | **Yes**, separate explicit per-action approval (never coupled to commit approval) |
| Required approval | **Per-action**, not batched; Orchestrator gates each commit and each push separately |
| Required verification | Full Builder → Agent 2 (Security) → Agent 3 (Test) → Orchestrator → Operator chain; tests run only if Agent 3 gating conditions met (per [`JAI_AGENT_ARCHITECTURE.md §3.4`](file:///C:/Users/Mitch/.claude/jai-standard/JAI_AGENT_ARCHITECTURE.md)); restricted-area negative receipts |
| Rollback expectations | Per-action revert plan documented before action; sandbox dry-run if change touches schema or infrastructure; rollback itself is Strict Lane |
| Use cases | Source code change; dependency add/upgrade/downgrade; Docker change; migration; provider config; scheduler change; route/consumer wiring change; host-binding change |
| Triggers | Any of: source code, Docker, `.env`, migrations, providers, schedulers, dependencies, DB, Redis/Postgres, runtime state, deployment, host-binding changes (per [`JAI_AGENT_ARCHITECTURE.md §5 Strict Lane`](file:///C:/Users/Mitch/.claude/jai-standard/JAI_AGENT_ARCHITECTURE.md)) |

### 2.5 Blocked Lane

**Definition:** Operations no automation may perform under any approval claim; only Manual Lane handles these.

| Aspect | Spec |
|---|---|
| Always-blocked file types | `.env*` (any read or write); `secrets/*`; `credentials/*`; `*.pem`; `*.key`; mnemonic files; signing keys; private key blocks; live runtime data files; Docker volume contents (`pgdata`, `redisdata`); production data exports |
| Always-blocked commands | `git push --force` to protected branches; `git config` (init / global); `rm -rf` on system dirs / `$HOME` / `$VAR` / unresolved variables; `DROP TABLE`; `DROP DATABASE`; `DROP SCHEMA`; `DELETE FROM` without `WHERE`; `TRUNCATE`; `chmod 777`; `chmod a+rwx`; `curl ... \| sh`; `wget ... \| sh`; `bash -c "$(curl ...)"`; `eval` of network-fetched content; `dd of=/dev/`; `mkfs`; `>/dev/sd*`; package publish without `--dry-run`; `docker volume rm` on protected volumes; migrations in production; outbound provider API calls without approval; **reading any always-blocked file to "verify" a violation** |
| Commit allowed | **No** |
| Push allowed | **No** |
| Required approval | **Even operator approval does not move a Blocked operation to another lane.** If action is genuinely needed, operator handles it personally in Manual Lane. |
| Required verification | Hard-block at hook layer (if hooks are eventually approved per `JAI_HOOK_CONTRACT.md`); Agent 2 reviewer hard-block per [`JAI_RUNTIME_ADVISORY_REVIEWER.md §6`](file:///C:/Users/Mitch/.claude/jai-standard/JAI_RUNTIME_ADVISORY_REVIEWER.md); reading a restricted file to verify the violation is **itself** a violation (per [`JAI_AGENT_ARCHITECTURE.md §3.3 Hard read-prohibition`](file:///C:/Users/Mitch/.claude/jai-standard/JAI_AGENT_ARCHITECTURE.md)) |
| Rollback expectations | N/A — operation never starts |
| Use cases | Reading `.env`; touching production data; force-pushing to main; running destructive SQL; modifying Docker volume contents; outbound provider calls without approval |
| Triggers | Always; **cannot be downgraded** |

## 3. Lane Determination Algorithm

When a task arrives, the Orchestrator (per [`JAI_AGENT_ARCHITECTURE.md §3.1`](file:///C:/Users/Mitch/.claude/jai-standard/JAI_AGENT_ARCHITECTURE.md)) classifies it:

```
Step 1: Identify all touched file types and commands the task would invoke.
Step 2: Check Blocked Lane first — if any trigger, refuse and route to Manual Lane.
Step 3: Check Strict Lane — if any trigger, lane = Strict.
Step 4: Check Review Lane — if any trigger and no Strict trigger, lane = Review.
Step 5: Check Fast Lane criteria — if all hold and no higher-lane trigger, lane = Fast.
Step 6: If none apply (e.g., the task is operator-personal), lane = Manual.
```

If the operator says "treat this as Fast Lane" when a Strict trigger applies, the Orchestrator **rejects the downgrade**, names the specific category engaged, and asks for explicit re-scope ("remove the dependency change to restore Fast Lane eligibility"). This mirrors [`JAI_AGENT_ARCHITECTURE.md §5 Borderline cases`](file:///C:/Users/Mitch/.claude/jai-standard/JAI_AGENT_ARCHITECTURE.md) — actual Strict Lane triggers cannot be downgraded.

## 4. Friction Reduction Examples

The lane contract reduces approval friction for low-risk reversible work without weakening S1–S8:

| Work type | Today (pre-vNEXT-draft) | Under JAI_AUTOMATION_LANES.md |
|---|---|---|
| Typo / banned-phrase / link fix across 5 files in `jai/` | 5 separate pre-change reports + 5 approvals | **1 Fast Lane batch approval** |
| Update `CURRENT_STATE.md` + `HANDOFF.md` after a session | 2 separate approval cycles | **1 Fast Lane batch** |
| Refresh `JAI_CONFORMANCE.md` after operator-approved migration step | Separate Review Lane cycle | **1 Fast Lane action** (content update; schema changes remain Review Lane) |
| Add a paragraph to `PROCESS_CONVENTIONS.md` explaining a new pattern | Review Lane | Stays Review Lane (substantive governance change) |
| Add a dependency to `package.json` | Strict Lane | Stays Strict Lane (per-action approval) |
| Edit `.env` | Manual Lane only | Stays Manual Lane only (Blocked) |
| Force-push to main | Forbidden | Stays Blocked (operator must do manually if ever justified) |

## 5. Future Hook Contract (Deferred — `JAI_HOOK_CONTRACT.md`)

If hooks are ever approved, they must satisfy a strict contract written *before* any hook is implemented. The contract spec is **not drafted in this file**; it is added as a deferred follow-up that requires its own operator approval before any hook prototyping begins. The five lanes above provide all near-term friction reduction without requiring hooks.

Required contract terms (when written):

1. **Defensive only** — block, warn, or inject context (read-only). Hooks may **never** approve, modify `.claude/settings*.json`, or perform any Blocked Lane operation.
2. **Cross-platform** — must run on PowerShell AND bash; bash-only hooks rejected.
3. **Auditable** — every hook action logs to `<project>/jai/HOOK_LOG.md`.
4. **Reversible** — any hook that modifies state (other than the formatter exception) is rejected.
5. **Testable** — fixture-based test coverage required.
6. **Operator-disable-able** — env var (e.g., `JAI_HOOK_<NAME>_DISABLED=1`) to disable without deletion.
7. **Lane-respecting** — hooks may not bypass any lane gate.
8. **Fail-closed if missing dependency** — if `jq`, `gh`, or required binary is missing, deny the action; never silently skip.

Hook prototypes live in `Repo_Lab/lab/hooks-prototype/` (not yet created — pending `JAI_HOOK_CONTRACT.md` approval). No hook is installed live.

## 6. Maintenance Rule

Changes to this file follow the standard JAI governance process:

1. Enhancement Intake (`JAI_COMMANDS.md §8`) — propose the change.
2. Standard Update (`JAI_COMMANDS.md §9`) — apply the approved change.
3. Version bump in `JAI_VERSION` and entry in `JAI_CHANGELOG.md`.

In-place edits forbidden. The intake → update path is mandatory.
