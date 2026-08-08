---
title: WorkCore–MagicAI Document Reconciliation and Source Authority
date: 2026-08-09
status: Documentation control record
workcore_current_main: 64d05e1a7a1071bf61c1f252d359fbd712da39f5
---

# WorkCore–MagicAI Document Reconciliation and Source Authority

## Why this document exists

Scans 01–14 were produced primarily from the supplied MagicAI package and an earlier consolidated WorkCore snapshot. Scans 15–20 introduced the real `Masterleeaus/workcore-extensions` package family but were initially pinned to WorkCore commit `e56389a87db869915a96b7adca1bb12237895d50`.

WorkCore `main` has since advanced to:

```text
64d05e1a7a1071bf61c1f252d359fbd712da39f5
```

through merged PR #4, **Harden WorkCore CRM replacement foundation**.

The earlier scans are not deleted. They remain evidence of the MagicAI host snapshot and earlier WorkCore implementation state. Later current-main reports supersede stale WorkCore implementation claims while preserving that audit history.

## Current source authority

| Source | Authority |
|---|---|
| Supplied MagicAI 11.00 package | Exact host code, routes, middleware, providers and marketplace runtime |
| `Masterleeaus/workcore-extensions` `main` | Exact current WorkCore extension packaging and operational source |
| Current scanned WorkCore commit `64d05e1…` | Current implementation baseline for Scans 21 onward |
| Earlier consolidated WorkCore ZIP | Historical donor and comparison source |
| Documents repository | Audit trail, revisions and integration decisions |

## Current combined authority

The authoritative combined integration sequence is now:

```text
15  combined architecture
16  six-extension MagicAI contracts
17  installation/activation/release plan
18  historical blocker register at earlier WorkCore main
19  this reconciliation/source-authority record
20  parent-extension snapshot at e56389a
21  Business Network + MagicAI scan at current main 64d05e1
```

Where Scan 21 explicitly marks a WorkCore issue resolved or superseded, Scan 21 takes precedence over the older commit-pinned statement.

## Reconciliation of earlier scans

### Scan 01 — Complete system inventory

**Retained:** MagicAI host inventory, version/framework counts, absent paid extension source, host authority boundaries and runtime residue findings.

**Revised:** WorkCore is a parent plus five MagicAI-native add-ons, not one monolithic extension. The WorkCore repository remains the implementation authority.

### Scan 02 — Bootstrap and runtime architecture

**Retained:** provider order, route/middleware boot sequence, MagicAI queue boot defect, CSRF defect and modern-versus-legacy installer analysis.

**Revised:** Marketplace must register six WorkCore providers with the parent first. The parent owns runtime autoload and migrations; add-ons activate module keys through `WorkModuleRegistry`.

### Scan 03 — Extension marketplace runtime

**Retained:** filesystem/provider presence is part of runtime authority, database state can drift from runtime state, installer integrity/rollback concerns and the modern installer is the only acceptable base.

**Revised:** the package family is six extensions and add-on manifests need effective/transitive runtime contribution metadata.

### Scan 04 — Menu and dashboard hooks

Earlier WorkCore menu design was conceptual. Current WorkCore `main` now implements:

```text
WorkCoreWorkspaceCatalogue
WorkCoreWorkspaceManifest
MagicAIMenuSynchronizer
entitlement-aware workspace routes
```

The stable native roots are:

```text
CRM
Operations
Workforce
Resources
Commercial
```

The navigation layer is now structurally implemented, while the actual CRUD/board/report workspace content remains incomplete.

### Scan 05 — Authentication and user identity

MagicAI remains authentication authority. WorkCore uses MagicAI user identity while owning operational company membership and authorization.

Current native parent now sets its MagicAI API profile to Passport-compatible `auth:api`. Durable actor identity and deletion preservation remain future work.

### Scan 06 — Teams, companies and tenancy

MagicAI Company is not the WorkCore operational tenant. The WorkCore parent owns `tz_companies`, memberships and tenant context.

Current main has additionally hardened tenant-owned reads to fail closed and introduced an audited privileged cross-company path for legitimate platform/migration work.

### Scan 07 — Roles and permissions

MagicAI roles remain platform roles; WorkCore roles remain operational roles.

**Superseded defect:** `WorkCoreAccessLevel::Manage` is no longer referenced by the current native permission resolver. Owner/admin now resolve to `WorkCoreAccessLevel::All`.

### Scan 08 — Plans, subscriptions, credits and billing

MagicAI remains SaaS plan/subscription and shared-AI-credit authority. WorkCore Commercial remains operational finance authority.

Current WorkCore main now adds seven WorkCore plan flags and implements company entitlement projection/revisioning.

**New current blocker:** the shipped WorkCore MagicAI subscription resolver defaults expect `subscriptions.status` and `starts_at`, while the supplied MagicAI 11 schema uses `stripe_status` and has no `starts_at`. The entitlement bridge must be corrected before rollout.

### Scan 09 — CRM host contract

The earlier scan treated a paid MagicAI CRM bridge as a possible final mode because the paid implementation was unavailable.

Current WorkCore source makes the final cutover policy explicit:

```text
WorkCore Business Network is the sole CRM authority after cutover.
MagicAI CRM/Sales become migration sources/read-only legacy surfaces.
Permanent dual-master synchronization is prohibited.
```

### Scan 10 — Paid CRM deep scan

Still blocked because the paid MagicAI CRM package itself was not supplied. It is no longer required to establish final WorkCore authority, but it remains useful for building the one-time importer and migration reconciliation.

### Scan 11 — Sales, estimates, invoices and payments

WorkCore Commercial remains operational Finance authority. MagicAI billing remains platform/SaaS billing.

Current Commercial status must be rescanned from current WorkCore main in Scan 22 before relying on earlier Finance defect counts.

### Scan 12 — Customer lifecycle

Business Network owns customer lifecycle, Work Operations owns service execution, Commercial owns operational money and Property Operations owns premises context.

The WorkCore repository's current replacement plan reinforces the end-to-end target:

```text
Lead → Opportunity → Quote → Customer → Premises → Work order → Invoice → Payment
```

### Scan 13 — AI providers and model routing

MagicAI remains preferred provider/model authority.

**Superseded findings:** WorkCore AI is no longer unreachable; Business Network explicitly activates `ai`. The current AI provider loads the canonical `Config/workcore_ai.php` file.

**Still open:** `NullCredentialResolver`, dedicated AI route middleware using generic `auth`, limited WorkCore provider breadth, MagicAI provider adapter, privacy/cost hardening and optional-registry filtering.

### Scan 14 — AI Chat runtime and context hooks

MagicAI AI Chat remains the user-facing conversation shell, but its chat ownership, PDF retrieval and realtime credential issues remain host blockers before WorkCore operational data is attached.

WorkCore tools/actions remain the authority for operational reads/writes.

### Scan 15 — Combined architecture

Still authoritative for the six-extension topology and MagicAI/WorkCore authority split. Its WorkCore commit pin is historical; current implementation statements should be checked against Scan 21 and later scans.

### Scan 16 — Extension-by-extension contracts

Still authoritative for package responsibilities. Current-main extension scans beginning with Scan 21 provide deeper implementation status and supersede stale defect claims.

### Scan 17 — Install, activation and release plan

Still the implementation runbook. Current-main fixes reduce some P0 work, but MagicAI queue/CSRF hardening, subscription-schema alignment, installer signing/staging and full end-to-end shell verification remain release gates.

### Scan 18 — Blockers and remediation

Treat as a historical blocker snapshot.

Resolved on current WorkCore main:

```text
undefined Manage access level
native parent Passport mismatch
arbitrary non-empty confirmation acceptance
fail-open tenant-owned reads
```

Still open or newly confirmed:

```text
MagicAI queue rewriting/boot worker
MagicAI dashboard-wide CSRF exemption
MagicAI subscription-schema mismatch
Reviews tz_contacts defect
WorkCore AI dedicated-route generic auth
Null AI credential resolver
functional workspace incompleteness
legacy CRM importer
installer signing/atomic rollback
```

### Scan 19 — This reconciliation record

This file is intentionally edited as the source-authority control document when the WorkCore repository advances materially.

### Scan 20 — Parent extension snapshot

Scan 20 remains useful evidence for the parent architecture at `e56389a…`, but several P0 findings are superseded by current main.

Resolved since Scan 20:

```text
owner/admin Manage enum failure
plain confirmation verifier
native parent API Passport mismatch
lack of WorkCore native navigation/menu synchronization
lack of plan entitlement projection
```

Still open from its host findings:

```text
MagicAI queue rewriting/boot worker
MagicAI CSRF exemption
outbox production transport
MagicAI storage/notification/AI provider adapters
full live-shell verification
```

### Scan 21 — Business Network + MagicAI

Scan 21 is the current CRM/front-office integration authority and current-main correction point for earlier WorkCore findings.

It establishes:

```text
WorkCore Business Network = final CRM authority
MagicAI CRM/Sales = one-time migration source/read-only legacy after cutover
native navigation = structurally implemented
functional CRM workspaces = still incomplete
signed confirmations = implemented
company entitlement projection = implemented but MagicAI subscription default columns are currently wrong
Reviews contact-table defect = still open
```

## Documentation rule going forward

Every future integration scan must inspect all three layers:

```text
MagicAI host implementation
+
relevant WorkCore extension implementation at current main
+
the adapter and authority boundary between them
```

When `workcore-extensions/main` advances materially:

1. Record the new commit in the next scan.
2. Identify which prior findings are fixed, regressed or superseded.
3. Update this reconciliation document and the document index.
4. Preserve older commit-pinned reports instead of deleting them.
5. Treat current-main scan conclusions as authoritative for WorkCore implementation state.

A host-only or WorkCore-only scan is incomplete unless explicitly labelled as a component audit.
