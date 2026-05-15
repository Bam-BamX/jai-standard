**Status:** Promoted (seed batch) on 2026-05-14; sandbox original retained.

# Intake — Seed catalogs (SECRET_REGEX, DANGEROUS_COMMANDS, INJECTION_CATEGORIES, TOKEN_BUDGET_HEURISTICS)

**Date:** 2026-05-14
**Source:** Plan `role-developer-task-research-shimmering-anchor.md` §"Useful dotclaude / lasso Concepts". Acts on cherry-pick concepts deferred from 2026-05-08 vetting at [`references/repo-vetting-lasso-vs-dotclaude-2026-05-08.md`](file:///C:/Users/Mitch/.claude/jai-standard/references/repo-vetting-lasso-vs-dotclaude-2026-05-08.md).
**Classification:** Detector libraries (4 new files in `catalogs/`)
**Version impact:** MINOR (additive — catalogs are referenced by skills/agents, not loaded per turn)
**Pilot project:** Used by `skills/secret_scanning.skill.md`, `skills/security_review.skill.md`, `skills/threat_modeling.skill.md`, `skills/context_budget.skill.md` — all part of vNEXT-draft prototype.
**Status:** Sandbox drafts — see [`Repo_Lab/lab/jai-standard-prototype/catalogs/`](file:///C:/Users/Mitch/Projects/Repo_Lab/lab/jai-standard-prototype/catalogs/). Awaiting operator review.

## Problem

`~/.claude/jai-standard/catalogs/` is currently empty (only README). The 2026-05-08 vetting at [`references/repo-vetting-lasso-vs-dotclaude-2026-05-08.md §7`](file:///C:/Users/Mitch/.claude/jai-standard/references/repo-vetting-lasso-vs-dotclaude-2026-05-08.md) identified specific extractable concepts from dotclaude and lasso:

- Secret regex catalog (dotclaude `scan-secrets.sh`)
- Dangerous-command rubric (dotclaude `block-dangerous-commands.sh`)
- Five-category prompt-injection taxonomy (lasso `patterns.yaml`)
- Token-budget heuristics (dotclaude `/context-budget`)

The vetting concluded these should be filed as intake items but no items were filed. Four versions later (v1.0 → v1.7), the concepts remain deferred while skills that would use them already exist project-locally (e.g., SCC's `skills/secret_scanning.skill.md` re-implements pattern detection inline).

## Proposal

Create four detector-library catalogs:

1. **`catalogs/SECRET_REGEX_CATALOG.md`** — high-confidence secret patterns (cloud keys, code-hosting tokens, LLM API keys, private key blocks, connection strings) + env-var-aware false-positive exclusions + severity rubric.
2. **`catalogs/DANGEROUS_COMMANDS.md`** — push-to-protected-branch, force-push, rm -rf system dirs, DROP/TRUNCATE/DELETE-without-WHERE, chmod 777, curl|sh, dd, mkfs, package publish without --dry-run, Docker volume operations against protected volumes.
3. **`catalogs/INJECTION_CATEGORIES.md`** — five-category prompt-injection taxonomy (Instruction Override, Role-Playing/DAN, Encoding/Obfuscation, Context Manipulation, Instruction Smuggling) with detection patterns and severity rubric.
4. **`catalogs/TOKEN_BUDGET_HEURISTICS.md`** — chars/4 heuristic, loading categories (always-loaded / path-scoped / invoked-only / reference-only / manifest-only), budget caps (CLAUDE.md hard cap 50 lines; always-loaded total hard cap 1500 tokens), reporting format.

All catalogs are **referenced by skills/agents**, not loaded per turn — zero token cost until invoked.

## Open decisions

- **OD-1.** Should the `INJECTION_CATEGORIES.md` taxonomy be added to existing `policies/THREAT_MODEL_BASELINE.md` §1.1 inline, or referenced from the catalog? Recommendation: **reference from catalog** — keeps `THREAT_MODEL_BASELINE.md` lightweight; catalog can grow without bloating the policy file.
- **OD-2.** Should `TOKEN_BUDGET_HEURISTICS.md` API-mode (Anthropic count_tokens) require explicit operator approval per S5? Recommendation: **yes** — outbound provider call requires approval, even for benign measurement.

## Risk

- **LOW.** Catalogs are docs-only. Skills already reference them via the vNEXT-draft prototype skills.
- **Re-vetting risk:** catalogs may become stale as new attack patterns emerge. Mitigation: each catalog has a "Re-Vetting" §; intake item filed when new pattern observed.

## Migration impact

- Project-specific secret/command/injection patterns continue to live in `<project>/jai/SECURITY_RULES.md` (via AUGMENT). Catalogs hold the universal patterns; projects add stack-specific ones.
- SCC's existing inline pattern lists in `skills/secret_scanning.skill.md` get scrubbed to reference the canonical catalog (per the lifted-skill pattern in `Repo_Lab/lab/jai-standard-prototype/skills/secret_scanning.skill.md`).

## Pilot

Each catalog is referenced by at least one prototype skill in `Repo_Lab/lab/jai-standard-prototype/skills/`. Walk a hypothetical detection scenario (e.g., a fake secret in source) against `secret_scanning.skill.md` invoking `SECRET_REGEX_CATALOG.md`; expected: detected with appropriate severity.

## Approval gate

Standard intake → `JAI standard update`. Companion to [`2026-05-14-skills-templates-lift.md`](./2026-05-14-skills-templates-lift.md) (skills that reference these catalogs).
