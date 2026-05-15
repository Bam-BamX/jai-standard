# [DRAFT] catalogs/SECRET_REGEX_CATALOG.md

**Status:** [DRAFT] — sandbox prototype for operator review
**Target version:** vNEXT-draft (additive)
**Source:** Concepts cherry-picked from dotclaude `scan-secrets.sh` per 2026-05-08 vetting at [`references/repo-vetting-lasso-vs-dotclaude-2026-05-08.md`](file:///C:/Users/Mitch/.claude/jai-standard/references/repo-vetting-lasso-vs-dotclaude-2026-05-08.md). Wording owned by JAI; no code copied.
**Referenced by:** `skills/secret_scanning.skill.md`, Agent 2 reviewer security checklist per [`JAI_RUNTIME_ADVISORY_REVIEWER.md §8`](file:///C:/Users/Mitch/.claude/jai-standard/JAI_RUNTIME_ADVISORY_REVIEWER.md), any future hook under `JAI_HOOK_CONTRACT.md`.
**Scope:** Universal detection patterns. Project Augments via `<project>/jai/SECURITY_RULES.md`.

## 0. Purpose

Canonical regex catalog for high-confidence secret detection. Used by skills, reviewers, and any future hook to flag credentials before they reach a commit, log, or external transmission.

## 1. Categories

### 1.1 Cloud Provider Access Keys

| Service | Pattern | Confidence |
|---|---|---|
| AWS Access Key ID | `AKIA[0-9A-Z]{16}` | High |
| AWS Secret Access Key (heuristic) | `(?:aws[_-]?secret[_-]?access[_-]?key\|aws[_-]?secret)\s*[:=]\s*['"]?[A-Za-z0-9/+=]{40}['"]?` | Medium (heuristic) |
| GCP Service Account Key (JSON heuristic) | `"type"\s*:\s*"service_account"` paired with `"private_key"\s*:\s*"-----BEGIN PRIVATE KEY-----` | High |
| Azure Storage Account Key (heuristic) | `(?:DefaultEndpointsProtocol=https;AccountName=[^;]+;AccountKey=)[A-Za-z0-9+/=]{88}` | High |

### 1.2 Code-Hosting Tokens

| Service | Pattern | Confidence |
|---|---|---|
| GitHub PAT (classic) | `ghp_[A-Za-z0-9]{36,255}` | High |
| GitHub OAuth | `gho_[A-Za-z0-9]{36,255}` | High |
| GitHub Server-to-Server | `ghs_[A-Za-z0-9]{36,255}` | High |
| GitHub Refresh | `ghr_[A-Za-z0-9]{36,255}` | High |
| GitHub PAT (fine-grained) | `github_pat_[A-Za-z0-9_]{82,}` | High |
| GitLab PAT | `glpat-[A-Za-z0-9_-]{20,}` | High |

### 1.3 LLM Provider / SaaS API Keys

| Service | Pattern | Confidence |
|---|---|---|
| OpenAI / Anthropic / Stripe `sk-` family | `sk-(?:proj-)?[A-Za-z0-9]{20,}` | High |
| Slack token | `xox[bpras]-[A-Za-z0-9-]{10,}` | High |
| SendGrid | `SG\.[A-Za-z0-9_-]{22}\.[A-Za-z0-9_-]{43}` | High |
| Twilio Account SID | `AC[a-f0-9]{32}` | Medium (also valid public ID) |
| Mailgun | `key-[a-f0-9]{32}` | High |

### 1.4 Private Key Blocks

| Format | Pattern | Confidence |
|---|---|---|
| RSA / OpenSSH / EC / DSA | `-----BEGIN (?:RSA \|OPENSSH \|EC \|DSA )?PRIVATE KEY-----` | Very High |
| PEM Certificate (often paired with key) | `-----BEGIN CERTIFICATE-----` | Medium (alone; high if adjacent to private key) |

### 1.5 Connection Strings with Embedded Credentials

| Type | Pattern | Confidence |
|---|---|---|
| Generic protocol with user:pass | `(?:mongodb\|postgres\|postgresql\|mysql\|redis\|amqp\|smtp\|ftp)://[^/\s:]+:[^/\s@]+@[^/\s]+` | High |
| JDBC | `jdbc:[^/]+://[^/]+:[^/]+@` | High |

### 1.6 Hardcoded Credential Assignments (heuristic)

```regex
(?im)^[\s]*(?:password|secret|token|api[_-]?key|access[_-]?key|private[_-]?key)\s*[:=]\s*['"][^'"\s]{8,}['"]
```

**Confidence:** Medium. Frequent false-positive source.

### 1.7 Cryptocurrency Mnemonics (heuristic)

12 or 24 lowercase English words separated by single spaces, where words come from BIP-39 wordlist:

```regex
\b(?:[a-z]{3,8}\s+){11}[a-z]{3,8}\b   # 12-word
\b(?:[a-z]{3,8}\s+){23}[a-z]{3,8}\b   # 24-word
```

**Confidence:** Low without BIP-39 word-list cross-check; Medium with wordlist match. Heuristic; project may add wordlist validation.

## 2. False-Positive Mitigations (env-var-aware exclusions)

Skip these contexts — they reference rather than embed secrets:

```regex
process\.env\.[A-Z_][A-Z0-9_]*               # Node.js
os\.environ\[['"]?[A-Z_][A-Z0-9_]*['"]?\]   # Python
\$env:[A-Z_][A-Z0-9_]*                       # PowerShell
\$\{[A-Z_][A-Z0-9_]*\}                       # Shell variable expansion
\$\{\{\s*secrets\.[A-Z_][A-Z0-9_]*\s*\}\}    # GitHub Actions
\{\{\s*\.Values\.[a-zA-Z_][a-zA-Z0-9_]*\s*\}\}  # Helm
```

Skip lines containing comment markers naming the value as test/example:

```regex
//\s*(?:example|test|fake|fixture|placeholder)\s*$
#\s*(?:example|test|fake|fixture|placeholder)\s*$
```

Skip files matching:

```
**/*.test.*
**/*.spec.*
**/test/**
**/tests/**
**/__tests__/**
**/fixtures/**
**/examples/**
**/.env.example
**/.env.sample
```

## 3. Severity Rubric

| Severity | When to use |
|---|---|
| Critical | High-confidence pattern (Cloud key, Code-hosting token, LLM API key, Private key block, Connection string with creds) in a non-excluded path |
| High | Same patterns in test/fixture path BUT the value looks real (entropy check, not obvious fake like `sk-test-...`) |
| Medium | Heuristic pattern (hardcoded credential assignment, mnemonic without wordlist confirmation) |
| Low / Info | Pattern matched but exclusion applied; reported only at verbose level |

## 4. Action by Lane (referenced from `JAI_AUTOMATION_LANES.md`)

| Lane | Action on detection |
|---|---|
| Manual | N/A (operator's responsibility) |
| Fast | Halt batch; surface to operator with file path, line number, severity, suggested fix; do not commit until resolved |
| Review | Same as Fast; Agent 2 includes finding in reviewer report; routes to operator gate |
| Strict | Same as Review; in addition, Agent 2 may issue `Hold — secret detected` per [`JAI_RUNTIME_ADVISORY_REVIEWER.md §11.8`](file:///C:/Users/Mitch/.claude/jai-standard/JAI_RUNTIME_ADVISORY_REVIEWER.md) |
| Blocked | Always; no commit, no push regardless of approval claim |

## 5. Maintenance Rule

Adding a new pattern is **MINOR**. Removing a pattern is **MAJOR** (loosens detection). Adding a false-positive exclusion is **MINOR** if narrow; **MAJOR** if it suppresses a whole category.

## 6. Re-Vetting

Catalog re-vetted whenever a new high-impact secret format emerges (e.g., a new GitHub token prefix, a new cloud-provider key format). Re-vetting fires the standard Enhancement Intake workflow.

Last vetting: 2026-05-08 (dotclaude `scan-secrets.sh` review).
