# [TEMPLATE] JAI_CONFORMANCE.md (Drift Manifest)

**Template version:** vNEXT-draft
**Used by:** every project's `<project>/jai/JAI_CONFORMANCE.md` per [`JAI_OVERLAY_SCHEMA.md §1`](../JAI_OVERLAY_SCHEMA.md)
**Schema reference:** [`JAI_DRIFT_DETECTION.md §1`](../JAI_DRIFT_DETECTION.md)

---

> Copy this file to `<project>/jai/JAI_CONFORMANCE.md`. Replace placeholders. Do not modify the schema structure.

---

```markdown
---
jai_standard_version: vX.Y           # what this project conforms to
declared_at: YYYY-MM-DD              # when this version was declared
last_audit: YYYY-MM-DD               # when last fleet-sync-audit ran
last_migration: YYYY-MM-DD           # date of last completed migration pass
project_name: <name>
adopted_optional_files:              # files the project opts into per JAI_OVERLAY_SCHEMA.md MAY
  - PROCESS_CONVENTIONS.md
  - CLEANUP_REGISTER.md
---

# JAI Conformance — <project name>

## Required Files (per JAI_OVERLAY_SCHEMA.md §1 at vX.Y)
- [x] CLAUDE.md (present, frontmatter declares vX.Y)
- [x] PROJECT_CONTEXT.md (present)
- [x] SECURITY_RULES.md (present, extends policies/SECURITY_RULES.md@vX.Y)
- [x] WORKFLOW_RULES.md (present, extends policies/WORKFLOW_RULES.md@vX.Y)
- [x] THREAT_MODEL.md (present, extends policies/THREAT_MODEL_BASELINE.md@vX.Y)
- [x] JAI_CONFORMANCE.md (this file)

## Recommended Files (per JAI_OVERLAY_SCHEMA.md §2)
- [ ] DECISION_LOG.md
- [ ] CURRENT_STATE.md
- [ ] HANDOFF.md
- [ ] VULNERABILITY_REVIEW.md
- [ ] ARCHITECTURE.md
- [ ] INFRASTRUCTURE.md

## Optional Files Adopted (per JAI_OVERLAY_SCHEMA.md §3)
- [ ] PROCESS_CONVENTIONS.md
- [ ] CLEANUP_REGISTER.md
- [ ] PROJECT_EXCEPTIONS.md (file may be empty — that means "no overrides declared")

## Forbidden Files (per JAI_OVERLAY_SCHEMA.md §4)
None present. (Verified at last_audit.)

## Lifted-File Checksums (per JAI_OVERLAY_SCHEMA.md §6)
| File | Local SHA-256 | Global SHA-256 (at vX.Y) | Status |
|---|---|---|---|
| skills/planning.skill.md | <hash> | <hash> | match / drift / local-fork |
| skills/security_review.skill.md | <hash> | <hash> | match |
| skills/secret_scanning.skill.md | <hash> | <hash> | match |
| skills/threat_modeling.skill.md | <hash> | <hash> | match |
| skills/context_budget.skill.md | <hash> | <hash> | match |
| skills/drift_audit.skill.md | <hash> | <hash> | match |

(Add or remove rows based on which canonical skills the project has lifted locally. Project-local-only skills not listed in global do not appear here.)

## Declared Exceptions (per JAI_OVERRIDE_CONTRACT.md)
See PROJECT_EXCEPTIONS.md.

Summary:
- Narrow: <count>
- Augment: <count>
- Replace-with-Exception: <count> (next sunset: YYYY-MM-DD)
- Invalid-Weakening: 0 (would block conformance)

## Audit History
| Date | Class | Notes |
|---|---|---|
| YYYY-MM-DD | ALIGNED / DRIFT_ACCEPTABLE / DRIFT_BLOCKING / DIVERGENT | <brief notes> |
```
