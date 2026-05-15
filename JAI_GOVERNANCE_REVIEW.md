# JAI Governance Review

**Standard version:** Introduced in JAI v1.4 (2026-05-10)
**Companion command:** `JAI governance review` (see [JAI_COMMANDS.md](./JAI_COMMANDS.md) §15)
**Sits above:** [JAI_GOVERNANCE_STACK.md](./JAI_GOVERNANCE_STACK.md) — this file reviews the standard the governance stack defines, but never edits it.

---

## 0. Purpose

Defines a read-only workflow that audits the **global JAI standard itself**
against best practices, identifies structural gaps, and proposes additions.
All findings route into the existing Enhancement Intake gate
(`JAI_GOVERNANCE_STACK.md §4`); this command never edits global files
directly.

This is the meta-layer: where `JAI runtime advisory review` reviews dev work
and `JAI drift audit` reviews a project's `/jai/`, **this command reviews the
global standard**.

**Scope: global only.** Not duplicated in any project `/jai/`.

---

## 1. Relationship to other JAI files

| File | Relationship |
|---|---|
| `JAI_GOVERNANCE_STACK.md §4` | All findings exit through Enhancement Intake — no shortcuts. |
| `JAI_GOVERNANCE_STACK.md §6` | S1–S8 safety rules apply unchanged. |
| `JAI_STANDARD.md` | Read-only input. Never edited by this command. |
| `JAI_CHANGELOG.md` | Read-only input. Never appended by this command. |
| `JAI_VERSION` | Read-only input. Never bumped by this command. |
| `JAI_COMMANDS.md` | Read-only input; gap analysis may recommend new commands but does not register them. |
| `references/*.md` | Read-only input. Vetting reports inform best-practice crosswalk. |

---

## 2. When to use

Use this command when:

- A new external repo or article surfaces a pattern worth crosswalking against the standard.
- A project ships a workflow that proved valuable and might be globally lifted.
- A pilot retrospective from §4.5 surfaces a recurring pattern.
- The standard hasn't been audited in a while (suggested cadence: every 2–3 minor versions).

Do **not** use this command to:

- Edit the global standard (use `JAI standard update` after Intake approval).
- Audit a project (use `JAI drift audit`).
- Audit many projects (use `JAI fleet sync audit`).

---

## 3. Audit phases

### Phase 1 — Inventory

Read every file under `~/.claude/jai-standard/`. List:
- Top-level `*.md` files and their stated purposes.
- `policies/`, `catalogs/`, `skills/`, `templates/` contents (whether populated or placeholder-only).
- `intake/` contents (open / closed / deferred).
- `references/` contents (existing vetting reports).
- `JAI_VERSION` declared.

### Phase 2 — Structural audit

Check for:
- Files referenced by other files but missing.
- Files present but not referenced anywhere.
- Numbered sections that disagree across files (e.g. §6 in stack vs §2 in commands).
- Lazy-migration directories that have stayed empty across multiple version bumps (a recurring symptom — `skills/`, `templates/`, `policies/`, `catalogs/` were placeholder-only across v1.0–v1.3).
- Locked decisions in `JAI_GOVERNANCE_STACK.md §8` whose preconditions have changed (e.g. ≥2 machines now, or ≥3 projects now).

### Phase 3 — Safety audit

Verify:
- S1–S8 in `JAI_GOVERNANCE_STACK.md §6` are present and unweakened.
- Universal command safety rules in `JAI_COMMANDS.md §2` are consistent with §6.
- Every command in `JAI_COMMANDS.md` declares a Forbidden actions block.
- No command grants implicit authorization for `.env`, Docker, migrations, provider calls, hooks, settings, branch deletion, force pushes, or destructive cleanup.

If any safety rule is missing or weakened, the finding is flagged **`SAFETY-CRITICAL`** and the operator is alerted before the rest of the report is rendered.

### Phase 4 — Command coverage audit

For each command in `JAI_COMMANDS.md`:
- Does its `Required reads` list cite every file it depends on?
- Does its `Required output` field list match the actual spec it implements?
- Is it represented in the §12 summary table?
- Does its approval gate match the safety rules of the spec it triggers?

Identify capability gaps — workflows the operator has invoked ad-hoc that don't have a registered command.

### Phase 5 — Best-practice crosswalk

Compare the standard against:
- Existing `references/*.md` vetting reports (already in scope).
- Public best-practice sources via WebFetch (allowed under S5 read-only carve-out).
- Patterns documented in `JAI_CHANGELOG.md` migration notes that haven't been formalised.

External sources are cited by URL + access date only. No content is copied. The crosswalk produces categories, not verdicts:

| Category | Meaning |
|---|---|
| **Confirms** | The standard already does this. |
| **Extends** | The standard partially does this; could be deepened. |
| **Missing** | The standard does not address this. |
| **Diverges** | The standard intentionally does the opposite. |

### Phase 6 — Gap classification

Each gap from Phases 2–5 gets routed per `JAI_GOVERNANCE_STACK.md §4.3`:

| Type | Where it routes |
|---|---|
| Universal rule | Edit to `policies/` (MINOR). |
| Reusable detector / checklist | New entry under `catalogs/` (MINOR). |
| Reusable workflow | New skill in `skills/` or top-level `JAI_*.md` behavior file (MINOR). |
| Reusable doc shape | New template (MINOR). |
| Project-level shape change | Coordinated update to standard + implementation plan (MINOR or MAJOR). |
| Project-specific only | Out of scope for this command — recommend the operator open it as a project-level discussion. |
| Ambient context | New `references/*.md` (no version bump). |

Each gap also gets a recommendation: **Adopt / Adapt / Reference only / Reject / Defer**, matching the External Repo Vetting outcomes.

---

## 4. Output routing — the no-self-modification guardrail

The output of this command is **always**:

1. A chat-rendered report (Phases 1–6 above).
2. One or more proposed `intake/YYYY-MM-DD-<short-name>.md` items, returned in chat as drafts.

The operator decides which proposals to write into `intake/`. Writes happen
through `JAI enhancement intake` (`JAI_COMMANDS.md §8`), not this command.
Standard updates happen through `JAI standard update` (§9), not this command.

This means:

- This command **cannot** write to `~/.claude/jai-standard/` directly.
- This command **cannot** bump `JAI_VERSION`.
- This command **cannot** append `JAI_CHANGELOG.md`.
- This command **cannot** edit S1–S8 safety rules.
- This command **cannot** alter locked decisions in `JAI_GOVERNANCE_STACK.md §8`
  — only flag that a precondition may have changed and recommend the operator
  re-open the decision via Enhancement Intake.

A safety rule weakening (S1–S8) is **MAJOR** per `§3 versioning` and surfaces
in the report as `SAFETY-CRITICAL — operator decision required`.

---

## 5. Forbidden actions

Inherits all S1–S8 from `JAI_GOVERNANCE_STACK.md §6`. In addition:

- Do not edit any file under `~/.claude/jai-standard/`.
- Do not edit any project `/jai/`.
- Do not bump `JAI_VERSION`.
- Do not append `JAI_CHANGELOG.md`.
- Do not write `intake/*.md` files — only return drafts in chat.
- Do not register new commands in `JAI_COMMANDS.md`.
- Do not run installs, Docker, migrations, or provider calls.
- Do not invoke `JAI standard update` automatically based on findings.

---

## 6. Output format

Returned in chat. One report per audit pass.

```markdown
# JAI Governance Review — YYYY-MM-DD

**Global JAI version reviewed:** vX.Y
**Auditor:** Claude (read-only)

## Summary
- N safety-critical findings
- M structural gaps
- K command-coverage gaps
- C best-practice crosswalk findings (Confirms / Extends / Missing / Diverges)

## Phase 1 — Inventory
<file list>

## Phase 2 — Structural findings
| Finding | Severity | Recommendation |

## Phase 3 — Safety findings
| Finding | Severity | Recommendation |

## Phase 4 — Command coverage findings
| Command | Gap | Recommendation |

## Phase 5 — Best-practice crosswalk
| Source (URL + accessed) | Pattern | Category | Recommendation |

## Phase 6 — Gap classification
| Gap | Routes to | Version impact | Recommendation |

## Proposed intake items (drafts — not written)
1. <draft body for intake/YYYY-MM-DD-<name>.md>

## Approval needed
Reply with one of:
- `apply intake N` — write that intake draft as a real intake item.
- `apply intake all` — write every draft as real intake items.
- `defer` — keep the report, no writes.
- `reject` — discard the report.

No file is written until you respond.
```

---

## 7. Maintenance rule

Changes to this file are MINOR if additive. Removing the no-self-modification
guardrail (§4) is **MAJOR** and requires the standard `Enhancement Intake →
Pilot → Roll out` sequence with explicit operator approval at every gate.
