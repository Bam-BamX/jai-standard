# policies/

Cross-cutting rules every JAI-conforming project inherits. Project files extend these; they don't redefine them.

**Seeded v1.5** — first real entry landed (canonical safety rules).

Files:
- [`SAFETY_RULES.md`](./SAFETY_RULES.md) — canonical S1–S8. Lifted from `JAI_GOVERNANCE_STACK.md §6` at v1.5 (2026-05-10) per intake item `intake/2026-05-10-safety-rules-policy-lift.md`.

Planned (still pending intake):
- `SECURITY_RULES.md` — universal portion of the security policy.
- `THREAT_MODEL.md` — universal threat catalog (e.g. prompt-injection categories, secret-leak vectors).
- `WORKFLOW_RULES.md` — universal portion of the workflow policy (push protections, branch rules, etc.).

Adding a file here is a MINOR version bump and follows the [Enhancement Intake workflow](../JAI_GOVERNANCE_STACK.md#4--enhancement-workflow).
