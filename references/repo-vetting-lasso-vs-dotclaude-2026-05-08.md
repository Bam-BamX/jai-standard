# JAI external repo vetting — `lasso-security/claude-hooks` vs `poshan0126/dotclaude`

**Date:** 2026-05-08
**Mode:** Read-only audit. No installs, no settings changes, no file copies.
**Standard version at time of vetting:** v1.0 (bootstrap)
**Repo SHAs at time of audit:**
- `lasso-security/claude-hooks` — default branch `dev`, last update 2026-05-07. (No specific commit SHA recorded; refresh if revisited.)
- `poshan0126/dotclaude` — default branch `main`, last update 2026-05-08. (No specific commit SHA recorded; refresh if revisited.)
**Current /jai surface (for context):**
- 11 policy/context docs: `CLAUDE.md`, `SECURITY_RULES.md`, `THREAT_MODEL.md`, `VULNERABILITY_REVIEW.md`, `WORKFLOW_RULES.md`, plus 6 supporting docs.
- 10 skills incl. `security_review`, `secret_scanning`, `threat_modeling`, `dependency_review`, `infra_audit`.
- 4 templates. No hooks, no settings overrides.
- THREAT_MODEL.md flags **prompt-injection as "partial — user discipline; no scanner in CI"** — i.e. an explicit known gap.

---

## 1 · Purpose

| | lasso-security/claude-hooks | poshan0126/dotclaude |
|---|---|---|
| **Problem solved** | Indirect prompt-injection defense. Scans output of `Read`, `WebFetch`, `Bash`, `Grep`, `Task`, `mcp__*` tools for known injection patterns and emits a `PostToolUse` warning so Claude treats the content with suspicion. | Opinionated `.claude/` starter kit: 5 reviewer agents, 9 skills, 6 modular rules, 6 hooks (protect-files, scan-secrets, block-dangerous-commands, warn-large-files, format-on-save, session-start), settings.json. |
| **Relevant to JAI?** | **High.** Directly addresses the gap THREAT_MODEL.md already names. | **Low–medium.** Most components duplicate /jai/ skills+rules. Hooks (deterministic enforcement) are the only thing /jai doesn't have. |

---

## 2 · Repository structure

| | lasso | dotclaude |
|---|---|---|
| **Stars / size** | 225 ⭐ / ~5 MB / TypeScript+Python / MIT | 386 ⭐ / ~150 KB / Shell+Markdown / MIT |
| **Default branch** | `dev` | `main` |
| **Key folders** | `.claude/skills/prompt-injection-defender/` (SKILL.md, patterns.yaml, hooks/defender-python, hooks/defender-typescript, cookbook, test-prompts) | `agents/`, `skills/`, `rules/`, `hooks/`, `plugins/` (14 marketplace entries), `.claude-plugin/marketplace.json`, `settings.json` |
| **Install scripts** | `install.sh` — copies hook into target project's `.claude/hooks/`, writes `.claude/settings.local.json`, **runs `curl https://astral.sh/uv/install.sh \| sh`** if `uv` missing. | No bash installer; two paths: (a) `/plugin marketplace add poshan0126/dotclaude` (third-party plugin install), (b) manual `cp -r` from a clone. |
| **Hook files** | 1 hook (Python via uv, or TypeScript via bun); 1 patterns.yaml; settings JSON snippet. | 6 hooks: `protect-files.sh`, `warn-large-files.sh`, `scan-secrets.sh`, `block-dangerous-commands.sh`, `format-on-save.sh`, `session-start.sh`. All bash + jq. |
| **Commands/agents/skills/templates** | 1 skill (`prompt-injection-defender`), 2 commands (`install`, `prime`). No agents. | 9 skills (`/setupdotclaude`, `/debug-fix`, `/ship`, `/pr-review`, `/tdd`, `/explain`, `/refactor`, `/test-writer`, `/context-budget`). 5 agents (code-, security-, performance-, doc-, frontend-designer). 6 rules (security, code-quality, testing, database, error-handling, frontend). 14 plugin packagings of the same skills/agents. |

---

## 3 · Security review

### 3a · lasso-security/claude-hooks

| Question | Finding |
|---|---|
| Executes shell commands? | The hook itself does **not** — pure Python regex scan over `tool_response`. The **installer** does: `chmod +x`, `cp`, and a curl-piped uv installer. |
| Modifies `.claude/` or settings? | Yes — installer writes `.claude/hooks/prompt-injection-defender/` and `.claude/settings.local.json` (or warns to merge if exists). |
| Network calls? | Hook itself: **no**. Installer: yes — `curl https://astral.sh/uv/install.sh \| sh` if uv missing (auto-runs Astral's bootstrap script). At runtime, `uv run` may also fetch `pyyaml` to a cache. |
| Reads/logs files? | Hook reads stdin (Claude's tool_response JSON) and `patterns.yaml`. No disk logging, no telemetry, no outbound reporting. Output goes to stdout (visible to Claude only). |
| Could expose .env / secrets? | **Low risk.** The hook receives whatever Claude already sees (tool outputs). It would only relay them if Claude itself read a secret. No exfiltration channel. |
| New dependencies? | `pyyaml` (Python) or none (TypeScript via bun). `uv` if missing. |
| Uninstall? | Documented inverse: delete `.claude/hooks/prompt-injection-defender/` and remove the hook block from `settings.local.json`. Clean, manual. |

**Threat note:** The `curl \| sh` uv installer in `install.sh` is a supply-chain concern. The hook code itself is auditable and self-contained (~280 lines Python, ~600 lines patterns.yaml).

### 3b · poshan0126/dotclaude

| Question | Finding |
|---|---|
| Executes shell commands? | Yes — every hook is bash. `block-dangerous-commands.sh` parses Bash tool input and denies risky patterns. `format-on-save.sh` runs prettier/black/etc. `session-start.sh` runs `git`/`gh`. |
| Modifies `.claude/` or settings? | Yes — `/setupdotclaude` skill writes `.claude/{settings.json,rules,hooks,skills,agents}` and root `CLAUDE.md`. Marketplace install registers a plugin source. |
| Network calls? | `session-start.sh` calls `gh pr view` (only if `DOTCLAUDE_SESSION_VERBOSE=1`). Marketplace path requires Claude Code to fetch plugin contents from GitHub. Hooks themselves: no other network. |
| Reads/logs files? | Hooks read stdin (tool_input JSON) and parse with `jq`. `scan-secrets.sh` reads `tool_input.content`/`new_string` only. Nothing written to disk by hooks. |
| Could expose .env / secrets? | `protect-files.sh` actively **blocks** writes to `.env*`, `*.pem`, `*.key`, `secrets/`, `.git/`, `.claude/hooks/`. `scan-secrets.sh` blocks on AWS/GitHub/Slack/sk- keys, private-key blocks, hardcoded creds. Net effect is **defensive**, not exposing. Note: `chmod +x` on hook files happens in setup. |
| New dependencies? | `jq` (mandatory; hooks fail-closed if missing). `gh` (optional, session-start only). Formatters per language. |
| Uninstall? | No formal uninstall. Manual: `rm -rf .claude/`, undo CLAUDE.md, `/plugin uninstall` for marketplace path. |

**Threat note:** `/plugin marketplace add poshan0126/dotclaude` is a third-party plugin source running with the same trust as user-installed code. The repo is MIT-licensed, well-scoped, no obvious malicious patterns, but it is unaudited by Anthropic and could change between versions. Hook scripts are 50–150 lines each, all readable bash with sane fail-closed behavior.

---

## 4 · JAI compatibility

### 4a · lasso

| /jai surface | Overlap |
|---|---|
| `CLAUDE.md` | None. |
| `SECURITY_RULES.md` | Adds a missing category: "treat tool outputs as untrusted; scan before honoring instructions." Worth 1–2 lines. |
| `THREAT_MODEL.md` | **Direct fit.** Currently lists prompt-injection as `partial`. Lasso's 5 attack categories + severity rubric is a ready-made expansion of that row. |
| `WORKFLOW_RULES.md` | None. |
| Skills (`security_review`, `threat_modeling`) | The patterns.yaml taxonomy could become a checklist inside `threat_modeling.skill.md`. |
| **Best fit form** | **Reference + policy.** Catalog the categories in THREAT_MODEL.md. Optionally adopt the hook later as a separate, user-approved change. |

### 4b · dotclaude

| /jai surface | Overlap |
|---|---|
| `CLAUDE.md` | dotclaude's CLAUDE.md is a generic template — JAI's is project-specific and stricter. No replacement value. |
| `SECURITY_RULES.md` | dotclaude `rules/security.md` is 8 generic lines (validate input, parameterized queries, etc.). JAI's is more concrete. |
| `THREAT_MODEL.md` | None. |
| `WORKFLOW_RULES.md` | dotclaude `block-dangerous-commands.sh` would *enforce* things JAI documents (no push to main, no force push, no `rm -rf $HOME`, no `DROP TABLE`). Net new capability. |
| `secret_scanning.skill.md` | dotclaude `scan-secrets.sh` regex catalog (AKIA, ghp_, sk-, xox[bpras]-, private key blocks, conn-strings) is a useful reference set. |
| `security_review.skill.md` | dotclaude `agents/security-reviewer.md` is a well-structured prompt with severity/confidence rubric and explicit "what NOT to flag." Style reference. |
| `implementation_review.skill.md` / `code-reviewer` | Heavy overlap; dotclaude version is more generic. |
| Skills (`/ship`, `/pr-review`, `/tdd`, `/explain`, `/refactor`, `/debug-fix`) | Out-of-scope for JAI — JAI is project-context, not workflow tooling. |
| **Best fit form** | **Reference only.** Cherry-pick regex catalogs and review-prompt patterns. Reject the kit. |

---

## 5 · Risk classification

| | lasso | dotclaude |
|---|---|---|
| **Risk** | **Medium** (hook code: low; installer: medium) | **Medium** (kit code: low; install footprint: medium) |
| **Why** | Hook is small, auditable, no network, no exfil. Risk is concentrated in (a) `curl \| sh` uv bootstrap and (b) the hook running on every monitored tool call (any bug = noisy/blocked workflow). The `block` decision in PostToolUse is documented as warn-only, but is a non-obvious API contract that could change. | Six hooks running on every Edit/Write/Bash/SessionStart enlarges the surface. `jq` is a hard dependency (Windows users need to install it). `block-dangerous-commands.sh` is robust but its rule set may diverge from JAI's WORKFLOW_RULES. Marketplace install path equals trusting a third-party plugin source. |

Neither is "high" — both are MIT, single-author/org, readable, no obfuscated code, no telemetry. Both would still violate the user's standing rules ("Never change environment state without asking", "Do not modify Claude settings") if installed without explicit approval.

---

## 6 · Recommendation

| | Verdict | Rationale |
|---|---|---|
| **lasso-security/claude-hooks** | **Reference now → Adapt later (with approval)** | Solves a documented JAI gap. Step 1: lift its category taxonomy into THREAT_MODEL.md as documentation. Step 2 (separate, approved change): consider installing the Python hook locally — but only after a session where the user explicitly approves a `.claude/settings.local.json` edit and the uv bootstrap. |
| **poshan0126/dotclaude** | **Reject for adoption · Reference only** | Heavy overlap with /jai/ skills. The unique value (deterministic hooks for protect-files / scan-secrets / block-dangerous-commands) is real but small, and adopting the kit wholesale would create two parallel policy systems. Cherry-pick patterns into existing /jai/ files instead. |

---

## 7 · Extractable value (concepts only — nothing copied)

### From lasso
1. **Five-category prompt-injection taxonomy** for THREAT_MODEL.md: Instruction Override, Role-Playing/DAN, Encoding/Obfuscation, Context Manipulation, Instruction Smuggling.
2. **Severity rubric** (`high` = definite injection, `medium` = suspicious, `low` = informational/false-positive-prone) — generic enough to apply to JAI's other threat rows.
3. **"Warn, don't block" stance** — useful framing for `security_review.skill.md`: when patterns match, raise to user, don't auto-deny.
4. **Monitored-tool list** (Read, WebFetch, Bash, Grep, Task, mcp__*) — concrete inventory of where untrusted content enters Claude's context. Worth listing in SECURITY_RULES.md as "untrusted input surfaces."
5. **Specific high-value patterns**: zero-width Unicode (`​-‍﻿`), Cyrillic homoglyphs, `<!-- ignore -->` HTML comments, fake `{"role":"system"}` JSON, `[INST]`/`[/INST]` markers — concrete detection examples for the threat model.

### From dotclaude
1. **Secret regex catalog** — extend `secret_scanning.skill.md`: `AKIA[0-9A-Z]{16}` (AWS), `(ghp_|gho_|ghs_|ghr_|github_pat_)…` (GitHub), `sk-[a-zA-Z0-9]{20,}` (OpenAI/Anthropic/Stripe), `xox[bpras]-…` (Slack), `-----BEGIN … PRIVATE KEY-----`, `(mongodb|postgres|mysql|redis|amqp|smtp)://user:pass@`. Also the **env-var-aware exclusion** (`process.env`, `os.environ`, `${...}`) that suppresses false positives on legitimate code.
2. **Dangerous-command rubric** for WORKFLOW_RULES.md cross-reference: push-to-protected-branch detection (incl. `HEAD:main`, refspec forms, bare `git push` while on main), force-push (with `--force-with-lease` exemption), `rm -rf` on `/`, `~`, `$HOME`, unresolved `$VAR`, system dirs, `DROP TABLE/DATABASE/SCHEMA`, `DELETE FROM` w/o `WHERE`, `TRUNCATE`, `chmod 0?777` / `a+rwx`, `curl|sh`, `>/dev/sd*`, `mkfs`, `dd of=/dev/…`, `git reset --hard`, `git clean -f`, package publish (with `--dry-run` exemption).
3. **Protect-files allowlist+denylist pattern** for SECURITY_RULES.md: deny-by-glob in `.claude/settings.json` permissions for `**/.env`, `**/*.pem`, `**/*.key`, `**/secrets/**`, plus a hook layer for case-insensitive directory matches (`.git/`, `secrets/`, `.claude/hooks/`).
4. **`security-reviewer` agent prompt structure** as a reference for `security_review.skill.md`:
   - "≥80% confidence threshold; only flag exploitable issues"
   - Explicit "what NOT to flag" section (theoretical attacks, pre-existing issues outside diff, defense-in-depth nice-to-haves, style)
   - Categorical coverage: Injection / Auth / Authorization / Data exposure / Dependencies / Crypto / Input validation
   - Severity + Confidence + File:Line + Attack vector + Fix output template.
5. **`session-start.sh` minimalism principle** — "branch + dirty" by default, opt-in verbose. Useful framing if JAI ever adds a session-start context injector.
6. **Path-scoped rules pattern** (frontmatter `paths:` + glob) — if JAI ever wants per-area rule files, this is a clean pattern.

---

## 8 · Status & next-step gating

- **Vetting filed** as a `references/` artifact at JAI Standard v1.0 bootstrap (2026-05-08).
- **No intake item filed yet.** The §7 extractable concepts are deferred until Mitch decides which to turn into MINOR bumps for v1.1+. Likely first candidates: `catalogs/INJECTION_CATEGORIES.md` (lasso §7.1) and `catalogs/SECRET_REGEX_CATALOG.md` (dotclaude §7.1).
- **No code changes made.** Nothing was installed, no Claude settings were edited, no hook was written to any project.
