# [DRAFT] policies/THREAT_MODEL_BASELINE.md (universal threat catalog)

**Status:** [DRAFT] — sandbox prototype for operator review
**Target version:** vNEXT-draft (additive — fills a gap noted as planned in [`JAI_STANDARD.md`](file:///C:/Users/Mitch/.claude/jai-standard/JAI_STANDARD.md))
**Inheritance:** Inherits S1–S8 from [`SAFETY_RULES.md`](file:///C:/Users/Mitch/.claude/jai-standard/policies/SAFETY_RULES.md). Projects extend in `<project>/jai/THREAT_MODEL.md` with project-specific assets, boundaries, and abuse cases.
**Scope:** Universal threat catalog — applies to every JAI-conformant project. Project files extend, never replace.

## 0. Purpose

Universal threats every JAI-conformant project inherits. The catalog is intentionally lightweight — it names threat *categories* every project should consider, with pointers to detector libraries in [`catalogs/`](../catalogs/) for specific patterns. Project-specific assets (Solana RPC, BullMQ, OANDA API, etc.) and boundaries are described in `<project>/jai/THREAT_MODEL.md`.

## 1. Universal Threat Categories

### 1.1 Prompt Injection

**Description:** An attacker delivers a payload via tool output (Read, WebFetch, Bash, Grep, sub-agent results) that attempts to redirect the agent's instructions.

**Categories:** See [`catalogs/INJECTION_CATEGORIES.md`](../catalogs/INJECTION_CATEGORIES.md) for the five-category taxonomy (Instruction Override, Role-Playing/DAN, Encoding/Obfuscation, Context Manipulation, Instruction Smuggling).

**Universal mitigation:**
- Treat all tool outputs as untrusted (per [`policies/SECURITY_RULES.md §4`](./SECURITY_RULES.md))
- Agent 2 reviewer cross-checks Builder claims against tool outputs

### 1.2 Secret Leakage

**Description:** A credential, API key, token, mnemonic, or signing material is read, logged, transmitted, or committed by an agent.

**Universal mitigation:**
- S2 (no `.env` reads/writes) per [`SAFETY_RULES.md`](file:///C:/Users/Mitch/.claude/jai-standard/policies/SAFETY_RULES.md)
- Restricted-file patterns per [`policies/RESTRICTED_FILE_PATTERNS.md`](./RESTRICTED_FILE_PATTERNS.md)
- Detection patterns per [`catalogs/SECRET_REGEX_CATALOG.md`](../catalogs/SECRET_REGEX_CATALOG.md)
- Log redaction (per `SECURITY_RULES.md §2`)

### 1.3 Destructive Command Execution

**Description:** A command (Bash, shell, SQL, git) destroys state irreversibly — whether by accident, by an injected payload, or by lane misclassification.

**Universal mitigation:**
- Blocked Lane per [`JAI_AUTOMATION_LANES.md §2.5`](../JAI_AUTOMATION_LANES.md)
- Detection patterns per [`catalogs/DANGEROUS_COMMANDS.md`](../catalogs/DANGEROUS_COMMANDS.md)

### 1.4 Provider / Third-Party Service Exposure

**Description:** Code, queries, or context is transmitted to an external SaaS (LLM provider, code-search service, hosted IDE feature) without operator approval, exposing project content beyond the operator's machine.

**Universal mitigation:**
- S5 (no provider calls without approval) per [`SAFETY_RULES.md`](file:///C:/Users/Mitch/.claude/jai-standard/policies/SAFETY_RULES.md)
- Outbound HTTP forbidden under Fast/Review Lanes; requires Strict Lane per-action approval

### 1.5 Lane Downgrade Attempts

**Description:** Operator pressure (or self-pressure) to "treat this as Fast Lane" when the touched set actually triggers Strict.

**Universal mitigation:**
- Lane Determination Algorithm rejects downgrades per [`JAI_AUTOMATION_LANES.md §3`](../JAI_AUTOMATION_LANES.md)
- Orchestrator surfaces the trigger and asks for re-scope, never silent downgrade
- Borderline default = stricter lane

### 1.6 Conformance Drift

**Description:** Project surface no longer matches its declared `jai_standard_version`; lifted skills/templates have local edits not declared as exceptions; required files missing.

**Universal mitigation:**
- Drift Manifest per [`JAI_DRIFT_DETECTION.md`](../JAI_DRIFT_DETECTION.md)
- `JAI fleet sync audit` per [`JAI_FLEET_SYNC_AUDIT.md`](file:///C:/Users/Mitch/.claude/jai-standard/JAI_FLEET_SYNC_AUDIT.md) on operator-set cadence
- Strict Lane work blocked on `DRIFT_BLOCKING` or `DIVERGENT`

### 1.7 Override Weakening

**Description:** Project's `PROJECT_EXCEPTIONS.md` weakens a global rule (e.g., relaxes S2, downgrades Blocked operations).

**Universal mitigation:**
- `INVALID_WEAKENING` audit per [`JAI_OVERRIDE_CONTRACT.md §2.1`](../JAI_OVERRIDE_CONTRACT.md)
- Always blocking; project loses conformance

### 1.8 Hidden Hook Installation

**Description:** A hook gets installed into `<project>/.claude/hooks/` or `~/.claude/settings*.json` without operator-approved `JAI_HOOK_CONTRACT.md` lifecycle.

**Universal mitigation:**
- S8 (no environment changes without approval) per [`SAFETY_RULES.md`](file:///C:/Users/Mitch/.claude/jai-standard/policies/SAFETY_RULES.md)
- Hooks remain Deferred per [`JAI_AUTOMATION_LANES.md §1`](../JAI_AUTOMATION_LANES.md)
- `JAI_HOOK_CONTRACT.md` not yet drafted; no hook may be installed live until it is

### 1.9 Marketplace / Plugin Trust Boundary

**Description:** A third-party plugin is installed (e.g., dotclaude marketplace, lasso install.sh) and runs with the same trust as JAI-native code.

**Universal mitigation:**
- Marketplace plugins explicitly Rejected per [`JAI_AUTOMATION_LANES.md §1`](../JAI_AUTOMATION_LANES.md)
- Cherry-picks from external repos travel via standard Enhancement Intake → vetting → cataloging in `catalogs/`, never as direct installs

### 1.10 Restricted-File Verification Violation

**Description:** An agent (especially Agent 2 Security Reviewer) opens a restricted file to "verify" a violation found via diff/metadata.

**Universal mitigation:**
- Hard read-prohibition per [`JAI_AGENT_ARCHITECTURE.md §3.3`](file:///C:/Users/Mitch/.claude/jai-standard/JAI_AGENT_ARCHITECTURE.md): reading a restricted file to verify is itself a violation
- Verification uses diff, file metadata, timestamps, or staged-file lists only

## 2. Project Extensions

Each project's `<project>/jai/THREAT_MODEL.md` extends this file with:

- **Project-specific assets** (Solana RPC keys, BullMQ topology, Postgres data, Redis state, OANDA API quotas, etc.)
- **Project-specific trust boundaries** (which services are first-party, which are external)
- **Project-specific abuse cases** (replay attacks against signal scoring, queue flooding, provider rate-limit abuse, etc.)
- **Project-specific residuals** (known accepted risks with operator decision references)
- **Cross-references** to project's `SECURITY_RULES.md` and `WORKFLOW_RULES.md`

A project's `THREAT_MODEL.md` MUST NOT remove a universal threat from this catalog. It may add project-specific threats and assets.

## 3. Maintenance Rule

Adding a new universal threat is **MINOR**. Removing one is **MAJOR**. Updating the mitigation pointer (e.g., adding a new catalog reference) is MINOR.
