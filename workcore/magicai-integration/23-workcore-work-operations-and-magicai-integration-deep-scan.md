---
title: "WorkCore Work Operations and MagicAI 11 — Combined Integration Deep Scan"
scan_number: 23
date: "2026-08-09"
status: "Completed"
magicai_source: "MagicAI 11.00 supplied server package"
workcore_repository: "Masterleeaus/workcore-extensions"
workcore_ref: "main"
workcore_commit: "64d05e1a7a1071bf61c1f252d359fbd712da39f5"
repository_target: "Masterleeaus/Documents"
repository_path: "workcore/magicai-integration/23-workcore-work-operations-and-magicai-integration-deep-scan.md"
---

# Scan 23 — WorkCore Work Operations + MagicAI 11 Integration

## Executive conclusion

`WorkCoreWorkOperations` is the operational execution engine MagicAI does not natively provide.

The add-on activates seven modules:

- `operations`
- `scheduling`
- `dispatch`
- `recurring`
- `forms`
- `repairs`
- `fleet`

Together they provide the field-service lifecycle:

```text
customer/site
→ work order
→ appointment
→ dispatch
→ field execution
→ forms/evidence
→ completion
→ Commercial invoice readiness
```

MagicAI should remain the host for authentication, SaaS entitlements, AI Chat/AI Agent UX, external calendar and map connectors, notifications, storage transport, and the application shell. WorkCore remains authoritative for work orders, appointments, dispatch assignments, recurring service agreements, forms/submissions, repairs, fleet allocation, time entries, completion state and operational evidence references.

The architecture is sound, but production integration remains blocked by host/runtime issues and several package gaps:

1. QRCode is still not activated by the native Work Operations add-on.
2. The manifest understates the effective runtime activated by the wrapper.
3. Functional Work Operations screens remain incomplete in the MagicAI workspace shell.
4. MagicAI's boot-time queue rewriting remains incompatible with operational queues.
5. MagicAI's broad `dashboard/*` CSRF exemption remains unsafe.
6. Calendar and map integrations must remain projections/adapters, not operational authorities.
7. Offline/mobile synchronization is not yet proven end to end.
8. The Work Operations → Commercial completed-job contract is not yet reliably wired.
9. AI operational tools must remain company-, permission-, risk- and confirmation-filtered.

---

# 1. Native extension identity

```text
Folder: WorkCoreWorkOperations
Manifest key: workcore-work-operations
Marketplace key: workcore_work_operations
Provider: App\Extensions\WorkCoreWorkOperations\System\WorkCoreWorkOperationsServiceProvider
Version: 0.1.1
Parent: WorkCore ^0.1
```

Compatibility:

```text
MagicAI >= 11.0
Laravel ^10.0
PHP ^8.2
```

The provider loads exactly:

```text
operations
scheduling
dispatch
recurring
forms
repairs
fleet
```

and preserves parent-owned operational data on uninstall.

---

# 2. Capabilities

The native manifest advertises:

```text
workcore.operations
workcore.scheduling
workcore.dispatch
workcore.recurring
workcore.forms
workcore.repairs
workcore.fleet
```

This matches the seven activated runtime keys.

---

# 3. QRCode activation gap

The broader Work Operations package family still identifies QRCode as owned source/capability, but neither the native manifest nor the provider loads a `qrcode` module.

Current state:

```text
owned/source family includes QRCode
native activation keys = 7
qrcode activation key = absent
```

This earlier finding remains unresolved on current `main`.

Required resolution:

1. create a first-class `qrcode` module/provider and load it from Work Operations; or
2. reclassify QRCode as a parent/shared WorkCore capability; or
3. deliberately merge QR workflows into Operations/Forms and remove standalone ownership metadata.

Do not leave QRCode owned but unreachable.

---

# 4. Manifest transparency

The add-on manifest declares:

```text
permissions: []
routes: []
queues.used: false
schedules: []
webhooks: []
owned_tables: []
```

That describes only the activation wrapper, not the modules it activates.

The effective runtime contributes governed actions, read models, routes, permissions, operational tables, events, AI registries and recurring/background behaviours.

Generated release metadata should include:

```text
effective_actions
effective_read_models
effective_permissions
effective_routes
effective_tables
effective_ai_tools
effective_events
effective_queues
effective_schedules
effective_external_integrations
```

---

# 5. Operations module

The Operations provider registers four governed writes:

```text
workcore.work_order.create
workcore.work_order.change_status
workcore.work_order_task.update_status
workcore.time_entry.add
```

and one read model:

```text
workcore.work_order.search
```

It binds WorkOrder repository and access contracts.

Every write has:

```text
capability
permission
risk
confirmation requirement
```

Current risk levels:

```text
work order create          medium
work order status change   high
task status update         medium
time entry add             medium
```

All currently require confirmation.

This is conservative and appropriate while MagicAI approval/action-card UX is still being hardened.

---

# 6. Scheduling module

Scheduling registers:

```text
workcore.appointment.create
workcore.appointment.reschedule
workcore.appointment.change_status
```

and:

```text
workcore.appointment.search
```

All writes are medium-risk and confirmation-required.

This establishes WorkCore as schedule authority.

---

# 7. Calendar integration rule

MagicAI's calendar connectors should be projections, not the source of truth.

Correct relationship:

```text
WorkCore Appointment
    = operational schedule authority

Google/Outlook Calendar Event
    = external projection and optional availability signal
```

Required connector responsibilities:

```text
publish appointment
update event
cancel event
capture external availability
store external event ID
store sync revision
detect conflicts
retry safely
```

Inbound events must re-resolve company membership and WorkCore record authority.

---

# 8. Dispatch module

Dispatch registers:

```text
workcore.dispatch.assign
workcore.dispatch.reassign
workcore.dispatch.change_status
workcore.dispatch.search
```

Writes are medium-risk and confirmation-required.

The design correctly separates:

```text
work order
appointment
dispatch assignment
worker/crew
```

MagicAI may supply UI, maps and AI suggestions, but WorkCore owns dispatch state.

---

# 9. Maps/geocoding boundary

Maps should provide:

```text
address lookup
geocoding
route visualization
distance/travel estimates
map rendering
```

They must not own:

```text
territory
job
appointment
dispatch assignment
```

Business Network owns territory logic. Work Operations owns dispatch and schedule.

---

# 10. Recurring Services

The repository contains real recurring-service actions including:

```text
CreateServiceAgreement
ChangeServiceAgreementStatus
GenerateRecurringOccurrences
SkipRecurringOccurrence
RescheduleRecurringOccurrence
RenewServiceAgreement
```

This is a genuine recurring-service domain, not just a calendar repeat rule.

Recommended lifecycle:

```text
service agreement
→ recurrence rule
→ generated occurrence
→ appointment/work order
→ dispatch
→ completion
→ next occurrence
```

Recurring generation must be:

```text
idempotent
company scoped
time-zone aware
DST safe
horizon bounded
revision aware
audited
```

Scheduler retries and concurrent generators must not create duplicate occurrences.

---

# 11. Forms

The Forms module includes:

```text
CreateFormTemplate
PublishFormTemplate
StartFormSubmission
SaveFormResponse
SubmitFormSubmission
ConvertFormFailureToWorkOrder
```

with a provider, routes and AI registry.

This should become the common operational evidence engine for:

- job checklists
- pre-starts
- safety checks
- cleaning checklists
- inspections
- handovers
- vehicle checks
- equipment checks
- incident intake
- completion evidence

Published form versions should be immutable snapshots. A submission must retain the exact template version used.

---

# 12. Form failure → work order

`ConvertFormFailureToWorkOrder` is an important governed cross-workflow transition.

Required invariants:

```text
one failure creates at most one linked work order
source submission preserved
evidence preserved
company preserved
actor preserved
idempotency preserved
```

AI may recommend conversion. The WorkCore action owns the mutation.

---

# 13. Repairs

The Repairs module contains actions including:

```text
CreateRepairTemplate
ReportRepairOrder
StartRepairOrder
CompleteRepairOrder
SearchRepairOrders
```

plus a provider, repository and routes.

Recommended relationship:

```text
asset/premise
→ repair report
→ repair order
→ work order/appointment if field execution required
→ completion/evidence
→ Commercial cost/invoice handoff
```

Property Operations remains asset/premises authority. Work Operations owns repair execution.

---

# 14. Fleet

Fleet contains actions including:

```text
CreateFleetVehicle
AssignFleetVehicle
ReturnFleetVehicle
RecordFleetMileage
SearchFleetVehicles
```

with provider and route surfaces.

Recommended authority split:

```text
fleet assignment/status        Work Operations
worker eligibility             Workforce Assurance
vehicle asset documents        Property/Asset capabilities
fuel/expense accounting        Commercial
map/route display              MagicAI/external maps
```

Safe early AI tools should be read-oriented. Assignment/return/safety-state mutation should remain governed.

---

# 15. Work Operations read-model strategy

MagicAI dashboards and AI context should consume WorkCore read models, never direct operational Eloquent queries.

Confirmed examples:

```text
workcore.work_order.search
workcore.appointment.search
workcore.dispatch.search
```

Additional projections should include:

```text
my_day
late_jobs
unassigned_jobs
today_dispatch
recurring_exceptions
form_failures
open_repairs
fleet_availability
invoice_ready_jobs
```

---

# 16. MagicAI Operations workspace

The native WorkCore navigation foundation already supports the Operations workspace.

Target sections:

```text
Overview
Jobs & Work Orders
Schedule
Dispatch
Map & Territories
Recurring Services
Forms & Checklists
Repairs
Fleet
Reports
Operations Assistant
```

The navigation/entitlement shell exists, but complete functional screens remain unfinished.

Manager screens still required:

```text
work-order table/detail
calendar
dispatch board
map
recurring-service manager
form-template/submission manager
repair queue
fleet board
invoice-readiness panel
```

Field-worker/mobile views still required:

```text
My Day
Current Job
Schedule
Messages
Forms
Knowledge
Photos/Evidence
Complete Job
```

---

# 17. Mobile/offline contract

Responsive web UI alone is insufficient.

Offline bundles should include only authorized operational data:

```text
assigned work order
appointment
dispatch assignment
tasks/checklist
form submissions
customer/site summary
required evidence
knowledge snippets
pending actions
```

Offline mutations should be stored as:

```text
action_key
company
actor/device
record_public_id
base_revision
payload
idempotency_key
created_at
```

Reconnect flow:

```text
reauthenticate
→ re-resolve membership
→ compare revision
→ authorize
→ apply conflict policy
→ execute governed action
→ return receipt
```

Never synchronize authoritative tables directly from the client.

---

# 18. Device identity

Field operations need identity beyond a MagicAI login.

Recommended operation context:

```text
actor subject
MagicAI user
WorkCore membership
worker
device
company
branch/territory
assurance level
```

This matters for photos, location, attendance, signatures, completion and offline replay.

---

# 19. Notifications and customer communications

MagicAI should provide transport for:

```text
appointment confirmation
ETA
reschedule notice
technician-on-way
job completion
form/signature request
payment request
```

Correct flow:

```text
WorkCore domain event/outbox
→ MagicAI notification/channel adapter
→ email/SMS/chat/WhatsApp/etc.
→ delivery result
→ WorkCore communication receipt
```

Business repositories should never send channels directly.

---

# 20. AI Operations Assistant

MagicAI AI Chat/AI Agent should expose filtered WorkCore tools.

Useful reads:

```text
today's jobs
late jobs
unassigned jobs
worker schedule
job details
open repairs
recurring exceptions
fleet availability
```

Governed writes can include:

```text
create work order
reschedule appointment
assign/reassign dispatch
change work status
update task
record time
start/submit form
```

Every mutation terminates in the canonical WorkCore action dispatcher.

High-consequence actions such as bulk reschedule, completion/cancellation, evidence override or safety-critical repair closure should retain explicit approval rules.

---

# 21. Work completion is commercially significant

Completion can trigger:

```text
evidence validation
customer notification
review request
invoice eligibility
inventory consumption
job costing
workforce/time consequences
compliance updates
```

Keeping work-order status change as high-risk is appropriate.

---

# 22. Work Operations → Commercial handoff

Scan 22 confirmed Commercial contains:

```text
CompletedJobMoneyFlowListener
CreateInvoiceFromCompletedJob
WorkCoreJobInvoiceAssembler
```

but production source/event wiring is incomplete.

Work Operations should emit one canonical durable event:

```text
workcore.work_order.completed.v1
```

Recommended envelope:

```text
event_id
company_public_id
work_order_public_id
customer_public_id
premises_public_id
completion_revision
completed_at
completed_by_actor
correlation_id
```

Commercial should then read billable details through a versioned Work Operations read contract.

Do not place financial truth directly in the event.

---

# 23. Invoice readiness

A work order should not be considered invoice-ready merely because `status=complete`.

Recommended deterministic projection:

```text
completed
customer present
billable lines present
required time present
required forms submitted
required evidence present
required signatures present
material usage settled
variations approved
no blocking dispute
```

Commercial consumes that projection.

---

# 24. Cross-package authority

## Business Network → Work Operations

```text
customer
support ticket
catalogue service
territory
opportunity
→ governed conversion
→ work order
```

## Property Operations → Work Operations

```text
premise
space
asset
document context
→ work order / repair / form
```

## Workforce Assurance → Work Operations

```text
worker
skills
certifications
roster
eligibility
→ dispatch assignment
```

## Commercial → Work Operations

```text
inventory reservations
billable service/material context
```

## Work Operations → Commercial

```text
completed work
invoice-readiness projection
job costing inputs
→ invoice workflow
```

All cross-package writes should use actions/contracts/events, not direct shared-table writes.

---

# 25. Route/authentication strategy

Module providers can load API routes when enabled.

Inside MagicAI they must use Passport-compatible:

```text
auth:api
workcore.tenant
workcore.api
```

Do not reintroduce standalone Sanctum assumptions.

Recommended two API planes:

```text
generic action/read API
    for agents, automation and cross-module execution

purpose-built Work Operations API
    for stable web/mobile clients
```

Both must enforce the same WorkCore policies.

---

# 26. MagicAI queue blocker

MagicAI's existing boot-time logic rewrites non-default queue names to `default` and runs a queue worker during web application boot.

That is incompatible with Work Operations tasks such as:

```text
recurring generation
calendar sync
notifications
offline reconciliation
outbox delivery
document processing
AI jobs
Commercial handoff
```

Remove that host behaviour before Work Operations production rollout.

---

# 27. MagicAI CSRF blocker

MagicAI's broad `dashboard/*` CSRF exemption remains unsafe.

Operational writes requiring normal CSRF include:

```text
dispatch
reschedule
status change
form submission
repair completion
fleet assignment
```

Restore CSRF protection before functional workspace forms launch.

---

# 28. Entitlements

Work Operations capabilities depend on MagicAI plan projection.

Scan 21 found the MagicAI subscription resolver's defaults do not match the supplied MagicAI 11 subscription schema.

That blocker affects Operations too.

Visibility must ultimately require:

```text
extension installed
module loaded
runtime ready
company entitled
integration healthy
actor permitted
```

not only a plan feature flag.

---

# 29. Degraded mode

Operational truth must survive optional host-integration failure.

```text
AI unavailable
→ jobs/scheduling/forms still work

calendar unavailable
→ WorkCore schedule remains authoritative; sync retries

maps unavailable
→ dispatch remains usable without map visualization

notifications unavailable
→ business state commits; delivery retries

Commercial unavailable
→ completion event remains durable; invoice processing retries
```

---

# 30. QR workflows once activated

Useful QR workflows include:

```text
scan premise/asset
open current work
start inspection
open required form
verify equipment
record service
open payment handoff
```

QR payloads must use opaque/public IDs and signed/expiring action links where appropriate.

Never encode privileged action payloads or sequential IDs directly.

---

# 31. Security rules

1. Never trust company IDs from request without membership validation.
2. MagicAI team membership is not worker eligibility.
3. Use public IDs externally.
4. Customer/QR action links must be signed and expiring.
5. Offline actions require stable idempotency keys.
6. Photos/evidence use private storage.
7. Geolocation needs purpose and retention policy.
8. Status changes go through governed actions.
9. Cross-package writes use actions/contracts/events.
10. AI never writes operational repositories directly.

---

# 32. Current blockers

## Critical

1. MagicAI boot-time queue rewriting/worker.
2. MagicAI dashboard-wide CSRF exemption.
3. MagicAI subscription-schema mismatch affecting WorkCore entitlements.
4. Work Operations → Commercial completion event/source wiring incomplete.

## High

5. QRCode owned but not activated.
6. Functional Operations workspace incomplete.
7. Offline sync not proven end to end.
8. Production Calendar adapter not proven.
9. Production maps/geocoding adapter not proven.
10. Effective manifest metadata absent.
11. Cross-package runtime-health diagnostics incomplete.
12. Device identity/assurance incomplete.

## Medium

13. Recurring concurrency/DST tests required.
14. Immutable form-version evidence needs E2E proof.
15. Repair-to-work-order linkage needs cross-package tests.
16. Fleet eligibility/asset integration needs proof.
17. Customer communication receipts need host adapter.
18. Bulk dispatch/reschedule approval policy needs definition.

---

# 33. Required implementation sequence

## Host safety

1. remove MagicAI boot queue worker/rewrite;
2. restore CSRF;
3. fix MagicAI subscription mapping;
4. verify six-provider map.

## Packaging

5. resolve QRCode activation;
6. generate effective manifest;
7. add extension health diagnostics.

## Functional UI

8. work orders;
9. schedule/calendar;
10. dispatch board;
11. recurring services;
12. forms/checklists;
13. repairs;
14. fleet.

## Host adapters

15. Calendar connector;
16. maps/geocoding;
17. notifications/channels;
18. private evidence storage.

## Offline/mobile

19. device identity;
20. encrypted local operational bundle;
21. queued governed actions;
22. conflict resolution;
23. receipts/replay.

## Cross-package workflows

24. CRM/support → work order;
25. Property → work/repair;
26. Workforce eligibility → dispatch;
27. Inventory reservation/consumption;
28. completion → Commercial invoice event.

---

# 34. Required E2E tests

## Work orders
- create;
- cross-company access rejected;
- invalid transition rejected;
- completion permission/confirmation;
- duplicate completion executes once.

## Scheduling
- create/reschedule/status;
- timezone and DST;
- connector retry;
- external calendar failure does not lose WorkCore schedule.

## Dispatch
- assign eligible worker;
- reject wrong company/worker;
- concurrent reassignment;
- maps outage does not block work.

## Recurring
- occurrence generated once;
- retry does not duplicate;
- DST handling;
- skip/reschedule/renew preserve agreement history.

## Forms
- published version immutable;
- offline save/replay;
- submission retains version;
- failure creates one linked work order;
- evidence remains private.

## Repairs
- report/start/complete;
- correct premise/asset linkage;
- linked work order created once.

## Fleet
- create/assign/return/mileage;
- cross-company rejection;
- worker eligibility policy.

## Commercial handoff
- one completion event;
- retry creates one invoice;
- incomplete evidence blocks readiness;
- Commercial outage does not undo completed work.

## MagicAI
- entitlement projects correctly;
- workspace hides when not entitled;
- Passport works;
- CSRF enforced;
- named queues preserved;
- AI reads and writes use WorkCore governance.

---

# 35. Authority matrix

| Domain | MagicAI | Work Operations | Authority |
|---|---|---|---|
| Login | yes | consumes | MagicAI |
| SaaS plan | yes | consumes | MagicAI |
| Work orders | no | yes | WorkCore |
| Appointments | connector projection | yes | WorkCore |
| Dispatch | UI/map assist | yes | WorkCore |
| Recurring services | no | yes | WorkCore |
| Forms | UI/AI assist | yes | WorkCore |
| Repairs | no | yes | WorkCore |
| Fleet assignment | map/UI assist | yes | WorkCore |
| Customer notifications | transport | event source | WorkCore state + MagicAI delivery |
| AI provider | yes | governed tools/context | shared boundary |
| Offline execution | shell/donor capability | operational authority | WorkCore |

---

# 36. Decisions established by Scan 23

1. WorkCore Work Operations is operational execution authority.
2. External calendars are projections, not schedule authority.
3. Maps are adapters, not dispatch authority.
4. Completion remains a high-consequence governed transition.
5. Completion must emit one durable versioned Commercial event.
6. Invoice readiness is deterministic projection, not a status assumption.
7. Forms become the common operational evidence engine.
8. Recurring services remain WorkCore-native.
9. Offline writes replay governed actions, not raw table sync.
10. Fleet remains company/worker scoped.
11. QRCode activation must be resolved.
12. Functional Operations UI remains incomplete.
13. MagicAI queue and CSRF defects remain production blockers.
14. AI operational writes always terminate in WorkCore governed actions.
15. Optional external integrations must degrade without losing operational truth.

---

# 37. Next combined scan

```text
24-workcore-property-operations-and-magicai-integration-deep-scan.md
```

Scope:

```text
premises
spaces
assets
documents
vertical operations
hotels/BnB/rooming
real estate/property management
field-service site context
MagicAI storage/vision/maps
evidence/document handling
Work Operations linkage
Workforce/Compliance linkage
```

---

# Evidence sources

## Current WorkCore main

```text
native-extensions/WorkCoreWorkOperations/extension.manifest.json
native-extensions/WorkCoreWorkOperations/System/WorkCoreWorkOperationsServiceProvider.php
packages/workcore-work-operations/src/Domains/WorkCore/Providers/WorkOperationsServiceProvider.php
packages/workcore-work-operations/src/Domains/WorkCore/System/Modules/Operations/Providers/WorkOperationsServiceProvider.php
packages/workcore-work-operations/src/Domains/WorkCore/System/Modules/Scheduling/Providers/WorkSchedulingServiceProvider.php
packages/workcore-work-operations/src/Domains/WorkCore/System/Modules/Dispatch/Providers/WorkDispatchServiceProvider.php
packages/workcore-work-operations/src/Domains/WorkCore/System/Modules/RecurringServices/
packages/workcore-work-operations/src/Domains/WorkCore/System/Modules/Forms/
packages/workcore-work-operations/src/Domains/WorkCore/System/Modules/Repairs/
packages/workcore-work-operations/src/Domains/WorkCore/System/Modules/Fleet/
native-extensions/WorkCore/System/Navigation/workspaces.php
packages/workcore-shared-foundation/src/Domains/WorkCore/Config/workcore.php
```

## MagicAI evidence retained from prior scans

```text
MarketplaceServiceProvider
MenuService
CheckTemplateTypeAndPlan
AppServiceProvider queue logic
VerifyCsrfToken
Calendar/connectors
AI Chat / AI Agent
notifications
storage
mobile/API host surfaces
```

## Evidence limitations

Static source scan only. No live job, scheduler, calendar sync, map call, offline replay, field form, completion event or invoice handoff was executed.
