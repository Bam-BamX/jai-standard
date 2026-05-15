# [DRAFT] policies/RESTRICTED_FILE_PATTERNS.md (canonical glob list)

**Status:** [DRAFT] — sandbox prototype for operator review
**Target version:** vNEXT-draft (additive)
**Inheritance:** Inherits S1–S8 from [`SAFETY_RULES.md`](file:///C:/Users/Mitch/.claude/jai-standard/policies/SAFETY_RULES.md). Projects extend in `<project>/jai/SECURITY_RULES.md` via Augment (add patterns); never via Weaken (remove patterns).
**Scope:** Universal — applies to every JAI-conformant project.

## 0. Purpose

The canonical list of file glob patterns that no automation may read, write, edit, or transmit. This file is the single source of truth referenced by:

- [`policies/SAFETY_RULES.md`](file:///C:/Users/Mitch/.claude/jai-standard/policies/SAFETY_RULES.md) S2 (credentials)
- [`policies/SECURITY_RULES.md §1`](./SECURITY_RULES.md) (restricted files)
- [`JAI_AUTOMATION_LANES.md §2.5`](../JAI_AUTOMATION_LANES.md) (Blocked Lane file types)
- [`JAI_AGENT_ARCHITECTURE.md §3.3`](file:///C:/Users/Mitch/.claude/jai-standard/JAI_AGENT_ARCHITECTURE.md) (Agent 2 hard read-prohibition)
- Any future hook under `JAI_HOOK_CONTRACT.md`

## 1. Always-Restricted Patterns (universal — MUST NOT be read, written, edited, or transmitted by any automation)

### 1.1 Environment / Configuration Secrets

```
**/.env
**/.env.*
**/.env.local
**/.env.production
**/.env.staging
**/.env.development
**/.env.test
```

### 1.2 Credentials / Keys / Certificates

```
**/credentials
**/credentials/**
**/credentials.json
**/credentials.yml
**/credentials.yaml
**/secrets/**
**/*.pem
**/*.key
**/*.crt
**/*.cer
**/*.p12
**/*.pfx
**/*.jks
**/*.keystore
**/*.truststore
**/id_rsa
**/id_rsa.*
**/id_ed25519
**/id_ed25519.*
**/id_ecdsa
**/id_dsa
**/known_hosts
```

### 1.3 Cryptocurrency / Signing Material

```
**/mnemonic
**/mnemonic.*
**/seed-phrase
**/seed-phrase.*
**/wallet.dat
**/keystore.json
**/*.wallet
```

### 1.4 Package Manager Tokens / Auth

```
**/.npmrc
**/.pypirc
**/.cargo/credentials
**/.cargo/credentials.toml
**/auth.json
**/.docker/config.json
**/.kube/config
**/.aws/credentials
**/.gcp/credentials.json
**/google-credentials.json
**/service-account*.json
```

### 1.5 Operating-System / Browser Secret Stores

```
**/Keychains/**
**/Cookies
**/Login Data
**/Local Storage
```

(These are unlikely to appear in a project repo; included for completeness for the unlikely case an agent walks an unintended directory.)

### 1.6 Live Runtime Data Files (read-only, never written by automation)

```
**/pgdata/**
**/redisdata/**
**/postgres-data/**
**/redis-data/**
**/var/lib/**
**/data/live/**
```

(Project-specific runtime data paths SHOULD be added by each project via Augment in `<project>/jai/SECURITY_RULES.md`.)

## 2. Project Extensions

Each project Augments this list in `<project>/jai/SECURITY_RULES.md`:

- Add project-specific paths that map to runtime state (e.g., a project-specific Docker volume name)
- Add project-specific credential file names (e.g., `**/oanda-api-key`)
- Add project-specific signing materials

Removing any pattern from this list requires a MAJOR version bump (per [`JAI_OVERRIDE_CONTRACT.md §2.1`](../JAI_OVERRIDE_CONTRACT.md) — Weaken is forbidden by default).

## 3. Verification Rules

Per [`JAI_AGENT_ARCHITECTURE.md §3.3`](file:///C:/Users/Mitch/.claude/jai-standard/JAI_AGENT_ARCHITECTURE.md):

- If a restricted file appears in a diff, staged-file list, or commit metadata: flag the violation **directly from metadata**; do not open the file.
- Reading a restricted file to "verify" the violation is itself a violation.
- Verification uses diff, file metadata, file timestamps, staged-file lists, or other permitted evidence — never file contents.

## 4. Maintenance Rule

Adding a new pattern is **MINOR**. Removing a pattern is **MAJOR** (forces fleet re-conformance audit). Reorganizing patterns into a new category is **MINOR** (no semantic change).
