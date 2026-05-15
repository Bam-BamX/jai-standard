# intake/

In-flight Enhancement Intake proposals. One file per proposal. Naming: `YYYY-MM-DD-<short-name>.md`.

**Empty at v1.0.**

Each intake file follows the [Enhancement Intake workflow](../JAI_GOVERNANCE_STACK.md#4--enhancement-workflow) and tracks the proposal through its six steps:

1. **Identify** — one-paragraph problem statement.
2. **Vet** — full External Repo Vetting (for external sources) or shorter internal vetting note (for incidents/ideas). Outcome: Adopt / Adapt / Reference only / Reject / Defer.
3. **Classify** — universal rule? reusable detector? new skill? new template? shape change? project-specific?
4. **Adapt** — draft diff against `~/.claude/jai-standard/` plus proposed `JAI_CHANGELOG.md` entry and version bump.
5. **Pilot** — applied to Trend Scanner v1 first; observation window logged in the intake file.
6. **Roll out** — committed; intake file moved or archived with outcome.

Once an intake item closes (rolled out, rejected, or deferred indefinitely), record the outcome at the top of the file and leave it in `intake/` for one version cycle, then move to an `archive/` subdirectory at the next bump. Until then, the file's presence here is the signal that the proposal is in flight.

When `references/` contains a vetting report whose recommendation is **Adapt** or **Adopt**, the corresponding intake item lives here with the same date stem.
