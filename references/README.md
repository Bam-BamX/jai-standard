# references/

External vetting reports and vendor-doc pointers. Read-only inputs to the [Enhancement Intake workflow](../JAI_GOVERNANCE_STACK.md#4--enhancement-workflow).

Naming:
- Repo vetting: `repo-vetting-<short-name>-YYYY-MM-DD.md`
- Vendor doc pointer: `vendor-<source>-YYYY-MM-DD.md`

A vetting report's conclusions either get lifted into the standard (with attribution + a `JAI_CHANGELOG.md` entry + version bump) or are recorded as a rejection in `JAI_CHANGELOG.md`. The vetting file itself stays here regardless — the rejection is part of the audit trail.

## v1.0 contents

- `repo-vetting-lasso-vs-dotclaude-2026-05-08.md` — Side-by-side audit of `lasso-security/claude-hooks` (prompt-injection defender) and `poshan0126/dotclaude` (full `.claude/` starter kit). Recommendations: lasso = Reference now, Adapt later (with separate approval); dotclaude = Reject for adoption, Reference only. Carried in at bootstrap from chat output 2026-05-08. **No intake item filed yet** — the recommendations are deferred until Mitch decides which extractable concepts (§7 of the report) are worth turning into MINOR bumps for v1.1.
