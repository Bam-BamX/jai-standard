# [DRAFT] catalogs/DANGEROUS_COMMANDS.md

**Status:** [DRAFT] — sandbox prototype for operator review
**Target version:** vNEXT-draft (additive)
**Source:** Concepts cherry-picked from dotclaude `block-dangerous-commands.sh` per 2026-05-08 vetting. Wording owned by JAI; no code copied.
**Referenced by:** Builder pre-flight (read-only check before running any command), Agent 2 reviewer security checklist, any future hook under `JAI_HOOK_CONTRACT.md`.
**Scope:** Universal. These are Blocked Lane commands per [`JAI_AUTOMATION_LANES.md §2.5`](../JAI_AUTOMATION_LANES.md) — no automation may invoke them regardless of approval claim.

## 0. Purpose

Canonical rubric of commands that destroy state irreversibly or breach Blocked Lane. Used by Builder pre-flight and Agent 2 reviewer to halt before execution. The rubric is *enforcement reference*; the policy that they are blocked is in [`JAI_AUTOMATION_LANES.md`](../JAI_AUTOMATION_LANES.md).

## 1. Git: Push to Protected Branch

Default protected branches: `main`, `master`, plus any branch listed in `<project>/jai/WORKFLOW_RULES.md` extension.

| Pattern | Why blocked |
|---|---|
| `git push origin (main\|master\|<protected>)` | Direct push to protected branch |
| `git push (main\|master\|<protected>)` (when on that branch) | Implicit push to protected branch |
| `git push :+(main\|master\|<protected>)` | Branch-deletion push |
| `git push HEAD:(main\|master\|<protected>)` | Refspec form, same effect |
| `git push <remote> <local>:(main\|master\|<protected>)` | Refspec mapping |
| `git push --force` (any target) | Force-push — even with `--force-with-lease` requires Strict Lane per-action approval |

## 2. Git: Other Destructive Operations

| Pattern | Why blocked |
|---|---|
| `git reset --hard` | Discards uncommitted changes irreversibly |
| `git clean -f` (or `-fd`, `-fdx`) | Deletes untracked files irreversibly |
| `git branch -D` | Force-deletes branch (loses unmerged commits) |
| `git push --delete` | Remote branch deletion |
| `git tag -d <tag>` | Tag deletion |
| `git config --global` | Modifies global git config |
| `git config init` | Initializes config |
| `git filter-branch` | History rewriting |
| `git rebase -i` | Interactive rebase (also blocked because it's interactive) |
| `git reflog expire --expire=now --all` | Wipes reflog |

## 3. Filesystem: rm -rf

| Pattern | Why blocked |
|---|---|
| `rm -rf /` | Root deletion |
| `rm -rf ~` | Home directory deletion |
| `rm -rf $HOME` | Same as above |
| `rm -rf $VAR` (where `$VAR` is unresolved or empty) | Unresolved variable expansion → potentially `rm -rf /` |
| `rm -rf /usr` `/etc` `/var` `/bin` `/sbin` `/lib` `/opt` `/root` `/boot` | System directory deletion |
| `rm -rf ../..` (or any path crossing project root) | Escapes project sandbox |
| `rm -rf <path-mapping-to-Docker-volume>` | Per [`policies/SECURITY_RULES.md §5`](../policies/SECURITY_RULES.md) |

## 4. SQL: Destructive

| Pattern | Why blocked |
|---|---|
| `DROP TABLE` | Irreversible table deletion |
| `DROP DATABASE` | Irreversible database deletion |
| `DROP SCHEMA` | Irreversible schema deletion |
| `DELETE FROM <table>` (without `WHERE` clause) | Whole-table delete |
| `TRUNCATE TABLE` | Whole-table delete |
| `ALTER TABLE ... DROP COLUMN` | Irreversible column deletion |
| `UPDATE <table> SET ...` (without `WHERE` clause) | Whole-table update |

Per-statement analysis: split on `;` and check each statement independently. SQL embedded in source files (string literals containing these patterns) is reported but not auto-blocked unless the file is going to be executed.

## 5. Filesystem: Permission Changes

| Pattern | Why blocked |
|---|---|
| `chmod 777` (or `chmod 0777`) | World-writable; security regression |
| `chmod a+rwx` | World-writable; security regression |
| `chmod -R 777` | Recursive world-writable |

## 6. Network: Bootstrap / Pipe-to-Shell

| Pattern | Why blocked |
|---|---|
| `curl ... \| sh` | Curl-pipe-bash bootstrap (supply-chain risk) |
| `curl ... \| bash` | Same |
| `wget ... \| sh` | Same |
| `bash -c "$(curl ...)"` | Process-substitution variant |
| `bash <(curl ...)` | Process-substitution variant |
| `eval "$(curl ...)"` | Eval of network-fetched content |

## 7. Storage / Disk: Destructive

| Pattern | Why blocked |
|---|---|
| `dd of=/dev/...` | Raw disk write |
| `mkfs` (any variant) | Filesystem format |
| `> /dev/sd*` | Raw disk overwrite via redirect |
| `> /dev/nvme*` | Raw NVMe overwrite |

## 8. Docker: Volume / Production

| Pattern | Why blocked |
|---|---|
| `docker volume rm pgdata` | Per [`policies/SECURITY_RULES.md §5`](../policies/SECURITY_RULES.md) |
| `docker volume rm redisdata` | Same |
| `docker volume prune` | Removes all unused volumes |
| `docker system prune -a` | Removes all unused images, containers, networks, volumes |
| `docker compose down -v` | Removes named volumes |

## 9. Package Manager: Publish

| Pattern | Why blocked |
|---|---|
| `npm publish` (without `--dry-run`) | Publishes to npm registry |
| `pip upload` / `twine upload` (without `--repository testpypi`) | Publishes to PyPI |
| `cargo publish` (without `--dry-run`) | Publishes to crates.io |
| `gem push` | Publishes to RubyGems |

## 10. Severity Rubric

All entries above are **Blocked Lane** per [`JAI_AUTOMATION_LANES.md §2.5`](../JAI_AUTOMATION_LANES.md). Severity = always Critical.

If detection happens *before* execution: hard-block.
If detection happens *after* execution (post-mortem): incident report; rollback if possible; operator-led remediation.

## 11. Action by Detection Point

| Detector | Action |
|---|---|
| Builder pre-flight (read-only command parse before invocation) | Refuse to invoke; report pattern matched; route to Orchestrator |
| Agent 2 reviewer (post-Builder review of the dev report) | Issue `Hold` or `Rejected` per [`JAI_RUNTIME_ADVISORY_REVIEWER.md §11.8`](file:///C:/Users/Mitch/.claude/jai-standard/JAI_RUNTIME_ADVISORY_REVIEWER.md) |
| Future hook layer (deferred per `JAI_HOOK_CONTRACT.md`) | Hard-block at PreToolUse; log to HOOK_LOG.md |

## 12. Project Extensions

A project Augments this catalog in `<project>/jai/SECURITY_RULES.md`:

- Project-specific protected branches (added to §1)
- Project-specific protected volumes (added to §8)
- Project-specific destructive paths (e.g., `<project>/jai/CLAUDE.md` itself if the project considers it sacred)

A project may NOT remove patterns from this catalog (Weaken is forbidden).

## 13. Maintenance Rule

Adding a new pattern is **MINOR**. Removing or weakening a pattern is **MAJOR**.
