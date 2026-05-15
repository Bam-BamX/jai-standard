# [DRAFT] catalogs/TOKEN_BUDGET_HEURISTICS.md

**Status:** [DRAFT] — sandbox prototype for operator review
**Target version:** vNEXT-draft (additive)
**Source:** Concepts from dotclaude `/context-budget` skill per 2026-05-08 vetting. Wording owned by JAI; no code copied.
**Referenced by:** `skills/context_budget.skill.md`.
**Scope:** Universal token-cost heuristics for the always-loaded JAI surface (global standard + project overlay).

## 0. Purpose

Heuristics for estimating per-turn token cost of the always-loaded JAI surface. Used by `skills/context_budget.skill.md` to report whether a project + global standard combination stays under operator-defined budget caps before adding new files.

## 1. Token-Counting Heuristic

**Default heuristic (no API call):** characters / 4. Anthropic documents ~4 characters per token for English text with ~10–15% margin. The heuristic over-estimates for code-heavy content and under-estimates for whitespace-heavy markdown — both within the documented margin.

**Exact mode (with API call):** Anthropic Messages count_tokens API. Skill calls only when `$ANTHROPIC_API_KEY` is set AND the operator has explicitly approved the API call for this batch (per Strict Lane S5 — outbound provider call requires approval). Default mode is heuristic.

## 2. Loading Categories

Files are classified by their loading behavior:

| Category | Definition | Per-turn cost |
|---|---|---|
| **Always-loaded** | `<project>/jai/CLAUDE.md`, `<project>/CLAUDE.md` (project root if present), and any rule file with frontmatter `alwaysApply: true` | Full file size every turn |
| **Path-scoped** | Rule file with frontmatter `paths: [...]` and a glob list | Cost only when working near matched files (heuristic: assume worst case = all globs match) |
| **Invoked-only** | `<project>/jai/skills/*.skill.md` (loaded only when skill is invoked) | Zero per-turn; full size on invocation |
| **Reference-only** | `<project>/jai/templates/*.template.md`, `~/.claude/jai-standard/catalogs/*` | Zero per-turn; full size only when explicitly referenced |
| **Manifest-only** | `<project>/jai/JAI_CONFORMANCE.md`, `PROJECT_EXCEPTIONS.md` | Zero per-turn; loaded only by `JAI fleet sync audit` and migration |

## 3. Budget Caps (universal defaults)

Operator overrides these per project in `<project>/jai/WORKFLOW_RULES.md`.

| Surface | Target | Hard cap |
|---|---|---|
| `<project>/jai/CLAUDE.md` | <25 non-blank lines | 50 lines |
| `<project>/CLAUDE.md` (project root) | <25 non-blank lines | 50 lines |
| Each `alwaysApply: true` rule | <30 lines, ~250 tokens | 500 tokens |
| Total always-loaded per project | <1,000 tokens | 1,500 tokens |
| Total always-loaded fleet-wide (all projects) | <2,000 tokens (assuming 1-2 projects active) | 3,000 tokens |

## 4. Reporting Format

`skills/context_budget.skill.md` produces a markdown table:

```
## Token Budget Report — <project>

| Category | File | Lines | Char count | Heuristic tokens | Notes |
|---|---|---|---|---|---|
| Always-loaded | jai/CLAUDE.md | 24 | 1850 | ~463 | within target |
| Always-loaded | rules/error-handling.md | 88 | 3200 | ~800 | over target; consider splitting |
| Path-scoped | rules/security.md | 25 | 1100 | ~275 | path-scoped: src/api/**, src/auth/** |
| Invoked-only | skills/planning.skill.md | 120 | 5500 | ~1375 | invoked-only; per-turn cost = 0 |
| ... | | | | | |

## Aggregate
- Always-loaded total: ~1,263 tokens / 1,500 hard cap (84%)
- Path-scoped total (worst case): ~275 tokens
- Invoked-only total (cumulative): ~5,500 tokens

## Verdict
PASS / NEAR LIMIT (>80%) / OVER BUDGET (>100%)

## Top 3 always-loaded contributors
1. rules/error-handling.md — ~800 tokens (consider splitting; only 30% relevant to most tasks)
2. jai/CLAUDE.md — ~463 tokens (within target; no action)
3. (none other always-loaded)

## Recommendations
- Consider converting rules/error-handling.md to path-scoped (paths: src/api/**, src/services/**)
- Move project context out of CLAUDE.md into PROJECT_CONTEXT.md (already SHOULD per JAI_OVERLAY_SCHEMA.md §2)
```

## 5. When to Run

Per `JAI_AUTOMATION_LANES.md`, this is a **read-only invoked-only skill** — no commits, no file changes, no provider calls (under default heuristic mode).

Recommended invocation cadence:

- Before adopting a new always-loaded rule (Review Lane action)
- Before lifting a SCC skill to global (would now load fleet-wide)
- During monthly fleet sync audit
- On-demand whenever the operator suspects budget creep

## 6. Caveats

- Heuristic is approximate; use API mode for exact counts when budget decision is close
- Claude Code does not expose live context state, so the report estimates *what would load*, not what is currently loaded
- Hooks (when eventually added) are silent on success and contribute zero per-turn unless they inject context
- Path-scoped totals are worst-case (all globs match); typical tasks load only the relevant subset

## 7. Maintenance Rule

Updating a budget cap is **MINOR** if relaxing (raises ceiling); **MAJOR** if tightening (forces existing projects to re-evaluate).

## 8. Re-Vetting

Re-vetting fires when Anthropic updates the documented chars-per-token ratio or releases a Claude Code feature that exposes live context state. Re-vetting fires the standard Enhancement Intake workflow.
