# JAI Fleet Sync Audit — Workflow Spec

**Standard version covered:** JAI v1.2 onward
**Companion command:** [JAI_COMMANDS.md §6](./JAI_COMMANDS.md#6-command-jai-fleet-sync-audit)
**Companion registry:** [JAI_PROJECT_REGISTRY.md](./JAI_PROJECT_REGISTRY.md)
**Companion runtime reviewer:** [JAI_RUNTIME_ADVISORY_REVIEWER.md](./JAI_RUNTIME_ADVISORY_REVIEWER.md) §17 (workflow shapes for any follow-up migrations)

## 0. Purpose

Defines the read-only procedural spec for auditing every known project
repository on this machine against the current global JAI standard.
Invoked via the `JAI fleet sync audit` trigger phrase. Outputs a single
report; never modifies any project.

## 1. Relationship to Other JAI Files

- `JAI_COMMANDS.md §6` — command-trigger entry (phrase, gates, output
  shape).
- This file — the procedural spec the command resolves to.
- `JAI_PROJECT_REGISTRY.md` — the canonical list of known project paths
  to walk.
- `JAI_RUNTIME_ADVISORY_REVIEWER.md §17` — applies if any project found
  to be drifting needs a Migration Pass.

## 2. When to Use

- After a global JAI version bump, to find which projects now drift.
- Periodically (operator-cadenced) to surface neglected projects.
- Before deciding which projects to schedule for migration.
- Before announcing a new standard rule project-wide, to scope impact.

## 3. Required Reads (in order)

1. `~/.claude/jai-standard/JAI_VERSION` — current global target.
2. `~/.claude/jai-standard/JAI_PROJECT_REGISTRY.md` — list of project
   paths to walk.
3. For each registered project: root `<path>/AGENTS.md` presence/content
   summary for Codex entrypoint drift, plus `<path>/jai/CLAUDE.md`
   front-matter only (read; do not parse code).

## 4. Forbidden Actions

The audit is **strictly read-only**. The following are disallowed
without separate explicit approval:

- Do not edit any file in any project.
- Do not edit any global JAI file.
- Do not run any project's app or scripts.
- Do not run Docker, migrations, or providers in any project.
- Do not read `.env` files in any project.
- Do not commit or push in any project.
- Do not delete branches.
- Do not follow symlinks outside the registered path.
- Do not invoke any tool that requires network access beyond local git
  metadata.

## 5. Per-Project Audit Procedure

For each entry in `JAI_PROJECT_REGISTRY.md`:

1. **Path existence.** Confirm `<path>` exists on disk. If not → mark
   `path missing`, skip remaining checks.
2. **Git tracking.** Run `git -C <path> rev-parse --is-inside-work-tree`.
   If not a repo → mark `not a git repo`, continue with §5.4 only.
3. **JAI presence.** Check whether `<path>/jai/` exists. If not → mark
   `not onboarded`, continue with §5.4 only.
4. **Branch + status.** Read `git -C <path> branch --show-current` and
   `git -C <path> status --short`. Record both.
5. **Declared JAI version.** Read `<path>/jai/CLAUDE.md` front-matter.
   Parse the `jai_standard:` field. If front-matter absent or field
   missing → record `unknown`.
6. **Codex entrypoint.** Check whether `<path>/AGENTS.md` exists. For
   Codex-enabled projects at v1.8 or later, missing `AGENTS.md` records
   `codex entrypoint missing`. If present, verify it is a thin pointer to
   the project `CLAUDE.md` and/or `jai/` framework and does not weaken
   approval gates. Weakened gates record `codex entrypoint divergent`.
7. **Drift comparison.** Compare declared version against global
   `JAI_VERSION` per §6 below.
8. **Last audit date.** Read from registry; do not update yet (updates
   are a write step that happens only on operator approval after the
   audit completes).

Each per-project check should fail closed: any inability to inspect a
field records `unknown` rather than guessing.

## 6. Drift Classification

| Drift level | Meaning |
|---|---|
| `none` | Declared version equals global version. |
| `minor` | Declared version is one or more MINOR behind global, same MAJOR. |
| `major` | Declared version is one or more MAJOR behind global. |
| `unknown` | Front-matter present but version field unparseable, or `jai/CLAUDE.md` exists without front-matter. |
| `not onboarded` | Path exists but no `<path>/jai/` directory. |
| `not a git repo` | Path exists but is not a git working tree. |
| `path missing` | Registry path does not resolve. |
| `codex entrypoint missing` | Project is Codex-enabled or declares v1.8+, but root `AGENTS.md` is absent. |
| `codex entrypoint divergent` | Root `AGENTS.md` weakens JAI gates or bypasses project `CLAUDE.md` / `jai/`. |

A project at `none` requires no action. Projects at `minor` are
candidates for the next migration cycle (operator priority). Projects
at `major` should be flagged as urgent. `unknown` / `not onboarded` /
`not a git repo` / `path missing` are reported but require operator
attention before the audit can produce a clean classification.

## 7. Output Format

A single markdown table, returned in chat. Same shape as the table in
`JAI_COMMANDS.md §6`, plus a `notes` column for operator-relevant
context (e.g., dirty tree, non-default branch, last audit date).

| project path | repo name | current branch | git status | JAI status | declared JAI version | target JAI version | drift level | last audited | notes | recommended next action |
|---|---|---|---|---|---|---|---|---|---|---|

The `notes` column must include `Codex entrypoint: present`, `Codex entrypoint:
missing`, `Codex entrypoint: divergent`, or `Codex entrypoint: not applicable`.

A summary line below the table:

```
N projects audited · X clean · Y minor drift · Z major drift · W not onboarded
```

## 8. Recommended Next Action (per row)

- `none` → `(none)`
- `minor` → `Schedule Migration Pass to vN.M when convenient.`
- `major` → `Schedule Migration Pass to vN.M with priority.`
- `unknown` → `Inspect <path>/jai/CLAUDE.md front-matter manually.`
- `not onboarded` → `Run JAI new-repo intake on <path>.`
- `not a git repo` → `Initialize git or remove from registry.`
- `path missing` → `Update or remove registry entry.`
- `codex entrypoint missing` → `Schedule Migration Pass to add thin root AGENTS.md.`
- `codex entrypoint divergent` → `Halt and reconcile AGENTS.md against project JAI gates.`

## 9. Approval Gate

Fleet audit is **report-only**. Any project migration that the report
recommends is a separate workflow (`JAI migration pass` per
`JAI_COMMANDS.md §5`). The audit does not bundle migration approval.

## 10. Privacy / Safety

- Never read `.env` files in any audited project (cross-project rule
  S2 of `JAI_RUNTIME_ADVISORY_REVIEWER.md §6`).
- Never read provider configs or credential stores.
- Never follow symlinks outside the registered project path.
- Never inspect uncommitted work-in-progress diffs beyond `git status`
  (which is metadata-only).
- The audit's report should not include any path-shaped content
  outside the registered paths and the global standard directory.

## 11. Output Cache (optional)

The audit may optionally update each registry row's `last audited`
column after the report is delivered. Updating the registry is itself
a write operation and follows the same approval gate as any global
file edit (per `JAI_GOVERNANCE_STACK.md §6 S7`). If the operator
declines, the registry stays as-is and the next audit re-walks every
project.
