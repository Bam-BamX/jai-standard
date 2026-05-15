# skills/

Canonical skill specs (prompt + I/O contract). Project `/jai/skills/` files are project-specific instantiations of these canonical specs.

**Empty at v1.0** — lazy migration. The 10 skills currently live in Trend Scanner v1's `jai/skills/` (per the project-level implementation plan):
- `backend_change.skill.md`
- `dependency_review.skill.md`
- `docs_cleanup.skill.md`
- `frontend_change.skill.md`
- `git_sync.skill.md`
- `implementation_review.skill.md`
- `infra_audit.skill.md`
- `secret_scanning.skill.md`
- `security_review.skill.md`
- `threat_modeling.skill.md`

Each will be lifted into a canonical version here when first amended (i.e. when an Enhancement Intake item proposes a change to it). Until then, projects use the implementation-plan versions and the JAI Standard v1.0 inherits them by reference.

Adding or amending a canonical skill is a MINOR version bump and follows the [Enhancement Intake workflow](../JAI_GOVERNANCE_STACK.md#4--enhancement-workflow).
