# JAI_GOVERNANCE_STACK.md

**Status:** Approved working spec · 2026-05-08 (draft v3 locked)
**Owner:** Mitch
**Scope:** Cross-project meta-layer for managing the JAI standard. Sits **above** the existing `/jai/` implementation plan; does not replace it.

---

## 0 · One-paragraph summary

JAI is a per-project Claude-context standard. The **implementation plan** (already live in Trend Scanner v1) defines what goes inside one project's `/jai/` folder — the required files, the two-tier read model, the skills, the templates. The **governance stack** (this document) sits above it and defines how the JAI standard is versioned across projects, how enhancements are intaked, how external repos are vetted, how project profiles are checked for drift, and how upgrades are rolled out safely. Implementation = builder. Governance = manager. Both are needed; neither replaces the other.

---

## 0.5 · Relationship to the existing implementation plan

| Layer | Artifact | Authority |
|---|---|---|
| **Project-level (builder)** | The existing `/jai/` implementation plan, live in Trend Scanner v1 since 2026-05-08. CLAUDE.md entrypoint, two-tier read model (5 always-read core docs + task-conditional reads), 10 skills, 4 templates, 27 files. | **Authoritative for what goes inside one project's `/jai/`.** Defines file shapes, section requirements, the read protocol, skill structure, template structure. The governance stack does not redefine any of this. |
| **Cross-project (manager)** | This document + the global standard at `~/.claude/jai-standard/`. | **Authoritative for how the project-level structure is versioned, propagated, audited, and upgraded.** Treats the implementation plan as the empirical baseline for JAI Standard v1.0. |

**Concretely:**
- The implementation plan determines that a project needs `CLAUDE.md`, `SECURITY_RULES.md`, `THREAT_MODEL.md`, `WORKFLOW_RULES.md`, etc. — that list **stays as-is** and becomes the v1.0 required-files list in the global spec.
- The governance stack determines that a project must declare which version of that list it conforms to, how exceptions are documented, how additions to the list become v1.1, and how a project gets safely migrated when the list changes.
- When governance and implementation appear to disagree, the implementation plan wins for project-level structural questions; governance escalates the disagreement through the Enhancement Intake workflow (§4).

**Files referenced from the project-level plan, kept verbatim:** `jai/CLAUDE.md`, `jai/PROJECT_CONTEXT.md`, `jai/ARCHITECTURE.md`, `jai/INFRASTRUCTURE.md`, `jai/SECURITY_RULES.md`, `jai/THREAT_MODEL.md`, `jai/WORKFLOW_RULES.md`, `jai/VULNERABILITY_REVIEW.md`, `jai/CURRENT_STATE.md`, `jai/DECISION_LOG.md`, `jai/HANDOFF.md`, `jai/CLEANUP_REGISTER.md`, `jai/skills/*.skill.md`, `jai/templates/*.md`. Governance adds **only one** new project-level file: `jai/PROJECT_EXCEPTIONS.md`.

---

## 1 · Definitions

| Term | Definition |
|---|---|
| **Project-level implementation plan** | The pre-existing guide for building `/jai/` inside one project. Stays authoritative. |
| **Global JAI Standard** | The cross-project specification: which version of the implementation plan is current, the cross-cutting policies that apply to every project, the universal catalogs and skill specs, the template library, and the version history. **Canonical location: `~/.claude/jai-standard/`**, starting as a local folder. Promotion to a dedicated git repo is deferred until ≥2 machines or ≥3 projects need it. |
| **Project `/jai/` Profile** | The on-disk implementation inside a specific project (e.g. `Trend Scanner v1/jai/`). Built per the implementation plan. Declares which JAI Standard version it conforms to in `jai/CLAUDE.md` front-matter or first section. |
| **Enhancement Intake** | The process of evaluating a candidate change for inclusion in the global standard. Inputs: external repos, project incidents, new capabilities, ad-hoc ideas. Output: a versioned addition, or a documented rejection. |
| **External Repo Vetting** | Read-only audit of a third-party repo to determine whether its concepts (not files) are worth lifting into the global standard. Six-section format: purpose, structure, security, compatibility, risk, recommendation, extractable value. Outcomes: Adopt / Adapt / Reference only / Reject / Defer. Reports live globally at `~/.claude/jai-standard/references/`. |
| **Drift Audit** | Comparison of a project's `/jai/` profile against the JAI Standard version it claims to conform to. Identifies missing files, missing rules, stale content, and undocumented exceptions. |
| **Migration Pass** | A scoped, approved set of edits that brings a project's `/jai/` from one standard version to a newer one. Always docs-only, always opt-in, always reviewed in chat first. |

---

## 2 · Hierarchy

Three layers. Lower may reference upper; upper never reaches into lower.

### Layer A — Global standard at `~/.claude/jai-standard/`

```
~/.claude/jai-standard/
├── JAI_VERSION                  # single line, e.g. "v1.0"
├── JAI_STANDARD.md              # spec: required files (cites implementation plan), required sections
├── JAI_CHANGELOG.md             # one entry per version bump
├── JAI_GOVERNANCE_STACK.md      # this document
├── policies/                    # cross-cutting rules every project inherits
│   ├── SAFETY_RULES.md          # the §6 absolutes from this doc
│   ├── SECURITY_RULES.md        # universal portion (project files extend it)
│   ├── THREAT_MODEL.md          # universal threat catalog
│   └── WORKFLOW_RULES.md        # universal portion
├── catalogs/                    # reusable detector libraries
│   ├── SECRET_REGEX_CATALOG.md
│   └── INJECTION_CATEGORIES.md
├── skills/                      # canonical skill specs
│   └── *.skill.md
├── templates/                   # canonical templates
│   └── *.md
├── intake/                      # in-flight enhancement proposals
│   └── YYYY-MM-DD-<short-name>.md
└── references/                  # external vetting reports + vendor links
    └── repo-vetting-*.md
```

Belongs globally:
- The **schema pointer** (cites the implementation plan; doesn't re-derive it).
- Cross-cutting policies that apply to every project (v1/v2 isolation, no env changes, docs-only default, no browser, codex review workflow, default Bash, timezone-verify-before-scheduling, etc.).
- Universal threat catalog and detector libraries.
- Canonical skill specs and templates.
- Versioning rules and this governance doc.

Does **not** belong globally:
- Project state (CURRENT_STATE, DECISION_LOG, HANDOFF) — per-project by definition.
- Project architecture, service inventories, domain knowledge.
- Secrets, infrastructure topology, deployment specifics.

### Layer B — Project `/jai/` profile

Built per the **existing implementation plan**. Layout from that plan stays as-is. Governance adds exactly one file: `PROJECT_EXCEPTIONS.md`.

```
<project>/jai/
├── CLAUDE.md                    # entrypoint; declares JAI version (front-matter or §1)
├── PROJECT_CONTEXT.md
├── ARCHITECTURE.md
├── INFRASTRUCTURE.md
├── SECURITY_RULES.md            # project-specific extension of policies/SECURITY_RULES.md
├── THREAT_MODEL.md
├── WORKFLOW_RULES.md
├── VULNERABILITY_REVIEW.md
├── PROJECT_EXCEPTIONS.md        # NEW (governance addition); documents divergence from declared version
├── CURRENT_STATE.md
├── DECISION_LOG.md
├── HANDOFF.md
├── CLEANUP_REGISTER.md
├── skills/                      # project-instantiated skills (per implementation plan)
└── templates/                   # rare project-level template overrides
```

Belongs in each project:
- Everything the implementation plan already specifies, populated with project-specific content.
- A version pointer in `jai/CLAUDE.md` (front-matter or first section).
- `jai/PROJECT_EXCEPTIONS.md` listing intentional divergences from the declared version.

Does **not** belong in a project:
- Copies of the global spec — only the version pointer.
- Cross-cutting policies — those are inherited from `~/.claude/jai-standard/policies/`.
- Vetting reports.

### Layer C — External reference (read-only)

At `~/.claude/jai-standard/references/`:
- Third-party repos cited by URL + commit SHA, never copied wholesale.
- Vendor docs — link + accessed-on date.
- Vetting reports. Their conclusions either get lifted into the standard (with attribution + version bump) or recorded as rejection in `JAI_CHANGELOG.md`.

---

## 3 · Versioning model

### Version numbers

`JAI Standard vMAJOR.MINOR`

- **MAJOR** — **breaking structural change only.** Required-file removed, renamed without alias, schema reshape that any conforming project must follow. A policy reversal that contradicts existing project content is also MAJOR. Rare.
- **MINOR** — **additive.** New optional file, new skill, new template, new policy that defaults safe. New required file is also MINOR if it ships with a skip-if-missing fallback for projects still on the prior version. Wording fixes and clarifications fold into the next MINOR.

No PATCH level.

### Project version declaration

Project's `jai/CLAUDE.md` declares conformance via YAML front-matter (preferred) or first-section block.

**Front-matter form (preferred):**
```markdown
---
jai_standard: v1.0
project: trend-scanner-v1
last_synced: 2026-05-08
---

# Trend Scanner v1 — JAI entrypoint
```

**First-section form (fallback):**
```markdown
# Trend Scanner v1 — JAI entrypoint

> JAI Standard: v1.0
> Last synced: 2026-05-08
```

The version pointer is updated **only** by an approved Migration Pass.

### Recording rule

A change to the global standard is **only real** when:
1. The new/edited file is committed to `~/.claude/jai-standard/`.
2. `JAI_CHANGELOG.md` has a new entry citing the source.
3. `JAI_VERSION` is bumped.

Until all three exist, the change is a draft.

### Relationship to the implementation plan

If a proposed change would alter the **shape** of `/jai/` itself (e.g. add a new required file, rename an existing one), it triggers a coordinated update: governance bumps the JAI Standard version **and** the implementation plan is amended. The implementation plan remains the authoritative source for the new shape; the standard cites it.

---

## 4 · Enhancement workflow

Six steps. Each has a clear gate. Nothing edits the standard until step 6.

### 4.1 · Identify

Triggers: external repo audit; project incident; new Claude Code/Anthropic capability; ad-hoc idea.

Output: one-paragraph problem statement at `~/.claude/jai-standard/intake/<YYYY-MM-DD>-<short-name>.md`.

### 4.2 · Vet

External repo → External Repo Vetting format. Internal source → shorter "internal vetting note" with same shape: purpose, security, compatibility with current standard, risk, recommendation.

Gate: explicit recommendation of **Adopt** / **Adapt** / **Reference only** / **Reject** / **Defer**.

### 4.3 · Classify

| If the change is… | It belongs as… |
|---|---|
| A universal rule | Edit to a `policies/` file. MINOR. |
| A reusable detector or checklist | New entry under `catalogs/`. MINOR. |
| A reusable workflow | New skill in `skills/`. MINOR. |
| A reusable document shape | New template in `templates/`. MINOR. |
| A change to the project-level `/jai/` shape (new required file, rename) | Coordinated update to standard **and** implementation plan. MINOR (with skip-if-missing) or MAJOR (without). |
| Project-specific only | Not in the standard. Goes into the project's `/jai/`. |
| Ambient / read-only context | Not in the standard. Lives in `references/`. |

Gate: classification written down before adapting starts.

### 4.4 · Adapt

Draft diff against `~/.claude/jai-standard/`:
- New or modified file content.
- Proposed `JAI_CHANGELOG.md` entry.
- Proposed `JAI_VERSION` bump.
- Proposed implementation-plan amendment (only if shape changes).
- Proposed migration notes (usually "none" for additive MINOR).

Gate: review by Mitch in chat. No files written until approval.

### 4.5 · Pilot

Apply to **Trend Scanner v1** first via Migration Pass (§5). Observation window: one week, or until next intake cycle. Capture: did the rule fire usefully? false positives? exceptions worth documenting globally?

Gate: pilot retrospective added to the intake item.

### 4.6 · Roll out

Pilot clean → commit to `~/.claude/jai-standard/`, bump `JAI_VERSION`, append `JAI_CHANGELOG.md`, schedule Migration Passes for remaining projects (Trend Scanner v2 next when it has a `/jai/`).

Pilot dirty → iterate (back to 4.4) or reclassify as Reject/Defer with a recorded reason.

---

## 5 · Existing project sync workflow

Five steps. Read-only until step 5. Always returns a report in chat first.

**Pilot project: Trend Scanner v1.** Other projects sync only after the pilot has cleared an observation window on the target version.

### 5.1 · Audit project `/jai/`

Inventory: present files; missing files (vs. the implementation plan + standard's required list); declared version; skills + templates present; modification dates.

### 5.2 · Compare against latest standard

For each required artifact at the **target version**:
- Present in the project?
- Content current (cites rules from claimed version or earlier)?
- Sections newer versions added that this project lacks?

### 5.3 · Classify drift

| Class | Meaning |
|---|---|
| **Missing** | Standard requires this; project doesn't have it. |
| **Stale** | Present but corresponds to older version. |
| **Project-specific exception** | Intentional divergence; should be in `PROJECT_EXCEPTIONS.md`. |
| **Project-specific addition** | Not required, not forbidden. Keep, note. |
| **Conflict** | Project content contradicts the standard. Highest priority. |

### 5.4 · Propose patch

Chat-returnable report (format in §7). Each finding gets one of:
- **Add** — create missing file/section.
- **Update** — replace stale content; preserve project-specific portions.
- **Document** — move undocumented divergence to `PROJECT_EXCEPTIONS.md`.
- **Resolve** — for conflicts: update project, or escalate to Enhancement Intake if the project is right and the standard is wrong.
- **Leave** — no action.

### 5.5 · Update only with approval

Mitch approves in chat, file by file or as a bundle. Only then are files written. After write:
- Bump `jai/CLAUDE.md` version pointer + `last_synced`.
- Append one-line entry to `jai/DECISION_LOG.md`: "JAI sync: vX.Y → vX.Z, files touched: …".
- No git commit unless explicitly requested.

---

## 6 · Safety rules

**Canonical text:** [`policies/SAFETY_RULES.md`](./policies/SAFETY_RULES.md).

The S1–S8 rules are absolute and hold across enhancement intake, external vetting, drift audit, and migration passes. They were lifted to `policies/SAFETY_RULES.md` at JAI v1.5 (2026-05-10) so that downstream specs reference a single source of truth. The §6 anchor remains stable — cross-references to "S1–S8 from `JAI_GOVERNANCE_STACK.md §6`" still resolve correctly because §6 redirects to the canonical file. The lift was a docs reorganization with no behavioral change.

---

## 7 · Output format (project sync report)

Returned in chat after a Drift Audit (§5). Approval needed before any file is written.

```markdown
# JAI sync report — <project name>

**Date:** YYYY-MM-DD
**Project path:** <repo root>
**Current JAI version:** v1.0   (declared in <project>/jai/CLAUDE.md front-matter)
**Target JAI version:** v1.2
**Auditor:** Claude (read-only)

---

## Summary
- N missing global rules
- M stale files
- K project-specific exceptions (P documented, Q undocumented)
- C conflicts requiring resolution

## Missing global rules
| Rule / file | Source (standard §) | Suggested action |
|---|---|---|

## Stale content
| File | Last touched | Standard version of content | Suggested action |
|---|---|---|---|

## Project-specific exceptions
| Exception | Documented? | Suggested action |
|---|---|---|

## Project-specific additions (keep)
| Addition | Notes |
|---|---|

## Conflicts
| Conflict | Project says | Standard says | Suggested resolution |
|---|---|---|---|

## Proposed file changes
1. **CREATE** | **UPDATE** | **APPEND** <path> — <one-line reason>

## Approval needed
Reply with one of:
- `apply all` — write every change.
- `apply 1,2,4` — only those.
- `defer` — keep report, no writes.
- `reject <#>` — exclude items, apply the rest.

No file is written until you respond.
```

---

## 8 · Locked-in decisions

Resolved 2026-05-08:

| # | Question | Decision |
|---|---|---|
| 1 | Canonical location for the global standard | **`~/.claude/jai-standard/`**, starting as a local folder. Promotion to a dedicated git repo deferred until ≥2 machines or ≥3 projects need it. |
| 2 | Project version pointer location | **`jai/CLAUDE.md` front-matter or first section** (§3 shows both forms). |
| 3 | `PROJECT_EXCEPTIONS` placement | **Separate file: `jai/PROJECT_EXCEPTIONS.md`.** |
| 4 | Pilot project | **Trend Scanner v1.** Trend Scanner v2 (and future projects) inherit later via Migration Pass once pilot clears observation window. |
| 5 | Vetting report retention | **Global, not per-project.** Live at `~/.claude/jai-standard/references/`. |
| 6 | MAJOR-bump trigger discipline | **Only for breaking structural changes.** All additive changes — including new required files with skip-if-missing fallback — are MINOR. |
| 7 | Relationship to existing implementation plan | **Implementation plan stays authoritative for project-level `/jai/` shape.** Governance stack sits above it. The two never re-derive each other. |

---

## 9 · Bootstrap plan (executed 2026-05-08, steps 1–6)

v1.0 bootstrap was a single docs-only pass:

1. ✅ Created `~/.claude/jai-standard/` with the §2 Layer A layout.
2. ✅ Seeded `JAI_VERSION` with `v1.0`.
3. ✅ Wrote this document to `~/.claude/jai-standard/JAI_GOVERNANCE_STACK.md`.
4. ✅ Wrote `JAI_STANDARD.md` v1.0 — a pointer to the project-level implementation plan as committed in Trend Scanner v1 at `2f57e65`, plus the cross-cutting policy/catalog/skill/template scaffolding.
5. ✅ Seeded `JAI_CHANGELOG.md` v1.0 entry.
6. ✅ Carried in `references/repo-vetting-lasso-vs-dotclaude-2026-05-08.md`.

Step 7 (Trend Scanner v1's first Migration Pass — adding `jai_standard: v1.0` front-matter and an empty `jai/PROJECT_EXCEPTIONS.md`) is **not** part of this bootstrap; it requires a separate explicit go-ahead.
