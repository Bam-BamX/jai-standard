# catalogs/

Reusable detector libraries: regex catalogs, taxonomies, checklist sets. Referenced by `policies/` and by project-level skills.

**Empty at v1.0** — scaffolding only. First seeds proposed:
- `INJECTION_CATEGORIES.md` — five-category prompt-injection taxonomy (Instruction Override, Role-Playing/DAN, Encoding/Obfuscation, Context Manipulation, Instruction Smuggling) plus severity rubric. Source: `references/repo-vetting-lasso-vs-dotclaude-2026-05-08.md` §7.
- `SECRET_REGEX_CATALOG.md` — AKIA, ghp_, gho_, ghs_, ghr_, github_pat_, sk-, xox[bpras]-, private-key blocks, conn-strings with embedded creds, env-var-aware exclusions. Source: same vetting report §7.

Adding an entry is a MINOR version bump and follows the [Enhancement Intake workflow](../JAI_GOVERNANCE_STACK.md#4--enhancement-workflow). Each catalog file should cite its source (intake item, external repo + commit SHA, or incident).
