# JAI Project Implementation Plan — v1.0

**Standard version covered:** JAI v1.0
**Established:** 2026-05-08
**Lives at:** `~/.claude/jai-standard/JAI_PROJECT_IMPLEMENTATION_PLAN.md`
**Companion docs:** [`JAI_STANDARD.md`](./JAI_STANDARD.md) · [`JAI_GOVERNANCE_STACK.md`](./JAI_GOVERNANCE_STACK.md) · [`JAI_CHANGELOG.md`](./JAI_CHANGELOG.md)

---

## 0 · Purpose

This document is the **standard process for adding `/jai/` to a new repo**. It is the builder/manual side of the JAI system. The output of running this plan against a fresh project is a fully-conforming `<project>/jai/` profile, formally linked to the current JAI Standard version, ready for ongoing Drift Audits and Migration Passes.

This plan is **prescriptive about structure** (which files exist, which skills exist, which templates exist, what the read order is) and **descriptive about content** (each file's content is filled from the project's actual state via Pass 1 audit findings, not from a global template). Two projects following this plan will produce the same `/jai/` shape; their `/jai/` content will reflect their distinct architectures.

If this plan and a project's reality disagree on shape, the plan wins for v1.0 (i.e. add the missing file, even if empty). If they disagree on content (e.g. the plan mentions a section that doesn't apply), document the divergence in `jai/PROJECT_EXCEPTIONS.md` rather than silently omitting.

---

## 1 · Relationship to Global JAI Standard

| Layer | Lives at | Authority |
|---|---|---|
| **Global JAI Standard** | `~/.claude/jai-standard/` | Defines the cross-project shape, cross-cutting policies, canonical skills, canonical templates, and version history. The current version is in [`JAI_VERSION`](./JAI_VERSION). The current spec is in [`JAI_STANDARD.md`](./JAI_STANDARD.md). |
| **This document** | `~/.claude/jai-standard/JAI_PROJECT_IMPLEMENTATION_PLAN.md` | The builder/manual for applying the global standard to **one project**. Cited by [`JAI_STANDARD.md`](./JAI_STANDARD.md) as the procedure for v1.0 conformance. |
| **Project `/jai/` profile** | `<project>/jai/` | The output of running this plan. Each project carries its own profile; profiles never cross-pollinate. |

When the global standard bumps, this document also bumps in lockstep — the procedure for applying v1.1 lives in `JAI_PROJECT_IMPLEMENTATION_PLAN.md` at v1.1 of the standard. Older versions remain accessible through git history (once the standard is promoted to a dedicated repo) or through `JAI_CHANGELOG.md` migration notes (until then).

---

## 2 · Required Pre-Read

Before running this plan against a new repo, read the following in order. Each takes 2–10 minutes; the full pre-read fits in a single session.

| # | File | Purpose |
|---|---|---|
| 1 | [`~/.claude/jai-standard/JAI_VERSION`](./JAI_VERSION) | One line. Tells you which standard version you're applying (e.g. `v1.0`). |
| 2 | [`~/.claude/jai-standard/JAI_STANDARD.md`](./JAI_STANDARD.md) | The spec. Tells you the required-files list, the cross-cutting policy/catalog/skill/template inventory, and the conformance criteria for the version above. |
| 3 | [`~/.claude/jai-standard/JAI_GOVERNANCE_STACK.md`](./JAI_GOVERNANCE_STACK.md) | The governance rules. Tells you the workflows (Enhancement Intake §4, Project Sync §5), the safety rules (§6), the report format (§7), and the locked-in decisions (§8). |
| 4 | This file | The procedure for applying steps 1–3 to one project. |

If the project is **not new** — i.e. it already has a `/jai/` directory — you are not running this plan; you are running a Drift Audit + Migration Pass per [JAI_GOVERNANCE_STACK §5](./JAI_GOVERNANCE_STACK.md#5--existing-project-sync-workflow). Stop here and switch workflows.

---

## 3 · Non-Negotiable Safety Rules

**Canonical S1–S8:** [`policies/SAFETY_RULES.md`](./policies/SAFETY_RULES.md). They apply across both Pass 1 (audit) and Pass 2 (creation) of this implementation plan, exactly as defined in the canonical file.

### 3.1 · Execution-context extensions (E1–E4)

The implementation plan adds four execution-specific safety rules on top of S1–S8. They layer on; they do not replace.

| # | Rule |
|---|---|
| **E1** | **Credential specificity.** S2's prohibition on `.env` and credential files extends to API keys, tokens, private keys, OAuth secrets, and DB passwords. If audit findings need to mention a secret-handling concern, describe the location and the risk; never include the value. |
| **E2** | **No branch deletion.** No `git branch -D`, no `git push :branch`, no remote branch removal. Stale branches get listed; the user decides. |
| **E3** | **No Claude settings changes.** No edits to `~/.claude/settings.json`, no edits to `<project>/.claude/settings.json`, no edits to `.claude/settings.local.json`. |
| **E4** | **No hook installs.** No edits to `.claude/hooks/`, no creation of new hooks, no enabling of existing-but-disabled hooks. |

**Versioning of E1–E4:** adding is a MINOR bump per `JAI_GOVERNANCE_STACK.md §3`; renumbering or removing is a MAJOR bump. (S1–S8 versioning lives with the canonical file at `policies/SAFETY_RULES.md §2`.)

> **Renumbering note (v1.5):** prior to v1.5, this section listed S1–S10. The canonical S1–S8 are now in `policies/SAFETY_RULES.md`; the four impl-plan-specific additions (formerly S2 broadened-credentials, S7 branch-deletion, S8 settings, S9 hook installs) are renamed E1–E4 to avoid label collision with the canonical S-numbers. The former S6 (destructive commands), S10 (approval before writes), and S1/S3/S4/S5 entries were duplicates of canonical S6/S7/S2/S3/S4/S5 and are now covered by reference.

---

## 4 · Pass 1 — Audit Only

**Purpose:** Build a complete read-only picture of the new repo before any `/jai/` file is created. Pass 1 produces a single audit report, returned in chat. No files are written during Pass 1.

**Output format:** A markdown report with each subsection below filled in. The report becomes the primary input to Pass 2.

### 4.1 · Docs inventory

List every existing documentation file in the repo. Common locations: root (`README.md`, `SETUP.md`, `CONTRIBUTING.md`, `CHANGELOG.md`), `docs/`, `backend/docs/`, `frontend/docs/`, inline `*.md` near code. Tag each with last-modified date.

### 4.2 · Docs classification

For each doc from §4.1, classify as one of:
- **Authoritative** — actively maintained, content matches code reality.
- **Stale** — obviously out of date; what was true when written may not be true now.
- **Aspirational** — describes desired future state, not current state.
- **Reference** — generated, vendor, or third-party (e.g. API specs, license files, copied tutorials).

This classification feeds §4.10 (source-of-truth candidates).

### 4.3 · Infrastructure inventory

Record:
- Containers / services declared in `docker-compose.yml` (or k8s manifests / process managers).
- Build tooling (Makefile, npm scripts, Cargo, etc.) — list targets, not contents.
- Runtime entry points (server start commands, CLI entry, scheduled tasks).
- Storage (volumes, bind mounts, host paths).
- External dependencies as declared in compose / config (databases, caches, brokers, queues, observability stacks).

Read-only. Do not start or stop anything per S3.

### 4.4 · Port binding / security exposure findings

For every published port found in §4.3, classify per the global "local-dev IP/port binding" baseline (already in scope as a cross-cutting policy):

| Service | Current binding | Safe? | Recommendation |
|---|---|---|---|
| backend / API | `127.0.0.1:8000:8000` | ✓ | (none) |
| postgres | `5432:5432` | ✗ | Move to internal network only or bind `127.0.0.1:5432:5432` |

Flag any of: `0.0.0.0:<port>`, `:::<port>`, implicit `"8000:8000"` (no IP), `5432:5432` published, `6379:6379` published, any unauthenticated service on a public/LAN interface. Pause and surface findings; do not change ports per S3.

### 4.5 · Secret-handling findings

Read-only review of how secrets enter the project:
- `.env` / `.env.*` files — list filenames + size + last-modified, **never values**.
- Hardcoded credentials in code — grep patterns from `~/.claude/jai-standard/catalogs/SECRET_REGEX_CATALOG.md` once that catalog exists; until then, look for `AKIA[0-9A-Z]{16}`, `(ghp_|gho_|ghs_|ghr_|github_pat_)…`, `sk-[a-zA-Z0-9]{20,}`, `xox[bpras]-…`, `-----BEGIN … PRIVATE KEY-----`, and connection strings with embedded user:pass.
- Secret-store integrations (e.g. AWS Secrets Manager calls, Vault clients, GCP KMS calls).
- Secret-shaped config keys without env-var sourcing.

Do not open `.env*` files per S1. Filename + size is enough.

### 4.6 · Script risk findings

For every shell/Python/PowerShell script in the repo (e.g. `scripts/`, `bin/`, root-level `*.sh`/`*.ps1`):
- What does it do?
- Does it modify the host (install, mkdir at root, edit `/etc`, write to `~`)?
- Does it call `curl | sh` or download-and-execute?
- Does it require sudo / elevation?
- Does it touch credentials, the database, or external APIs?

Read-only review. Do not run any script per S6.

### 4.7 · Dependency / security review findings

- `package.json` / `requirements.txt` / `Cargo.toml` / `go.mod` / `Gemfile` — list direct dependencies + version pins (or absence thereof).
- Lockfiles present? — note presence/absence; a missing lockfile is a finding.
- Known-vulnerable versions — if `npm audit` / `pip audit` / equivalent has been run recently, summarize; do not run them per S5 (they hit external indexes; defer to user).
- Postinstall scripts in `package.json` — list and flag.
- CDN imports without SRI in HTML/templates.

### 4.8 · Source-of-truth candidates

From §4.1–§4.7, pick which existing doc (if any) best matches each `/jai/` file the standard requires:

| `/jai/` file | Source-of-truth candidate | Notes |
|---|---|---|
| `PROJECT_CONTEXT.md` | `README.md` §"What this project does" | Trim to project context only; no install instructions. |
| `ARCHITECTURE.md` | `docs/architecture.md` | Authoritative per §4.2. |
| `INFRASTRUCTURE.md` | `docker-compose.yml` + `SETUP.md` | Compose binding-corrected per §4.4 findings. |
| `SECURITY_RULES.md` | (none) | Greenfield; extend `~/.claude/jai-standard/policies/SECURITY_RULES.md` once that policy lands. Until then, list project-specific rules only. |
| `THREAT_MODEL.md` | (none) | Greenfield; populate from §4.4–§4.7 findings. |
| `WORKFLOW_RULES.md` | `CONTRIBUTING.md` | Adapt branch policy + review rules. |
| `VULNERABILITY_REVIEW.md` | (none if no audit) | Greenfield; populate from §4.7 findings. |

Mark a file as **Greenfield** if no existing doc covers it; that file is created from audit findings in Pass 2.

### 4.9 · Proposed `/jai/` structure

Restate the §5 layout (below) as the proposed structure for this specific project. Confirm no existing `<project>/jai/` directory exists; if one does, this is a Drift Audit, not Pass 1.

### 4.10 · Restricted areas untouched

Explicitly list what was **not** touched during Pass 1, by category:
- `.env` / `.env.*` files — listed by name only.
- Docker — no `docker` commands run.
- Migrations — no migration commands run.
- Providers — no outbound API calls.
- Scheduler — no cron / Task Scheduler / launchd reads/writes.
- App code — no source files modified.
- Claude settings — no `.claude/` edits.
- Hooks — no hook installs or edits.

This is a confirmation, not a checklist for the user to fill — Pass 1 is read-only, so all these are true by definition. Restating them keeps the report self-auditing.

### 4.11 · git status

`git status --short` output at the end of Pass 1. Should be empty if Pass 1 was actually read-only.

### 4.12 · Next recommended step

End the Pass 1 report with one sentence: typically "Run Pass 2 to create `<project>/jai/` per §5 of the implementation plan, with the source-of-truth mappings above," or "Resolve <specific blocker> before proceeding to Pass 2."

---

## 5 · Pass 2 — Create Project `/jai/`

**Purpose:** Generate the project's `/jai/` profile from the Pass 1 audit. Pass 2 is **write-after-approval** — the proposal (file list + content per file) returns in chat first, the user approves, then files are written.

### 5.1 · Required structure

```
<project>/jai/
├── CLAUDE.md
├── CURRENT_STATE.md
├── PROJECT_CONTEXT.md
├── ARCHITECTURE.md
├── INFRASTRUCTURE.md
├── SECURITY_RULES.md
├── THREAT_MODEL.md
├── VULNERABILITY_REVIEW.md
├── WORKFLOW_RULES.md
├── DECISION_LOG.md
├── CLEANUP_REGISTER.md
├── HANDOFF.md
├── PROJECT_EXCEPTIONS.md
├── archive/
│   └── README.md
├── skills/
│   ├── implementation_review.skill.md
│   ├── frontend_change.skill.md
│   ├── backend_change.skill.md
│   ├── infra_audit.skill.md
│   ├── security_review.skill.md
│   ├── secret_scanning.skill.md
│   ├── dependency_review.skill.md
│   ├── threat_modeling.skill.md
│   ├── docs_cleanup.skill.md
│   └── git_sync.skill.md
└── templates/
    ├── dev_prompt_template.md
    ├── review_report_template.md
    ├── security_report_template.md
    └── handoff_template.md
```

13 root-level files + 1 archive subdir + 10 skills + 4 templates = **28 files** in a fresh Pass 2.

### 5.2 · File-by-file content guide

Each project's content differs; this is the **shape** each file holds.

| File | Contents |
|---|---|
| `CLAUDE.md` | Front-matter version pointer (§6) + project scope statement + the two-tier read model (§8) + any project-specific entry-point notes. |
| `CURRENT_STATE.md` | What's currently live, what's pending, recent changes, observation phases. Updated frequently. |
| `PROJECT_CONTEXT.md` | What this project does, who uses it, why it exists. Stable; rarely changes. |
| `ARCHITECTURE.md` | Services, components, data flow, key abstractions. Source of truth: §4.2 docs + code reading. |
| `INFRASTRUCTURE.md` | Docker, ports (with corrected bindings per §4.4), volumes, networks, deployment, environments. |
| `SECURITY_RULES.md` | Project-specific rules. Extends `~/.claude/jai-standard/policies/SECURITY_RULES.md` once that policy lands; until then, project-only. |
| `THREAT_MODEL.md` | Threat catalog with status (`mitigated` / `partial` / `untouched`). Source: §4.4–§4.7 findings + future review. |
| `VULNERABILITY_REVIEW.md` | Last security audit date, current vulnerabilities, fixes pending. Source: §4.7 findings. |
| `WORKFLOW_RULES.md` | Branch strategy, push policy, review requirements, commit conventions, CI gates. |
| `DECISION_LOG.md` | One row per significant decision, with date + context + outcome. Future Migration Passes append a sync row here per governance §5.5. |
| `CLEANUP_REGISTER.md` | Known cleanup items deferred from main work — the "to remove someday" list. |
| `HANDOFF.md` | Current handoff state for next session. Per-session document; replaced often. |
| `PROJECT_EXCEPTIONS.md` | Documented divergences from the declared JAI standard version (§7). Created even if empty. |
| `archive/README.md` | One-line note: "Older notes, retired skills, and superseded plans live here. Do not edit archive contents; treat as historical record." |

### 5.3 · Skills (10)

Each skill is a `.skill.md` file describing a repeatable workflow. Project-instantiated; reference the canonical specs at `~/.claude/jai-standard/skills/` once those are populated (lazy migration per JAI v1.0).

| Skill | Purpose |
|---|---|
| `implementation_review.skill.md` | Verify what was built matches what was specified. Inputs: spec doc + actual change. |
| `frontend_change.skill.md` | Frontend change checklist (component, styling, accessibility, tests, no PII in logs). |
| `backend_change.skill.md` | Backend change checklist (handler, validation, auth, logging, tests, migration if needed). |
| `infra_audit.skill.md` | Infra audit procedure (ports, volumes, networks, secrets, dependencies). |
| `security_review.skill.md` | Security review procedure. Reference: `~/.claude/jai-standard/policies/THREAT_MODEL.md` once seeded. |
| `secret_scanning.skill.md` | Secret detection procedure. Reference: `~/.claude/jai-standard/catalogs/SECRET_REGEX_CATALOG.md` once seeded. |
| `dependency_review.skill.md` | Dependency review procedure (pinning, lockfile, postinstall, known CVEs). |
| `threat_modeling.skill.md` | Threat modeling procedure. Reference: `~/.claude/jai-standard/catalogs/INJECTION_CATEGORIES.md` once seeded. |
| `docs_cleanup.skill.md` | Docs cleanup procedure (stale → archive, classification refresh, broken links). |
| `git_sync.skill.md` | Git sync procedure (fetch, rebase, conflict-resolution policy, no force-push to protected branches). |

### 5.4 · Templates (4)

| Template | Purpose |
|---|---|
| `dev_prompt_template.md` | Shape for handing a task to a dev or to Claude. Sections: goal, constraints, acceptance criteria, restricted areas. |
| `review_report_template.md` | Shape for review outputs. Sections: scope, findings (severity-ranked), recommendations, follow-ups. |
| `security_report_template.md` | Shape for security findings. Sections: target, attack vector, severity, evidence, fix, confidence. |
| `handoff_template.md` | Shape for session handoffs. Sections: goal, status, decisions, constraints, open questions, next step. |

### 5.5 · Pass 2 execution order

1. **Propose** the file list + per-file content in chat. The proposal is the Pass 1 audit report extended with concrete file content.
2. **Wait for approval** per S10. User approves all-or-some; writes only happen after explicit `apply` / `do it`.
3. **Write** the approved files. Order doesn't matter functionally; sensible order is `CLAUDE.md` first (so the version pointer is set early), then root files, then `skills/`, then `templates/`, then `archive/README.md`.
4. **Verify** with `git status` — only files inside `<project>/jai/` should appear; nothing outside.
5. **Report** per §9.

---

## 6 · Required Version Pointer

Every project's `jai/CLAUDE.md` declares conformance via YAML front-matter at the very top of the file (before the title), with these three fields:

```markdown
---
jai_standard: v1.0
project: <project-name>
last_synced: <YYYY-MM-DD>
---

# <project-name> — JAI entrypoint
```

Fields:
- **`jai_standard`** — the version of `~/.claude/jai-standard/JAI_VERSION` at the time the project was linked or last synced.
- **`project`** — short project identifier; lowercase-hyphenated by convention (`trend-scanner-v1`, not `Trend Scanner v1`).
- **`last_synced`** — ISO date the project was last linked or migrated. Updated on every Migration Pass.

The first-section block form (per [governance §3](./JAI_GOVERNANCE_STACK.md#project-version-declaration)) is a fallback if YAML front-matter conflicts with project tooling; prefer front-matter.

The version pointer is updated **only by an approved Migration Pass** (governance §5.5). Pass 2 sets the initial pointer; subsequent updates happen only when the project is migrated to a newer standard version.

---

## 7 · `PROJECT_EXCEPTIONS.md`

**Required even if empty.** This file documents intentional divergences between the project's `/jai/` profile and the JAI Standard version it claims to conform to. Per [governance §5.3](./JAI_GOVERNANCE_STACK.md#53--classify-drift), undocumented exceptions are flagged as drift in any future Drift Audit; documenting them here clears them.

### 7.1 · Empty form (most projects at Pass 2)

```markdown
# Project Exceptions — <project-name>

**JAI Standard conformance:** v1.0
**Last reviewed:** <YYYY-MM-DD>

This file documents any intentional divergences between this project's `/jai/`
profile and the JAI Standard version it claims to conform to.

## Active exceptions

| Rule | Why | Scope | Sunset |
|---|---|---|---|
| *(none)* | — | — | — |

## Retired exceptions

| Rule | Why | Scope | Retired | Reason |
|---|---|---|---|---|

*(none)*
```

### 7.2 · When to add a row

Whenever this project's `/jai/` deliberately diverges from the standard. Common cases:
- The project doesn't have a feature the standard assumes (e.g. no Docker → no `INFRASTRUCTURE.md` Docker section).
- The project follows a different convention than the standard (e.g. `Bash(scripts/dev/*)` instead of `Bash(npm run *)`).
- A standard rule fires false positives in this project's domain and is intentionally overridden.

Each row has: **Rule** (the specific standard rule + version), **Why** (concrete reason), **Scope** (which files/paths/workflows), **Sunset** (when to revisit — next bump, calendar date, or `indefinite`).

### 7.3 · When to retire a row

Move a row to "Retired exceptions" when:
- The exception is no longer needed (project converged on the standard).
- The standard changed to make the exception unnecessary.
- The exception's sunset date arrived.

Retired rows stay in the file for audit trail; they are never deleted.

---

## 8 · Required Read Order for Future Work

Once `/jai/` is in place, every Claude or dev session working in this repo follows the **two-tier read model**:

### 8.1 · Always read (every session, before any change)

1. `jai/CLAUDE.md` — entrypoint, declares JAI version, sets project scope.
2. `jai/CURRENT_STATE.md` — what's live, what's pending right now.
3. `jai/PROJECT_CONTEXT.md` — what this project is and does.
4. `jai/SECURITY_RULES.md` — project-specific security policy (extends global policy).
5. `jai/WORKFLOW_RULES.md` — project-specific workflow rules (branches, push policy, reviews).

### 8.2 · Read on demand (task-specific)

| File | Required when |
|---|---|
| `jai/THREAT_MODEL.md` | Security, infra, providers, auth, Docker, networking, deps, queues, DB, external integrations, deployment. |
| `jai/VULNERABILITY_REVIEW.md` | Dependency, Docker, provider, network, auth, logging, deployment, security-sensitive changes. |
| `jai/ARCHITECTURE.md` | Backend, frontend, API, DB, scheduler, provider, integration changes. |
| `jai/INFRASTRUCTURE.md` | Docker, ports, volumes, networks, deployment changes. |
| `jai/DECISION_LOG.md` | Any time the question "why was this decided?" comes up. |
| `jai/CLEANUP_REGISTER.md` | When picking up deferred cleanup. |
| `jai/HANDOFF.md` | Continuation from a prior session. |
| `jai/PROJECT_EXCEPTIONS.md` | When following a global rule appears to conflict with project reality — check here first. |
| `jai/skills/<name>.skill.md` | When the current task matches the skill's domain. |
| `jai/templates/<name>.md` | When producing a deliverable that has a templated shape. |

The two-tier model is **part of the standard**; projects may not omit it. A project that wants different always-reads must document the change as an exception in `PROJECT_EXCEPTIONS.md` *and* file an Enhancement Intake item against the global standard.

---

## 9 · Final Report Format

After Pass 2 completes, return this report in chat. Same shape as the Pass 1 audit report, but reflecting writes performed.

```markdown
# JAI Pass 2 report — <project-name>

**Date:** YYYY-MM-DD
**Project path:** <repo root>
**Standard version applied:** v1.0
**Operator:** Claude (or human)

## Files inspected
<list — same as Pass 1 §4.1>

## Files created
<list — every new file under <project>/jai/, by relative path>

## Files changed
<list — typically empty for fresh Pass 2; non-empty if Pass 2 also touched
existing root-level docs, which it normally does not>

## Restricted areas untouched
- `.env` / `.env.*`: not read, not written.
- Docker: no commands run, no compose/Dockerfile edits.
- Migrations: not run, not edited.
- Providers: no outbound calls.
- Scheduler: not read, not written.
- App code: no source files modified.
- Claude settings: no `.claude/` edits.
- Hooks: not installed, not edited.

## Tests / checks run
<list — typically just `git status` and a directory listing of /jai/;
expand if the project has a relevant lint/format pass on markdown>

## git status
<output of `git status --short` — should show only new files under jai/>

## Commit hash if committed
<sha or "not committed">

## Push status if pushed
<remote/branch or "not pushed">

## Risks / follow-ups
<list — typically: any §4 audit findings that warrant a separate task,
plus any global-standard items the project will inherit when those
catalogs/policies seed (e.g. SECRET_REGEX_CATALOG.md)>

## Next recommended step
<one sentence>
```

---

## 10 · Commit / Push Rules

Carry-over from existing standing rules. Restated for clarity.

| # | Rule |
|---|---|
| **C1** | **No commit unless approved.** Pass 2 writes files into the working tree; staging and committing is a separate explicit step. |
| **C2** | **No push unless approved.** Pushing makes work visible beyond the local machine and may trigger CI; explicit per-push approval. |
| **C3** | **No `git init` unless approved.** If the target repo is not a git repo, surface the fact and ask before initializing. Most projects with a `/jai/` are already git-tracked. |
| **C4** | **No force push unless explicitly approved.** No `--force`, no `--force-with-lease`, no `+refs/heads/...:` refspec. To main/master: never force-push, even with explicit approval — surface and discuss. |
| **C5** | **No bypassing hooks.** No `--no-verify`, no `--no-gpg-sign`. If a hook fails, fix the underlying issue. |
| **C6** | **Default to a feature branch for Pass 2.** A fresh `/jai/` is a meaningful change; prefer a branch like `chore/jai-init` over committing directly to main. The branch + PR + review path is the same as any other change. |

---

## Appendix A — Quick checklist (one-pager)

For experienced operators applying this plan to a new repo:

```
[ ] §2 pre-read complete
[ ] Pass 1 — audit report returned in chat
    [ ] §4.1 docs inventory
    [ ] §4.2 docs classification
    [ ] §4.3 infra inventory
    [ ] §4.4 port binding findings
    [ ] §4.5 secret-handling findings
    [ ] §4.6 script risk findings
    [ ] §4.7 dependency findings
    [ ] §4.8 source-of-truth candidates
    [ ] §4.9 proposed /jai/ structure
    [ ] §4.10 restricted-areas confirmation
    [ ] §4.11 git status (clean)
    [ ] §4.12 next step
[ ] User approves Pass 2
[ ] Pass 2 — files written
    [ ] §5.1 13 root files + archive/ + skills/ (10) + templates/ (4) = 28 files
    [ ] §6 version pointer in jai/CLAUDE.md
    [ ] §7 PROJECT_EXCEPTIONS.md created
    [ ] §8 two-tier read model in jai/CLAUDE.md
[ ] §9 final report returned in chat
[ ] §10 commit/push only after approval
```

---

## Appendix B — Provenance

**v1.0 derives from:**
- The JAI Project Security Context Infrastructure handoff that produced Trend Scanner v1's `/jai/` (live since 2026-05-08, commit `2f57e65`). That handoff is the empirical baseline.
- [`JAI_GOVERNANCE_STACK.md`](./JAI_GOVERNANCE_STACK.md) §0.5, §1, §2 — relationship to global standard, definitions, hierarchy.
- [`JAI_GOVERNANCE_STACK.md`](./JAI_GOVERNANCE_STACK.md) §6 — safety rules, restated in §3 above.
- [`JAI_GOVERNANCE_STACK.md`](./JAI_GOVERNANCE_STACK.md) §7 — sync report format, adapted in §9 above.
- The version pointer convention introduced by Trend Scanner v1's first Migration Pass (2026-05-08).

**v1.0 does not derive from:**
- Any external repo. The lasso/dotclaude vetting (`references/repo-vetting-lasso-vs-dotclaude-2026-05-08.md`) was completed before this plan landed; concepts from it remain queued as v1.1+ intake candidates.
