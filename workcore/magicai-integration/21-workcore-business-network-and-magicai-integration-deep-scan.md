---
title: "WorkCore Business Network and MagicAI 11 — Combined Integration Deep Scan"
scan_number: 21
date: "2026-08-09"
status: "Completed"
magicai_source: "MagicAI 11.00 supplied server package"
workcore_repository: "Masterleeaus/workcore-extensions"
workcore_ref: "main"
workcore_commit: "64d05e1a7a1071bf61c1f252d359fbd712da39f5"
repository_target: "Masterleeaus/Documents"
repository_path: "workcore/magicai-integration/21-workcore-business-network-and-magicai-integration-deep-scan.md"
supersedes_workcore_ref: "e56389a87db869915a96b7adca1bb12237895d50"
---

# Scan 21 — WorkCore Business Network + MagicAI 11 Integration

## Executive conclusion

`WorkCoreBusinessNetwork` is now the correct front-office business-data authority for Titan Zero inside MagicAI.

At current WorkCore `main` commit `64d05e1a7a1071bf61c1f252d359fbd712da39f5`, the Business Network add-on activates ten parent-owned runtime modules:

```text
crm
catalogue
support
knowledge
reviews
territories
intelligence
expansion
wizards
ai
```

The canonical Business Network package contains **448 tracked files**.

The integration direction is now explicit in the WorkCore repository: WorkCore replaces MagicAI CRM and Sales operational record authority, while MagicAI remains the host for authentication, SaaS subscriptions, AI providers, AI Chat, AI Agents, Marketplace, themes and platform administration.

After cutover:

```text
MagicAI CRM/Sales
    → migration source / legacy read-only system
    → not a permanent second authority

WorkCore Business Network
    → customers
    → contacts
    → leads
    → opportunities
    → pipelines
    → CRM activities
    → support
    → knowledge
    → reviews
    → territories

WorkCore Commercial
    → quotes
    → invoices
    → payments
    → receivables

MagicAI
    → authentication
    → SaaS plans
    → AI provider execution
    → AI Chat / AI Agent UX
    → Marketplace and theme shell
```

## Repository evolution since Scan 20

WorkCore `main` advanced from `e56389a87db869915a96b7adca1bb12237895d50` to `64d05e1a7a1071bf61c1f252d359fbd712da39f5` through merged PR #4, **Harden WorkCore CRM replacement foundation**.

That merge resolves several blockers recorded in Scans 13, 18 and 20:

- Invalid `WorkCoreAccessLevel::Manage` → fixed to `All`.
- Native MagicAI API auth → now defaults to `auth:api`.
- Arbitrary confirmation strings → replaced by signed, payload-bound, expiring one-time grants.
- Confirmation consumption → moved inside the business transaction.
- Missing-tenant reads → hardened to fail closed.
- Company entitlement projection → implemented.
- MagicAI plan feature flags → implemented.
- Entitlement refresh and revisioning → implemented.
- Native WorkCore navigation/workspace shell → implemented.
- WorkCore AI module reachability → Business Network explicitly loads `ai`.

Those older reports must therefore be read with the current-main revision register produced with this scan.

---

# 1. Native extension contract

```text
Folder: WorkCoreBusinessNetwork
Manifest key: workcore-business-network
MagicAI provider-map key: workcore_business_network
Provider: App\Extensions\WorkCoreBusinessNetwork\System\WorkCoreBusinessNetworkServiceProvider
Version: 0.1.1
Parent: WorkCore ^0.1
Compatibility: MagicAI >= 11.0, Laravel ^10.0, PHP ^8.2
```

The add-on owns activation only. It owns no duplicate migrations or operational tables and preserves parent-owned data on disable/uninstall.

Its manifest exposes:

```text
workcore.crm
workcore.catalogue
workcore.support
workcore.knowledge
workcore.reviews
workcore.territories
workcore.intelligence
workcore.expansion
workcore.wizards
workcore.ai
```

The provider loads exactly those ten runtime keys.

## Manifest transparency gap

The add-on wrapper still declares empty permissions, routes, queues, schedules, webhooks and database ownership even though the activated modules contribute all of those effective behaviours.

Release tooling should generate transitive fields such as:

```text
effective_capabilities
effective_permissions
effective_routes
effective_actions
effective_read_models
effective_ai_tools
effective_tables
effective_schedules
effective_queues
effective_external_hosts
```

Marketplace preflight should evaluate the effective runtime rather than only the small activation wrapper.

---

# 2. CRM authority and replacement model

The current WorkCore upgrade plan makes the final rule explicit:

```text
WorkCore is the only CRM authority after cutover.
MagicAI CRM and Sales must not remain parallel record owners.
Existing MagicAI CRM data may be imported once.
Permanent two-way synchronization is prohibited.
```

Final identity mapping:

```text
MagicAI User       = platform login/account identity
WorkCore Company   = operating tenant business
WorkCore Customer  = customer organisation or individual
WorkCore Contact   = person associated with a customer/prospect
WorkCore Lead      = unconverted prospect
WorkCore Opportunity = qualified commercial opportunity
```

A MagicAI CRM Company migrates to a **WorkCore Customer**, never a WorkCore tenant Company.

---

# 3. CRM governed actions and reads

`WorkCRMServiceProvider` registers **13 governed writes**:

```text
workcore.customer.create
workcore.contact.create
workcore.contact.set_primary
workcore.lead.create
workcore.lead.update
workcore.lead.convert
workcore.sales_pipeline.create
workcore.opportunity.create
workcore.opportunity.update
workcore.opportunity.move
workcore.opportunity.mark_won
workcore.opportunity.mark_lost
workcore.crm_activity.create
```

It registers **7 read models**:

```text
workcore.customer.search
workcore.lead.search
workcore.sales_pipeline.list
workcore.sales_pipeline.board
workcore.opportunity.search
workcore.crm.forecast
workcore.crm_activity.list
```

This is the correct integration boundary for MagicAI dashboards, AI Agent, Chatbot/channel capture and importers. External code should not update WorkCore CRM tables directly.

The CRM API contains **22 route declarations** across customers, contacts, leads, pipelines, opportunities and CRM activities. Routes are loaded only when the CRM route feature is enabled.

The native parent now establishes Passport-compatible `auth:api`; module-specific route profiles still require real-shell verification so no nested module preserves a stale Sanctum assumption.

---

# 4. CRM parity still incomplete

The governed foundation is substantial but full replacement parity is not finished.

Customers/contacts still need mature support for individual vs organisation records, contact roles, communication preferences, tags, segments, files, duplicate merge, retention and anonymisation.

Leads still need richer qualification/scoring, ownership and territory workflows, follow-up automation, status history, robust duplicate handling and conversion rollback policy.

Opportunities still need stale detection, stronger won/lost taxonomy, opportunity-to-quote conversion, opportunity-to-work-order conversion and immutable accepted-commercial-scope linkage.

The source therefore supports **real CRM foundations**, not yet a production-complete MagicAI CRM replacement UI.

---

# 5. CRM AI tools

`CRMToolRegistry` exposes **12 tools**.

Reads:

```text
crm_search_customers
crm_search_leads
crm_list_sales_pipelines
crm_view_pipeline_board
crm_search_opportunities
crm_get_forecast
crm_list_activities
```

Writes:

```text
crm_create_customer
crm_create_lead
crm_create_opportunity
crm_move_opportunity
crm_record_activity
```

The registry deliberately does **not** expose lead conversion, contact creation, primary-contact mutation, pipeline creation, generic opportunity update or won/lost closure as agent tools. That is a sensible conservative autonomy boundary.

MagicAI CRM Assistant/AI Agent should therefore become a presentation/orchestration layer over these WorkCore tools rather than another CRM datastore.

---

# 6. Catalogue

The Catalogue provider registers governed writes:

```text
workcore.service_category.create
workcore.service.create
workcore.job_template.create
```

Read models:

```text
workcore.service.search
workcore.service_variant.list
workcore.service_faq.list
```

Catalogue AI currently exposes only three read tools:

```text
catalogue_search_services
catalogue_list_service_variants
catalogue_list_service_faqs
```

WorkCore Catalogue should become the operational service catalogue for quotes, job templates, booking, chatbot service answers and AI-generated scope. MagicAI should not keep a second operational service catalogue.

---

# 7. Support

Support registers governed actions for queue creation, ticket creation, messaging, assignment, status changes and ticket-to-work-order conversion, plus a ticket-search read model.

Its AI registry exposes:

```text
support_search_tickets
support_create_ticket
support_add_message
support_assign_ticket
support_change_status
```

It deliberately does not expose support-ticket → work-order conversion to AI.

MagicAI email/chatbot/WhatsApp/Messenger/Telegram/voice can become intake and delivery channels, while WorkCore remains authoritative for ticket identity, state, assignment, customer linkage and conversion to operational work.

---

# 8. Knowledge

Knowledge registers governed category/article creation, article update, publication-state changes, article creation from support tickets and feedback, with article search as a read model.

Its AI registry exposes:

```text
knowledge_search_articles
knowledge_create_article
knowledge_update_article
knowledge_change_article_status
```

MagicAI can render/summarize knowledge, but the WorkCore article and publication lifecycle remains authoritative.

---

# 9. Reviews and rebooking

Reviews registers:

```text
workcore.review.request.create
workcore.review.record
workcore.review.publish
workcore.service_recovery.create
workcore.rebooking.request
```

with `workcore.review.search` as its read model.

The Reviews AI registry currently exposes no tools, which is a conservative default.

## Confirmed current defect

`EloquentReviewRepository::refs()` still resolves contacts through:

```text
tz_contacts
```

but the WorkCore CRM table is:

```text
tz_customer_contacts
```

Contact-linked review workflows can therefore fail. This remains an immediate code fix plus regression-test requirement.

---

# 10. Territories

Territories provides governed region, district, branch and territory creation/assignment plus territory search and geographic matching. It includes polygon/centroid/point-in-polygon logic.

Its AI registry is currently empty.

MagicAI Maps or other mapping providers should remain visualization/geocoding adapters; territory ownership, assignment and eligibility stay in WorkCore.

Safe future AI exposure should start read-only, for example territory search/match, rather than territory mutation.

---

# 11. Intelligence

Business Network activates a substantial intelligence kernel covering:

- secret scanning
- Australian identifier detection
- content safety
- structured proposal schema validation
- evidence chains
- observations and recommendations
- knowledge ingestion and chunking
- lexical relevance and reciprocal-rank fusion
- freshness and retention policy
- domain intelligence context
- CRM, Operations, Finance, Workforce, Premises/assets, Compliance/quality and Supply adapters

Intelligence should produce evidence, observations, recommendations and bounded proposals. It must never become a raw write bypass.

---

# 12. Expansion

Expansion supports capability-gap analysis, adaptive domains, adaptive features, adaptive records, simulation, compatibility validation, versioning, rollback and kill switches.

The WorkCore capability metadata explicitly declares runtime code generation disabled. High-risk activation/rollback operations remain governed and approval-protected.

This is a bounded/declarative expansion system, not an AI system that writes arbitrary executable PHP into production.

---

# 13. Wizards

Wizards registers **8 governed writes** for definition design/publication and run lifecycle, plus **3 read models** for list/run/next-question.

Its AI registry exposes only:

```text
wizards_list_definitions
wizards_get_run
wizards_next_question
wizards_start_run
wizards_save_answer
```

Higher-risk publish, approval and completion remain outside general AI tool exposure.

---

# 14. WorkCore native AI is now reachable

Scan 13's old finding that the WorkCore AI module was defined but never loaded is now superseded. Business Network explicitly activates `ai`.

The AI provider now binds model/profile resolution, tool catalogue, rate limits, budgets, circuit breaker, conversations, memory, approvals, agent profiles, orchestration runs and the agent orchestrator.

Remaining blockers:

```text
credential_resolver = NullCredentialResolver
AI orchestration middleware still uses generic auth
provider runtime remains primarily OpenAI-compatible/local
MagicAI broad provider/entity adapter is not implemented
```

The preferred architecture remains:

```text
WorkCore = company policy + tools + approval + memory + audit
MagicAI  = provider/model execution
Bridge   = MagicAIEntityProvider / credential adapter
```

The AI configuration also lists tool registries across optional add-ons, so catalogue construction must safely filter by class existence, loaded module, entitlement and actor permission.

---

# 15. P0 security improvements now merged

Current `main` fixes several old findings.

## Permission

Owners/admins now resolve to:

```text
WorkCoreAccessLevel::All
```

instead of the invalid `Manage` case.

## Confirmation

The native WorkCore parent now installs a signed confirmation system that binds:

```text
company
actor
action
payload hash
idempotency key
expiry
one-time nonce
```

The nonce is consumed inside the action transaction, and legacy plain confirmation tokens are disabled for the native MagicAI package.

## Tenancy

Tenant-owned model reads now fail closed without active tenant context. A separate audited privileged cross-company context is provided for legitimate platform/migration operations.

These old Scan 18/20 P0 findings are therefore resolved on current WorkCore `main`.

---

# 16. Native MagicAI plan entitlements

The parent now adds seven WorkCore flags to MagicAI plans:

```text
ext_workcore_core
ext_workcore_operations
ext_workcore_workforce
ext_workcore_resources
ext_workcore_commercial
ext_workcore_offline
ext_workcore_ai_actions
```

All default false.

Business Network ordinary capabilities map mainly from `ext_workcore_core`:

```text
workcore.crm
workcore.catalogue
workcore.support
workcore.knowledge
workcore.reviews
workcore.territories
```

AI-heavy capabilities map from `ext_workcore_ai_actions`:

```text
workcore.ai
workcore.intelligence
workcore.wizards
workcore.expansion
```

This is a useful separation between ordinary front-office operation and AI/action-heavy features.

---

# 17. Critical MagicAI subscription-schema mismatch

The current native WorkCore default subscription mapping expects:

```text
subscriptions.status
subscriptions.starts_at
subscriptions.ends_at
```

The supplied MagicAI 11 migration actually defines:

```text
subscriptions.stripe_status
subscriptions.trial_ends_at
subscriptions.ends_at
subscriptions.created_at
subscriptions.updated_at
```

There is no `status` column and no `starts_at` column.

`MagicAIEffectiveSubscriptionResolver` directly uses the configured status identifier in `whereIn()` and reads configured validity columns.

## Consequence

With the shipped defaults against the supplied MagicAI 11 schema, entitlement reconciliation can issue SQL against nonexistent columns and prevent:

- WorkCore capability projection
- menu visibility
- route capability checks
- governed actions
- AI tool availability

## Required fix

The MagicAI-native profile must ship actual MagicAI 11 column mappings, at minimum:

```text
status_column = stripe_status
```

Valid-from should either use a real host column or become explicitly optional; it must not default to a nonexistent `starts_at`.

A database-backed MagicAI subscription-shape fixture is mandatory.

---

# 18. Subscription ownership limitation

The entitlement resolver currently derives company subscription through:

```text
tz_companies.owner_user_id
→ MagicAI subscriptions.user_id
```

This is workable for an initial single-owner product but needs explicit policy for ownership transfer, team billing, reseller billing, one owner controlling several companies and subscriptions that cover multiple companies.

Long term, use an explicit billing-account/company link rather than owner identity alone.

---

# 19. Entitlement reconciliation

The parent now includes:

```text
EffectiveSubscriptionSnapshot
MagicAIEffectiveSubscriptionResolver
WorkCorePlanEntitlementAdapter
ProjectedCompanyEntitlementResolver
CompanyEntitlementRefreshService
workcore:refresh-entitlements
```

Scheduled reconciliation uses `withoutOverlapping()` and `onOneServer()`.

This is stronger than the earlier host-only entitlement proposal, but it assumes MagicAI's scheduler/queues run normally. The host's boot-time queue-worker/rewrite defect must still be removed.

---

# 20. Native MagicAI navigation is now structurally implemented

The parent defines five stable workspaces:

```text
CRM
Operations
Workforce
Resources
Commercial
```

Business Network contributes directly to:

```text
CRM: Customers, Contacts, Leads, Pipelines, Opportunities, Activities, Reports
Resources: Knowledge Base
Operations: Map and Territories
Commercial: Service Catalogue
```

`MagicAIMenuSynchronizer` upserts WorkCore menus into MagicAI, preserves administrator-controlled order and enabled state on existing rows, disables retired WorkCore keys and asks MagicAI `MenuService::regenerate()` to rebuild host menus.

`WorkCoreWorkspaceManifest` only exposes sections when the capability exists and the current company entitlement allows it. It also carries an entitlement revision.

This supersedes the old finding that there was no WorkCore menu adapter.

---

# 21. Workspace UI is still only a shell

The current WorkCore Blade workspace renders navigation, descriptions, company context, capability labels and a **foundation ready** notice.

It does not yet implement full customer/contact/lead tables, pipeline Kanban, opportunity detail, activity timeline, filters, forms, bulk operations or CRM reports.

Accurate state:

```text
navigation: implemented
route gating: implemented
entitlement visibility: implemented
functional CRM workspace UI: incomplete
```

---

# 22. Legacy MagicAI CRM/Sales cutover

MagicAI core still defines legacy CRM/Sales roots and menu contracts. After WorkCore parity and migration:

1. install/synchronize WorkCore menus
2. complete WorkCore workspaces
3. migrate legacy CRM/Sales data
4. put legacy CRM/Sales into read-only mode
5. disable legacy menu roots
6. redirect compatible URLs
7. preserve rollback window
8. remove legacy providers later
9. remove legacy schema only in a separately approved release

Do not expose two operational CRMs indefinitely.

Generic MagicAI CRM concepts map to operational WorkCore concepts:

| MagicAI CRM | WorkCore |
|---|---|
| Project | Work order, contract or recurring service |
| Task | Work-order task/checklist/corrective action |
| Milestone | Work-order/service stage |
| Calendar event | Appointment |
| Assignment | Dispatch assignment |
| Timesheet | Time entry/attendance evidence |
| Project file | Work-order/premises/customer document |
| Discussion | Activity timeline/operational conversation |

---

# 23. One-time CRM importer

The required cutover mapping is:

```text
MagicAI CRM Company → WorkCore Customer
MagicAI Contact → WorkCore Contact
MagicAI Lead → WorkCore Lead
MagicAI Deal → WorkCore Opportunity
MagicAI Pipeline → WorkCore Sales Pipeline
MagicAI Activity → WorkCore CRM Activity
MagicAI Project → WorkCore Work Order/Contract
MagicAI Task → WorkCore Task/Activity
MagicAI Event → WorkCore Appointment
MagicAI Product → WorkCore Catalogue Item
MagicAI Proposal/Estimate → WorkCore Quote
MagicAI Invoice → WorkCore Invoice
MagicAI Payment → WorkCore Payment Allocation
```

Current status: **not implemented**.

Every imported source record must have durable mapping/checksum state and end as imported, deliberately skipped or reconciliation-required. Permanent dual-master synchronization is prohibited.

---

# 24. Contact capture

MagicAI's core `ContactCapturedEvent` remains too weak for WorkCore because it lacks WorkCore company, event ID, external source record ID, idempotency, consent and correlation information and is not dispatched by core.

Final channel integration should use a versioned company-scoped capture envelope, for example:

```text
business.contact.captured.v2
→ WorkCore company
→ source identity
→ consent/preferences
→ idempotency
→ WorkCore lead/customer resolution
→ governed action
```

---

# 25. MagicAI AI Chat and AI Agent integration

MagicAI should remain the user-facing AI shell after Scan 14's chat-security issues are fixed.

Business Network contributes authoritative CRM, Catalogue, Support, Knowledge and Wizard tools plus Intelligence context.

Execution path:

```text
MagicAI Chat/AI Agent
→ WorkCore tool catalogue
→ NativeAiAccessGate
→ company entitlement
→ actor permission
→ WorkCore read model or governed action
→ signed confirmation when required
→ audit/event
→ structured result
→ MagicAI presentation
```

Do not install a permanent second `ai-agent-tool-crm` authority. Prefer an `ai-agent-tool-workcore`/host adapter whose writes terminate in the WorkCore dispatcher.

---

# 26. Authority matrix

| Capability | MagicAI | WorkCore Business Network | Final authority |
|---|---|---|---|
| Login/user identity | Yes | consumes identity | MagicAI |
| SaaS subscription | Yes | projects entitlement | MagicAI |
| CRM customer/contact | legacy/optional | yes | WorkCore |
| Lead/pipeline/opportunity | legacy/optional | yes | WorkCore |
| CRM activities | legacy/optional | yes | WorkCore |
| Service catalogue | donor/UI only | yes | WorkCore |
| Support channels | capture/delivery | ticket authority | WorkCore |
| Knowledge rendering | AI/UI | article authority | WorkCore |
| Reviews/rebooking | delivery channels | lifecycle authority | WorkCore |
| Territories/maps | visualization/geocoding | territory authority | WorkCore |
| Intelligence evidence | model inference | governed evidence/recommendation | WorkCore |
| Wizards | host presentation | run/approval authority | WorkCore |
| AI model/provider | yes | policy/orchestration | MagicAI provider layer |
| AI business tools | invokes | governed implementation | WorkCore |

---

# 27. Current blockers

## Critical

1. MagicAI subscription resolver defaults do not match supplied MagicAI 11 subscription schema (`status` vs `stripe_status`; nonexistent `starts_at`).
2. MagicAI host queue boot logic still rewrites queue names and runs a worker during request boot.
3. MagicAI dashboard-wide CSRF exemption remains unsafe.
4. Reviews still reference nonexistent `tz_contacts` instead of `tz_customer_contacts`.

## High

5. WorkCore native AI still uses `NullCredentialResolver`.
6. Dedicated WorkCore AI route middleware still uses generic `auth` rather than explicit Passport.
7. WorkCore AI provider execution is not yet adapted to MagicAI's broad model/entity catalogue.
8. Functional CRM workspace UI is incomplete.
9. One-time CRM importer is not implemented.
10. Complete customer/contact duplicate, merge, preferences and retention lifecycle is unfinished.
11. Reviews/Territories AI registries are empty.
12. Effective add-on runtime contributions are absent from the manifest.
13. Full multi-company MagicAI end-to-end CRM workflow is not yet proven.
14. MagicAI AI Chat ownership/document/realtime-key vulnerabilities from Scan 14 must be fixed before WorkCore data is attached.

## Medium

15. Explicit billing-account/company mapping should replace owner-user subscription assumption.
16. MagicAI menu cache must react correctly to company/entitlement revision changes.
17. Contact capture v2 is not implemented.
18. CRM reporting/workspace components are structural only.
19. Legacy URL redirect/read-only cutover is not implemented.
20. Communication consent/preferences require completion.

---

# 28. Required implementation work

## WorkCore

1. Fix MagicAI subscription-schema defaults.
2. Add real MagicAI subscription fixture/integration tests.
3. Fix `tz_contacts` reference.
4. Finish CRM customer/contact lifecycle.
5. Finish opportunity → quote/work conversion.
6. Build functional workspace components.
7. Build one-time CRM importer.
8. Bind MagicAI AI provider/credential adapter.
9. Make native AI route middleware Passport-aware.
10. Filter optional AI tool registries safely.
11. Generate effective/transitive manifests.
12. Add read-only Reviews/Territories AI tools only when needed.

## MagicAI

13. Remove queue rewriting and boot-time worker.
14. Restore CSRF.
15. Harden AI Chat ownership, document retrieval and realtime credential handling.
16. Register the WorkCore provider family.
17. Run WorkCore menu synchronization.
18. Configure and verify WorkCore entitlement projection.
19. Disable legacy CRM/Sales only after parity and migration.
20. Provide notification/channel adapters.
21. Provide private storage/provider adapters.
22. Expose filtered WorkCore tools to AI Agent.

---

# 29. Mandatory end-to-end tests

## Entitlement

- Real MagicAI 11 subscription resolves successfully.
- `stripe_status` maps correctly.
- Trial/lifetime/expired cases behave correctly.
- Plan feature change increments entitlement revision.
- Disabled `ext_workcore_core` hides CRM and denies actions.
- Disabled `ext_workcore_ai_actions` removes AI/intelligence/wizard capabilities.

## CRM

- Create customer/contact/lead/opportunity.
- Convert lead exactly once.
- Move opportunity.
- Mark won only with a signed grant.
- Record activity.
- Reject cross-company IDs.
- Replay through stable idempotency key.
- Owner/admin permission path uses valid access level.

## MagicAI UI

- Menu sync creates CRM workspace.
- Existing admin custom order is preserved.
- Entitlement removal hides sections.
- Company switch changes workspace manifest.
- No cross-company data appears.
- Legacy CRM root can be disabled cleanly.

## AI Agent

- Reads are company scoped.
- Non-exposed won/lost transitions are unavailable.
- Mutations enforce WorkCore permission.
- High-risk tools require signed approval.
- Tool results produce WorkCore audit/events.
- Missing optional package registries do not break AI boot.
- No provider credential reaches browser.

## Reviews

- Contact-linked review resolves `tz_customer_contacts`.
- Review publication requires approval.
- Service recovery/rebooking remain tenant scoped.

## Migration

- Legacy CRM Company imports as WorkCore Customer.
- Tenant Company is never imported as Customer.
- External mapping is idempotent.
- Financial totals reconcile.
- Rerun is safe.
- Ambiguous records enter reconciliation.

---

# 30. Revision impact on earlier scans

## Scan 13

Superseded:

```text
WorkCore AI module is defined but never loaded.
WorkCore AI config path is broken.
```

Current:

```text
Business Network explicitly loads ai.
The current AI provider resolves the canonical WorkCore Config file.
```

Still open: NullCredentialResolver, generic AI-route auth, limited provider breadth, MagicAI provider adapter and privacy/cost hardening.

## Scan 18

Resolved on current main:

```text
undefined Manage
native WorkCore Passport mismatch
arbitrary confirmation
fail-open tenant reads
```

New/open:

```text
MagicAI subscription schema mismatch
Reviews contact table defect
AI route-specific auth mismatch
functional workspace incompleteness
```

## Scan 20

Resolved on current main:

```text
permission enum blocker
confirmation blocker
parent native API middleware blocker
no menu adapter / navigation shell
```

Current code adds signed approvals, plan entitlement projection, workspace catalogue, menu synchronizer and entitlement reconciliation. MagicAI host queue and CSRF defects remain open.

---

# 31. Decisions established by Scan 21

1. WorkCore Business Network is the final CRM authority.
2. MagicAI CRM/Sales become migration sources, not permanent peers.
3. Business Network owns front-office business records.
4. MagicAI remains platform identity, subscription and AI-provider authority.
5. CRM AI tools remain a deliberately restricted subset of governed actions.
6. Catalogue remains WorkCore authority for operational services.
7. Support channels may be MagicAI-facing, but tickets are WorkCore records.
8. Knowledge authority stays WorkCore even when MagicAI renders AI answers.
9. Reviews and Territories remain WorkCore with conservative AI exposure.
10. Intelligence remains evidence/recommendation infrastructure.
11. Expansion remains bounded/declarative; no arbitrary runtime code generation.
12. WorkCore native AI is now reachable through Business Network.
13. WorkCore AI should still execute models through a MagicAI adapter.
14. Native WorkCore navigation in MagicAI is structurally implemented.
15. Functional CRM workspaces remain incomplete.
16. MagicAI subscription-schema defaults must be fixed before entitlement rollout.
17. The one-time legacy CRM importer is mandatory before cutover.
18. `tz_contacts` must be fixed before review workflows launch.

---

# 32. Next combined scan

```text
22-workcore-commercial-and-magicai-integration-deep-scan.md
```

It will cover quotes/estimates, invoices, payments, receivables, collections, GST, ledger, reconciliation, payroll, inventory, supply/procurement, Titan Vault, trust accounting, MagicAI SaaS billing boundaries, payment-provider reuse, Commercial workspace, AI tools and completion-to-invoice integration.

---

# Evidence sources

## WorkCore current main

```text
native-extensions/WorkCoreBusinessNetwork/extension.manifest.json
native-extensions/WorkCoreBusinessNetwork/System/WorkCoreBusinessNetworkServiceProvider.php
packages/workcore-business-network/files.sha256.json
packages/workcore-business-network/src/Domains/WorkCore/Providers/BusinessNetworkServiceProvider.php
packages/workcore-business-network/src/Domains/WorkCore/System/Modules/CRM/Providers/WorkCRMServiceProvider.php
packages/workcore-business-network/src/Domains/WorkCore/System/Modules/CRM/routes/api.php
packages/workcore-business-network/src/Domains/WorkCore/System/Modules/CRM/AI/CRMToolRegistry.php
packages/workcore-business-network/src/Domains/WorkCore/System/Modules/Catalogue/Providers/WorkCatalogueServiceProvider.php
packages/workcore-business-network/src/Domains/WorkCore/System/Modules/Catalogue/AI/CatalogueToolRegistry.php
packages/workcore-business-network/src/Domains/WorkCore/System/Modules/Support/Providers/WorkSupportServiceProvider.php
packages/workcore-business-network/src/Domains/WorkCore/System/Modules/Support/AI/SupportToolRegistry.php
packages/workcore-business-network/src/Domains/WorkCore/System/Modules/KnowledgeBase/Providers/WorkKnowledgeBaseServiceProvider.php
packages/workcore-business-network/src/Domains/WorkCore/System/Modules/Knowledge/AI/KnowledgeToolRegistry.php
packages/workcore-business-network/src/Domains/WorkCore/System/Modules/Reviews/Providers/WorkReviewsServiceProvider.php
packages/workcore-business-network/src/Domains/WorkCore/System/Modules/Reviews/Repositories/EloquentReviewRepository.php
packages/workcore-business-network/src/Domains/WorkCore/System/Modules/Reviews/AI/ReviewsToolRegistry.php
packages/workcore-business-network/src/Domains/WorkCore/System/Modules/Territories/Providers/WorkTerritoriesServiceProvider.php
packages/workcore-business-network/src/Domains/WorkCore/System/Modules/Territories/AI/TerritoriesToolRegistry.php
packages/workcore-business-network/src/Domains/WorkCore/System/Modules/Wizards/Providers/WorkWizardsServiceProvider.php
packages/workcore-business-network/src/Domains/WorkCore/System/Modules/Wizards/AI/WizardsToolRegistry.php
packages/workcore-business-network/src/Domains/WorkCore/System/Intelligence/Providers/WorkCoreIntelligenceServiceProvider.php
packages/workcore-business-network/src/Domains/WorkCore/System/Expansion/Providers/WorkExpansionServiceProvider.php
packages/workcore-business-network/src/Domains/WorkCore/System/AI/Providers/WorkCoreAIServiceProvider.php
packages/workcore-business-network/src/Domains/WorkCore/System/AI/routes/api.php
packages/workcore-shared-foundation/src/Domains/WorkCore/Config/workcore_ai.php
native-extensions/WorkCore/System/WorkCoreServiceProvider.php
native-extensions/WorkCore/config/workcore-native.php
native-extensions/WorkCore/System/Entitlements/MagicAIEffectiveSubscriptionResolver.php
native-extensions/WorkCore/System/Navigation/WorkCoreWorkspaceCatalogue.php
native-extensions/WorkCore/System/Navigation/WorkCoreWorkspaceManifest.php
native-extensions/WorkCore/System/Navigation/MagicAIMenuSynchronizer.php
native-extensions/WorkCore/System/Navigation/workspaces.php
native-extensions/WorkCore/routes/user.php
native-extensions/WorkCore/resources/views/workspace.blade.php
native-extensions/WorkCore/database/migrations/2026_08_03_020000_add_workcore_entitlements_to_magicai_plans.php
WORKCORE-MAGICAI-CRM-REPLACEMENT-UPGRADE-PLAN.md
docs/upgrade-baseline/known-defects.md
```

## MagicAI 11 host

```text
app/Domains/Marketplace/MarketplaceServiceProvider.php
app/Services/Common/MenuService.php
app/Http/Middleware/CheckTemplateTypeAndPlan.php
app/Providers/AppServiceProvider.php
app/Http/Middleware/VerifyCsrfToken.php
database/migrations/2019_05_03_000002_create_subscriptions_table.php
app/Models/Subscriptions.php
```

# Evidence limitations

This was a static repository and host-package scan. It did not execute real MagicAI entitlement reconciliation, Business Network routes with Passport, functional CRM datasets in the workspace shell, MagicAI AI Agent tool calls, a paid-CRM migration, live AI providers, channel notifications or multi-company concurrency tests. Future work described in the repository is marked incomplete rather than assumed implemented.
