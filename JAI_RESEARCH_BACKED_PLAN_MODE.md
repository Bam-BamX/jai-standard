# JAI Research-Backed Plan Mode

**Standard version:** Introduced in JAI v1.4 (2026-05-10)
**Companion command:** `JAI research-backed plan` (see [JAI_COMMANDS.md](./JAI_COMMANDS.md) §14)
**Companion runtime reviewer:** [JAI_RUNTIME_ADVISORY_REVIEWER.md](./JAI_RUNTIME_ADVISORY_REVIEWER.md) — used to review the plan this mode produces.
**Output format:** Plans are emitted per [JAI_DEV_RESPONSE_FORMAT.md](./JAI_DEV_RESPONSE_FORMAT.md).

---

## 0. Purpose

Defines a structured, read-only workflow that turns a vague project idea into an
implementation-ready plan. The mode runs six phases — clarify, domain research,
reference research, capability extraction, architecture, plan — and **stops at
an explicit approval gate** before any execution begins.

This file is the spec. The trigger phrase, required reads, allowed/forbidden
actions, and approval gate are registered in [JAI_COMMANDS.md](./JAI_COMMANDS.md) §14.

**Scope: global only.** This file is not duplicated inside any project's `/jai/`.
Conforming projects reference it from the global standard.

---

## 1. Relationship to other JAI files

| File | Relationship |
|---|---|
| `JAI_GOVERNANCE_STACK.md §6` | Inherits S1–S8 safety rules unchanged. |
| `JAI_COMMANDS.md §2` | Inherits universal command safety rules. |
| `JAI_RUNTIME_ADVISORY_REVIEWER.md` | The mode's output is reviewable under the runtime advisory reviewer; reviewer is the next step after the approval gate. |
| `JAI_DEV_RESPONSE_FORMAT.md` | Phase-6 plan is emitted in the canonical dev response shape; ST-10 mode may be invoked for the Plain-English Summary section. |
| Project `/jai/CLAUDE.md` Hard NOT-touch list | Read-only input; never proposed for modification. |

---

## 2. When to use

Use this mode when:

- The operator has an idea but the requirements are vague.
- A feature spans multiple repos, services, or domains and needs scoping.
- A third-party concept may be adapted into the project (paired with `JAI external repo vetting`).
- The operator wants to know what's possible before deciding to build.

Do **not** use this mode when:

- The work is a small, well-defined diff (use the project's matching skill).
- A drift audit, fleet sync, or migration pass is the right workflow.
- The change is purely operational (commits, branch ops) — those run through `JAI runtime advisory review` after the fact.

---

## 3. Six phases

Each phase has a fixed input → output and is reviewable in chat before the next
phase starts. The operator may say `proceed` to advance, `redirect <note>` to
adjust, or `stop` to end the mode at any phase.

### Phase 1 — Clarify

Input: the operator's idea in plain language.

Output: a one-paragraph problem statement plus an explicit list of:
- Stated goal (what the operator said).
- Inferred goal (what the work seems to be aiming at).
- Open questions (≤5; only blockers, not nice-to-haves).
- Out-of-scope items (what this work explicitly does not cover).

Gate: operator confirms the problem statement before Phase 2 starts.

### Phase 2 — Domain research

Input: the confirmed problem statement.

Output: a short domain map covering:
- Key concepts and their relationships.
- Existing solutions in the broader ecosystem (named, not endorsed).
- Constraints relevant to the domain (regulatory, technical, performance).
- Failure modes typical of this domain.

Sources: WebFetch on public docs and reference material is allowed (S5 carve-out
for public docs/repos). External repos cited by URL + access date only — no
clones, no copies.

Cap: ≤5 external sources per pass. If more are needed, the operator approves
the expansion explicitly.

### Phase 3 — Reference research

Input: the domain map.

Output: a comparison table of candidate reference repos / libraries:

| Source | URL + commit/tag | Purpose | Adopt / Adapt / Reference / Reject | Why |

Sources are read read-only via WebFetch. No installation, no execution, no
copying of files. License visible in the table.

This phase produces the input for any future `JAI external repo vetting`
command — Plan Mode does not perform full vetting itself, only the shortlist.

### Phase 4 — Capability extraction

Input: the reference shortlist.

Output: a list of capabilities the new work needs, each tagged with:
- Required vs. nice-to-have.
- Source of inspiration (reference shortlist row, or "novel").
- Closest existing project capability (so duplication is visible).
- Risk signal (security, performance, complexity, dependency weight).

### Phase 5 — Architecture

Input: capability list + the project's `ARCHITECTURE.md`, `INFRASTRUCTURE.md`,
`SECURITY_RULES.md`, and `THREAT_MODEL.md` (read-only).

Output: an architecture sketch covering:
- Component diagram (text-based; no external diagram tools).
- Data flow (input → processing → output, with trust boundaries marked).
- Integration points with existing project code (file paths cited; no edits).
- Restricted-area honor check: confirms nothing in the project's
  `Hard NOT-touch list` is required to change. If it is, the mode reports the
  conflict and stops at this phase.

### Phase 6 — Implementation-ready plan

Input: the approved architecture sketch.

Output: a step-by-step plan emitted per `JAI_DEV_RESPONSE_FORMAT.md`, covering:
- Sequenced batches (each batch = one approval gate later).
- Files that would change per batch (paths, not diffs).
- Skills the project would invoke per batch
  (e.g. `frontend_change`, `backend_change`).
- Validation per batch (typecheck, build, tests, HTTP probes — whatever the
  project requires).
- Risks and rollback notes.
- Estimated approval gates (count, not duration).

---

## 4. Approval gate (the hard stop)

After Phase 6 returns the plan, the mode **stops**. No code is written. No
branches are created. No commits are made. No installs are performed.

To advance to execution, the operator invokes the appropriate project-level
skill (e.g. `frontend_change`, `backend_change`) and references the plan. Each
batch in the plan needs its **own** explicit "go" — plan approval is **not**
execution approval. This restates the active project's `/jai/WORKFLOW_RULES.md`
per-batch cadence rule verbatim and makes it the cross-project default for
this mode.

---

## 5. Forbidden actions

Inherits all S1–S8 from `JAI_GOVERNANCE_STACK.md §6`. In addition, this mode
specifically forbids:

- Writing any file (including the plan output — plans are returned in chat).
- Cloning external repos into the project.
- Running fetched code.
- Modifying `.claude` settings or hooks.
- Calling provider APIs.
- Bumping `JAI_VERSION` or editing the global standard.
- Authorising any execution batch directly — execution requires a separate skill invocation and a separate operator "go".

---

## 6. Restricted-area discipline

Plan Mode reads the project's `Hard NOT-touch list` (in `jai/CLAUDE.md`) and the
project's `SECURITY_RULES.md`. If Phase 5 surfaces a need to modify anything on
either list, the mode:

1. Reports the conflict explicitly.
2. Stops at Phase 5 (does not produce a Phase 6 plan).
3. Recommends one of: scope down, document a `PROJECT_EXCEPTIONS.md` row,
   escalate to `JAI enhancement intake`.

The mode never silently routes around a restricted area.

---

## 7. Output format

Phase outputs are returned in chat in the order produced. Phase 6 follows the
ten-section structure of `JAI_DEV_RESPONSE_FORMAT.md`. ST-10 mode may be
invoked for the Plain-English Summary section per the operator's request.

A reviewer running `JAI runtime advisory review` against a Phase 6 plan
applies its standard checklist directly (`JAI_RUNTIME_ADVISORY_REVIEWER.md`
§11) without translation.

---

## 8. Maintenance rule

Changes to this file are MINOR per `JAI_GOVERNANCE_STACK.md §3` if additive
(new phase output fields, new safety carve-outs that default safe) and MAJOR
if a phase is removed, the approval gate is weakened, or a forbidden action is
moved to allowed.
