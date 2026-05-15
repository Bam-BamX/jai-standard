# JAI Dev Response Format

**Standard version:** Introduced in JAI v1.3 (2026-05-09)
**Companion command:** Applies to the `Required output` shape of every command in [JAI_COMMANDS.md](./JAI_COMMANDS.md) when the response is emitted by a JAI-conformant dev/implementation agent.
**Companion runtime reviewer:** [JAI_RUNTIME_ADVISORY_REVIEWER.md](./JAI_RUNTIME_ADVISORY_REVIEWER.md) §11 defines the parallel format for *reviewer* output. Both files cross-reference each other.

## 0. Purpose

Defines the canonical shape of a dev/implementation agent's response to the operator within any JAI workflow. Where `JAI_RUNTIME_ADVISORY_REVIEWER.md §11` defines how a *reviewer* should structure its output, this file defines how the *implementer* should structure theirs.

A response that follows this format is reviewable: a downstream reviewer in `JAI runtime advisory review` mode can apply its checklist directly without first having to reconstruct what happened, what was touched, what was verified, or what decision is needed next.

## 1. Relationship to Other JAI Files

- `JAI_RUNTIME_ADVISORY_REVIEWER.md §11` — parallel reviewer output spec. The two share field names where possible.
- `JAI_RUNTIME_ADVISORY_REVIEWER.md §5` — reviewer's "Required Inputs for a Good Review." This file makes that input contract explicit on the dev side.
- `JAI_COMMANDS.md` — every command's `Required output` is the field list; this file is the presentation shape.
- `JAI_GOVERNANCE_STACK.md §6` — safety rules (S1–S8). Nothing in this file overrides safety rules.
- `~/.claude/CLAUDE.md` (operator-global) — token-efficiency baseline. This file does not override it; see §6 Length Guidance.

## 2. When to Use

Use this format when:

- Closing out a batch (any work that produced a diff, commit, branch op, or service change).
- Reporting an audit result.
- Opening a PR and reporting back.
- Ending a multi-step task.

Use a short-form variant (sections may be omitted per §4 Section Omission Rules) when:

- Answering a single read-only question.
- Reporting a tool output that needs no operator decision.

## 3. Standard Section List

Ten sections, in order. Each section's purpose is fixed; the heading text may be re-themed per §Response Modes.

### Section 1 — Plain-English Summary

One short paragraph (1–3 sentences). No jargon. State what happened in terms a non-developer operator can absorb in 5 seconds. Lead with the verb.

### Section 2 — What This Means

Operator-facing implications. Translate the change into system-level effects: what is now true that was not before, what is now safe that was not, what is now possible. Avoid file-by-file detail (Section 3 covers that).

### Section 3 — What Was Touched

Itemized receipts of every modification. Tables preferred. Should answer: which files, which commits, which branches, which services, which ports.

| Asset | Change |
|---|---|
| `<file>` | `<+/-, lines, what>` |
| `<commit>` | `<sha, message>` |
| `<branch>` | `<created / merged / deleted>` |

Required for any session that produced a diff, a commit, a branch operation, a service start/stop, or a config change. Replaceable with "no assets touched" for read-only sessions.

### Section 4 — What Was Not Touched

Explicit negative receipts on every restricted area. The full list, not just the relevant items — silence is not the same as confirmation. Mirror the project's hard-NOT-touch list and `JAI_GOVERNANCE_STACK.md §6` safety rules.

Required every time. Length is the point — the operator should not have to ask.

### Section 5 — Verification Performed

Evidence the dev checked their work. Tables of `command run` → `result`. Includes greps, status commands, smoke tests, type checks, builds. If verification was *skipped*, say so explicitly with reason.

| Check | Command/method | Result |
|---|---|---|

If a UI/feature change is shipped, this section must explicitly state whether browser verification was performed or whether the operator must visually verify.

### Section 6 — Risk / Scope Assessment

Risk rating per `JAI_RUNTIME_ADVISORY_REVIEWER.md §13` (Critical / High / Medium / Low / Info) plus residual-risk callouts and any scope-creep flags.

| Dimension | Rating | Notes |
|---|---|---|
| Change risk | Low / Medium / High | Why |
| Reversibility | Easy / Hard | One revert? schema migration? |
| Blast radius | Local / Repo / Fleet / External | Who is affected if wrong |
| Residual risk | None / Tracked / Unknown | Pointer to register row if tracked |

### Section 7 — Pending Decisions

Bulleted list of every decision the operator needs to make to advance. Each item: what the decision is, what the options are, what the agent recommends if asked.

If no decisions are pending, write: "No pending decisions — batch fully closed."

### Section 8 — Recommended Next Action

One sentence. The single highest-priority next step, derived from Section 7.

### Section 9 — Copy-Paste Operator Reply

A pre-formatted operator reply the user can copy and paste with no editing if they want to approve the recommended next action. Written in the operator's voice. Always supplied. If no further action is recommended, write "No next batch — no copy-paste reply needed."

### Section 10 — Closeout Statement

One-line footer asserting batch state. Allowed values:

- **Batch complete.** No further action pending.
- **Batch complete — awaiting operator approval on Section 7 / Section 9.**
- **Batch partially complete — `<reason>`. Awaiting `<input>`.**
- **Batch halted — `<reason>`.** (Reserved for blockers.)

Mandatory final line. The operator should be able to scroll to the bottom and instantly know the state.

## 4. Section Omission Rules

- Section 3 may be reduced to "no assets touched" for pure read-only audit sessions.
- Section 5 may be reduced to "no verification needed (read-only)" for audit sessions.
- Section 9 may be reduced to "no next batch" only when the closeout is final.
- All other sections are mandatory.

A reviewer treating a missing required section as `Hold — insufficient evidence` is the correct behavior per `JAI_RUNTIME_ADVISORY_REVIEWER.md §5`.

## 5. Visual Conventions (Encouraged, Not Required)

- Headers as anchors.
- Tables for receipts.
- Bold for the key takeaway per section.
- Emoji icons (✅ 🛑 🟢 🟡 🔴 🔼 🟦 🎯 ⏸️ etc.) as scan markers — optional but encouraged for ADHD/dyslexia-friendly scanning.
- Strip emoji if a downstream consumer requires plain ASCII (e.g., agent-to-agent piping).

The spec defines the *required structure*; visual flair is presentation. Adapter behavior (per `JAI_RUNTIME_ADVISORY_REVIEWER.md §4` Codex/Gemini/Generic patterns) may downgrade visual styling without breaking the spec.

## 6. Length Guidance

The default response style remains the operator's global token-efficiency baseline (`~/.claude/CLAUDE.md`): concise, direct, high-signal. This format does not authorize verbose responses.

For trivial single-question responses, the format may collapse to a one-line answer plus a closeout line — sections 1–9 may be elided when nothing meaningful would be lost.

For batches that touched files, ran commands, or changed state, the full ten-section format applies. Length grows with batch significance, not by default.

## 7. Maintenance Rule

Future changes to either this file or `JAI_RUNTIME_ADVISORY_REVIEWER.md §11` must grep for the cross-reference and update the partner if the section list, section names, or omission rules change. Drift between the dev format and the reviewer format would break the implicit input/output contract.

---

## § Response Modes

The structural sections defined above describe the default shape of a JAI dev response. A **response mode** is an optional layer the operator can invoke to change the *register* of the response — its language, density, or framing — without changing the underlying contract on what must be reported.

Modes are opt-in and per-response. The dev agent must not apply a mode unless the operator explicitly invokes it, and must not maintain a mode across batches unless the operator says so.

v1.3 defines exactly one mode: **ST-10**.

### ST-10 — Simple Terms, 10-year-old clarity

**Purpose.** Explain complex technical work in plain, concrete language without removing engineering facts. ST-10 makes the response easier to understand; it does not make the response less accurate.

ST-10 is *not* "dumb it down."

**When to use.**

- The operator types one of these triggers (case-insensitive): `ST-10`, `Explain this ST-10`, `Break this down ST-10`, `ST-10 this dev update`, `Use ST-10 clarity`.
- The operator says ST-10 should apply by default for the rest of the session.
- Otherwise: do not use ST-10. Default response style applies.

**What ST-10 changes.**

- Explain abbreviations the first time they appear (e.g., "PR (pull request)").
- Use shorter sections; avoid long uninterrupted paragraphs.
- Keep technical terms but add a plain-English gloss next to them.
- Separate facts from decisions; separate "what happened" from "what the operator needs to do next."
- Re-theme section headings for the operator's mental model. Crosswalk:

| Default response section | ST-10 section heading |
|---|---|
| 1. Plain-English Summary | 1. What this is |
| 2. What This Means | 2. Why it matters |
| 3. What Was Touched | 3. What changed |
| 4. What Was Not Touched | 4. What did not change |
| 5. Verification Performed | 5. What was verified |
| 6. Risk / Scope Assessment | 6. What risk remains |
| 7. Pending Decisions | 7. What decision is needed |
| 8. Recommended Next Action | 8. Recommended next action |
| 9. Copy-Paste Operator Reply | 9. Copy-paste reply |
| 10. Closeout Statement | (folded into the closeout line) |

**What ST-10 must NOT do.**

- Remove important technical facts.
- Hide uncertainty.
- Skip security warnings.
- Skip approval gates.
- Skip restricted-area confirmations.
- Remove file paths when paths matter.
- Remove branch names when branches matter.
- Remove commit hashes when commit hashes matter.
- Remove command names when command names matter.
- Perform extra work without approval.
- Claim something was verified if it was not.

If a piece of information would be lost by simplifying it, ST-10 keeps the technical version and adds the plain-English gloss alongside — never replaces.

**Jargon rule.** Don't ban technical terms. Pair them with their meaning. Examples:

- *Front-matter* = the metadata block at the top of a markdown file.
- *Drift* = the project is behind the current global JAI version.
- *Migration Pass* = a controlled update that moves a project from one JAI version to another.
- *Restricted areas* = files or systems the dev must not touch without explicit approval.

**Length rule.** ST-10 does not automatically mean short. It means *clear first, then concise*. Small questions get short ST-10 answers; complex dev updates get detailed ST-10 answers, but broken into clear sections with obvious next steps.

**Interaction with other JAI rules.**

- Token efficiency (`~/.claude/CLAUDE.md`) still applies. ST-10 is not a license for verbosity; it is a license for clarity.
- Restricted-area confirmation rules (`JAI_GOVERNANCE_STACK.md §6` / `JAI_RUNTIME_ADVISORY_REVIEWER.md §6`) override every mode. ST-10 cannot be used to soften, paraphrase, or omit a restricted-area confirmation. The exact restricted-area list must appear verbatim when relevant.
- Reviewer mode (`JAI_RUNTIME_ADVISORY_REVIEWER.md §11`) is a separate output shape and is not affected by ST-10.

**Status as a JAI concept.** ST-10 is a *response mode* (presentation layer), not a numbered JAI workflow command. It is intentionally not registered as a top-level entry in `JAI_COMMANDS.md §3–§11`. Workflow commands run procedures; modes shape responses.

**End of mode.** ST-10 ends at the next operator turn unless the operator extends it. The dev agent should not silently keep using ST-10 once the operator stops asking for it.
