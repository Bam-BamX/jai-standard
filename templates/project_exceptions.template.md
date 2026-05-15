# [TEMPLATE] PROJECT_EXCEPTIONS.md

**Template version:** vNEXT-draft
**Used by:** every project's `<project>/jai/PROJECT_EXCEPTIONS.md` (optional file per [`JAI_OVERLAY_SCHEMA.md §3`](../JAI_OVERLAY_SCHEMA.md))
**Grammar reference:** [`JAI_OVERRIDE_CONTRACT.md`](../JAI_OVERRIDE_CONTRACT.md)

---

> Copy this file to `<project>/jai/PROJECT_EXCEPTIONS.md`. An empty file (no exception entries) is valid — it means "no overrides declared."

---

```markdown
# Project Exceptions

This file declares all project-specific deviations from the global JAI standard. [JAI_OVERRIDE_CONTRACT.md §1](file:///C:/Users/Mitch/.claude/jai-standard/JAI_OVERRIDE_CONTRACT.md) defines four allowed override categories. Three are recorded here as entries with an `Action:` keyword:

- **NARROW** — stricter version of a global rule (always allowed)
- **AUGMENT** — new project-specific rule on top of global (always allowed; no conflict)
- **REPLACE_WITH_EXCEPTION** — substitute a global rule with a different rule (allowed only with operator approval, sunset date, and no S1–S8 weakening)

The fourth allowed category, **ADOPT_OPTIONAL** (opting into a global file marked optional in `JAI_OVERLAY_SCHEMA.md`), is recorded in `JAI_CONFORMANCE.md` `adopted_optional_files:` frontmatter, not as an entry in this file.

**Forbidden:** WEAKEN, REMOVE, CONTRADICT, SILENT_OVERRIDE per [JAI_OVERRIDE_CONTRACT.md §2](file:///C:/Users/Mitch/.claude/jai-standard/JAI_OVERRIDE_CONTRACT.md). Audit flags any of these as `INVALID_WEAKENING` and the project loses conformance.

---

## Active Exceptions

(Empty. No overrides declared.)

---

## Exception Template (copy for each entry)

### Exception: <kebab-case-slug>

- **Rule:** <id from policies/ — e.g., SECURITY_RULES.md §3.2 — or NEW for AUGMENT>
- **Action:** NARROW | AUGMENT | REPLACE_WITH_EXCEPTION
- **Global wording:** <quoted from global policy; "N/A" for AUGMENT>
- **Project wording:** <project's stricter constraint, new rule, or substitution>
- **Justification:** <why this project needs this deviation>
- **Approved at:** YYYY-MM-DD
- **Approved by:** <operator>
- **Operator approval reference:** <commit hash, decision log entry, or chat record>
- **Sunset date:** YYYY-MM-DD (REQUIRED for REPLACE_WITH_EXCEPTION; not applicable for NARROW or AUGMENT)
- **Conformance impact:** <does this affect any required-file presence? any frontmatter declaration?>

---

## Expired Exceptions (audit log)

When a REPLACE_WITH_EXCEPTION entry's sunset date passes, move the entry here with a status note. Audit treats expired entries as `EXPIRED_EXCEPTION` (warning) until renewed or removed.

| Slug | Original sunset | Status | Resolution date |
|---|---|---|---|
| (none) | | | |
```
