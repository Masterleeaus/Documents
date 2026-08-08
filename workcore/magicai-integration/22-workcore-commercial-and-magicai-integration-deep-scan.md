---
title: "WorkCore Commercial and MagicAI 11 — Combined Integration Deep Scan"
scan_number: 22
date: "2026-08-09"
status: "Completed"
magicai_source: "MagicAI 11.00 supplied server package"
workcore_repository: "Masterleeaus/workcore-extensions"
workcore_ref: "main"
workcore_commit: "64d05e1a7a1071bf61c1f252d359fbd712da39f5"
repository_target: "Masterleeaus/Documents"
repository_path: "workcore/magicai-integration/22-workcore-commercial-and-magicai-integration-deep-scan.md"
---

# Scan 22 — WorkCore Commercial + MagicAI 11 Integration

## Executive conclusion

`WorkCoreCommercial` is the correct operational commercial and finance authority for Titan Zero inside MagicAI, but the current package is **not yet production-ready as an end-to-end money system**.

The package is substantial:

```text
435 tracked source files
6 top-level module keys
17 global governed Finance write actions
6 global Finance read models
7 additional Titan Money runtime actions
7 inventory write actions
13 supply-chain write actions
8 trust-accounting write actions
```

The extension activates:

```text
finance
payroll
inventory
supply
vault
trust_accounting
```

The Finance module itself contains a broad Titan Money system covering:

```text
quotes
invoices
credit notes
receivables
payments
payment instructions
cash
PayID
bank transfer
PayPal card
payment evidence
receipts
collections
bank import
reconciliation
ledger
GST
cashflow forecasting
job profitability
expenses
budgets
expense claims
procurement
e-invoicing
workforce loans
POS and sales orders
```

However, its own configuration explicitly declares:

```text
production_ready => false
```

That declaration should be treated as authoritative.

The correct product boundary is:

```text
MagicAI
    → SaaS plans
    → subscription billing
    → prepaid credits
    → platform payment gateways
    → platform administration

WorkCore Commercial
    → customer quotes
    → operational invoices
    → customer payments
    → receivables
    → payment evidence
    → ledger
    → reconciliation
    → operational GST
    → inventory and supply
    → trust accounting
```

MagicAI's SaaS payment records must never become the operational accounting ledger for WorkCore customer transactions.

---

# 1. Source authority

This scan uses WorkCore current `main`:

```text
64d05e1a7a1071bf61c1f252d359fbd712da39f5
```

and the supplied MagicAI 11 host source.

This report supersedes provisional Commercial conclusions in earlier MagicAI-only scans where explicitly identified.

The current source hierarchy is:

```text
WorkCore current repository
    = Commercial implementation authority

MagicAI supplied source
    = host/payment-platform implementation authority

Scans 01–21
    = retained evidence and architecture history
```

---

# 2. Native Commercial extension identity

```text
Folder:
WorkCoreCommercial

Manifest key:
workcore-commercial

MagicAI provider-map key:
workcore_commercial

Provider:
App\Extensions\WorkCoreCommercial\System\WorkCoreCommercialServiceProvider

Version:
0.1.1

Parent:
WorkCore ^0.1
```

Compatibility:

```text
MagicAI >= 11.0
Laravel ^10.0
PHP ^8.2
```

The add-on owns activation only.

It does not own WorkCore migrations or destructive uninstall behaviour.

---

# 3. Top-level capabilities

The native manifest advertises:

```text
workcore.finance
workcore.payroll
workcore.inventory
workcore.supply
workcore.vault
workcore.trust_accounting
```

These correspond to the six loaded module keys.

The parent remains the database/migration owner.

---

# 4. Commercial package size

The package checksum manifest reports:

```text
435 tracked files
```

This is not a thin Finance adapter.

Commercial contains real operational domain code, repositories, actions, read models, internal services, payment infrastructure and financial rules.

---

# 5. Effective-manifest transparency problem

The native add-on manifest reports:

```text
permissions: []
routes: []
queues.used: false
schedules: []
webhooks: []
billing.mode: none
owned_tables: []
```

That describes only the tiny activation wrapper.

It does not describe the runtime contributions of the six modules it activates.

The effective Commercial runtime contains:

- financial permissions
- governed actions
- read models
- optional HTTP routes
- webhook infrastructure
- command workers
- payment integrations
- outbox usage
- financial tables
- AI registries
- regulated trust-accounting actions

## Required improvement

Build generated transitive metadata:

```text
effective_actions
effective_read_models
effective_permissions
effective_routes
effective_webhooks
effective_commands
effective_queues
effective_tables
effective_external_hosts
effective_regulatory_risk
runtime_readiness
```

Marketplace install review should assess the effective runtime, not only the wrapper manifest.

---

# 6. Commercial provider behaviour inside MagicAI

`WorkCoreCommercialServiceProvider`:

1. merges native Commercial config;
2. checks the add-on enable flag;
3. verifies the parent WorkCore runtime exists;
4. verifies `WorkModuleRegistry` is bound;
5. disables direct Finance HTTP routes;
6. loads the six Commercial modules;
7. publishes configuration;
8. retains all operational data on uninstall.

This is intentionally conservative.

---

# 7. Direct Titan Money routes are deliberately disabled

The Commercial native provider forces:

```text
workcore.finance.routes_enabled = false
```

inside MagicAI.

The reason is correct:

the older Titan Money direct API routes use:

```text
auth:sanctum
```

while MagicAI uses Passport-compatible:

```text
auth:api
```

The disabled direct route plane includes:

```text
dashboard/user/titan-money/*
api/titan-money/*
api/titan-money/webhooks/*
```

This avoids accidentally exposing the older standalone Finance route contract.

## Consequence

Finance capabilities must be exposed through:

```text
global WorkCore governed actions
global WorkCore read models
native WorkCore workspace adapters
safe provider-specific webhooks
```

rather than simply switching the old Titan Money route group back on.

---

# 8. Finance/Titan Money implementation breadth

The Finance configuration declares support for:

```text
invoices
payments
quotes
expenses
ledger
GST posting and reporting
receivables ageing
payables
bank reconciliation
cashflow forecasting
job profitability
accountant exports
auto-invoicing policy
payment QR
collection sequences
payment evidence matching
payment allocation
receipts
reconciliation exceptions
PayPal settlement handling
Australian bank CSV import
budget control
expense claims
procurement
supplier credits
e-invoicing
workforce loans
POS
field sales orders
```

This is much broader than MagicAI's built-in SaaS billing engine.

---

# 9. Finance readiness is explicitly false

Finance config states:

```text
production_ready => false
```

This must control release messaging.

Do not expose Titan Money as production-grade accounting merely because many feature flags are true.

The correct interpretation is:

```text
architecture and substantial runtime exist
+
multiple workflows are implemented
+
production integration gates remain incomplete
```

---

# 10. Australian defaults

The Finance defaults are aligned to the initial Australian target:

```text
country_code: AU
base_currency: AUD
timezone: Australia/Melbourne
financial_year_start: 1 July
payment_terms_days: 7
prices_include_tax: true
GST accounting basis: cash
```

Currencies also include:

```text
AUD
NZD
USD
CAD
GBP
EUR
JPY
```

This is a useful base, but multi-country tax and regulated-money claims require jurisdiction-specific validation before expansion.

---

# 11. Payment priority matches Titan Zero

Titan Money supports the intended customer-payment priority:

```text
Cash
PayID
Bank transfer
PayPal card
```

The payment adapter registry includes exactly those four mechanisms.

This is a strong fit for Titan Zero's operational-payment model.

---

# 12. Payment adapters are instruction adapters, not all full processors

The payment adapter registry contains:

```text
CashPaymentAdapter
PayIdPaymentAdapter
BankTransferPaymentAdapter
PayPalPaymentAdapter
```

The PayPal adapter currently creates payment instructions containing a URL/reference.

It is not itself proof of a complete merchant checkout integration.

The provider-level PayPal verifier and connection resolver default to fail-closed implementations.

Therefore:

```text
payment-method contract: present
payment instruction contract: present
live operational PayPal merchant integration: incomplete
```

---

# 13. MagicAI payment stack is SaaS/platform billing

MagicAI's `PaymentGatewayEnum` includes:

```text
PayPal
Stripe
2Checkout
YooKassa
Iyzico
Paystack
Bank Transfer
RevenueCat
CoinGate
Paddle
Razorpay
Cryptomus
Midtrans
```

But the surrounding services are built around:

```text
Plan
Subscriptions
UserOrder
GatewayProducts
credits
prepaid purchases
subscription renewal
platform activity
```

That is a **platform billing system**, not an operational customer ledger.

---

# 14. Non-negotiable billing boundary

The two payment domains must remain separate.

## MagicAI owns

```text
Titan Zero SaaS subscription
plan purchase
prepaid AI/platform credits
platform checkout
platform subscription renewal
platform billing webhooks
```

## WorkCore owns

```text
customer quote
customer invoice
customer receivable
payment request
payment evidence
payment allocation
receipt
collection workflow
ledger
GST
bank reconciliation
```

No WorkCore invoice should be represented as a MagicAI `Subscription` or `UserOrder`.

No MagicAI SaaS subscription should be posted into the WorkCore customer receivable ledger as if it were a service-business customer invoice.

---

# 15. Reuse provider transport, not SaaS business logic

MagicAI's gateway stack can still be useful.

Safe reuse targets include:

```text
low-level provider HTTP transport
OAuth/client handling
webhook signature libraries
provider-specific API wrappers
currency/provider capability metadata
```

Unsafe direct reuse includes:

```text
MagicAI subscription service
MagicAI UserOrder
MagicAI plan product sync
platform credit updates
SaaS renewal logic
platform sales counters
```

Recommended adapter form:

```text
WorkCore Operational Payment Gateway Contract
    ↓
MagicAI provider transport adapter
    ↓
company-scoped WorkCore credential reference
```

---

# 16. MagicAI gateway credentials are platform-global

MagicAI stores gateway configuration in the platform `gateways` table.

The model exposes fields such as:

```text
sandbox_client_id
sandbox_client_secret
live_client_id
live_client_secret
webhook_secret
bank_account_details
```

No encrypted cast was confirmed in the supplied `Gateways` model.

No application-level encryption wrapper for those fields was found in the targeted scan.

## Implication

These are platform-level payment credentials.

They should not automatically become credentials for every WorkCore tenant.

---

# 17. WorkCore's provider connection model is the right operational boundary

WorkCore payment orchestration stores:

```text
company_id
payment_method
provider_key
connection_label
state
configuration
credentials_reference
```

This supports company-specific operational payment connections.

That is the correct shape for a multi-tenant business OS.

## Required hardening

The `configuration` payload is currently generic JSON.

Enforce:

```text
no raw secret values in configuration
credentials only through credentials_reference
credential-reference allowlist
company-bound secret resolution
rotation metadata
revocation
audit
```

---

# 18. MagicAI bank-transfer proof storage must not be reused

MagicAI's bank-transfer subscription flow stores uploaded proof using:

```text
public_path('proofs')
```

The proof is therefore placed in a public web path.

That may be acceptable only under a very narrow platform design, but it is inappropriate for WorkCore operational payment evidence.

WorkCore payment evidence may contain:

- payer names
- bank references
- amounts
- account details
- screenshots
- device provenance
- customer data

## Required WorkCore policy

Store operational payment evidence in:

```text
private object/file storage
company-scoped path
opaque file ID
hash
MIME validation
malware scan
retention class
audit
signed temporary download
```

Never reuse MagicAI's public proof path.

---

# 19. MagicAI BankTransferListener proves the SaaS coupling

MagicAI's bank-transfer listener updates:

```text
Laravel Cashier subscriptions
stripe_status
subscription end dates
user plan credits
platform activity
```

This is direct evidence that MagicAI's bank-transfer flow is subscription-specific.

It is not a customer invoice reconciliation engine.

---

# 20. Global WorkCore Finance action bridge

Finance registers **17 actions** with the canonical WorkCore `BusinessActionRegistry`.

```text
workcore.finance.quote.create
workcore.finance.quote.transition
workcore.finance.invoice.create
workcore.finance.invoice.issue
workcore.finance.invoice.transition
workcore.finance.credit_note.create
workcore.finance.credit.allocate
workcore.finance.receivable.allocate
workcore.finance.expense.record
workcore.finance.expense.approve
workcore.finance.account.create
workcore.finance.period.create
workcore.finance.period.close
workcore.finance.journal.post
workcore.payment.provider.upsert
workcore.payment.session.create
workcore.payment.attempt.record
```

This is the correct long-term external write plane.

---

# 21. Global Finance read models

Finance registers:

```text
workcore.finance.summary
workcore.finance.quote.profile
workcore.finance.invoice.profile
workcore.finance.receivables.search
workcore.payment.session
workcore.payment.orchestration.summary
```

These should drive:

- MagicAI workspace UI
- dashboard cards
- AI read context
- customer portal
- mobile field views

without exposing Finance tables directly.

---

# 22. Critical permission vocabulary mismatch

The canonical WorkCore Finance bridge currently assigns:

```text
finance.manage
```

to all 17 global writes.

Its read models use:

```text
finance.view
```

But Finance's own permission catalogue defines detailed permissions under:

```text
money.*
```

Examples include:

```text
money.invoices.draft
money.invoices.approve
money.invoices.issue
money.payments.confirm
money.payments.allocate
money.expenses.approve
money.reconciliation.manage
money.ledger.post
money.periods.close
money.payment_instructions.send
money.payment_qr.generate
```

No canonical `finance.manage` permission definition was found outside the bridge.

## Consequence

Owners/admins may work because their WorkCore membership resolves to full access.

Ordinary company roles may be denied unless `finance.manage` / `finance.view` are manually seeded.

Worse, one broad permission collapses separation between:

```text
drafting an invoice
issuing an invoice
posting a journal
closing an accounting period
configuring payment providers
```

## Severity

**Critical before role-based Finance rollout**

## Required correction

Map every global Finance action to the appropriate granular `money.*` permission.

Example:

```text
quote.create
→ money.quotes.draft

invoice.create
→ money.invoices.draft

invoice.issue
→ money.invoices.issue

expense.approve
→ money.expenses.approve

period.close
→ money.periods.close

journal.post
→ money.ledger.post

payment.provider.upsert
→ money.integrations.manage
```

Read models should use granular view permissions.

---

# 23. Two Finance governance planes currently coexist

Finance has two action systems:

## Canonical WorkCore plane

```text
BusinessActionRegistry
BusinessActionDispatcher
WorkCore confirmation grants
WorkCore capability checks
WorkCore audit/domain event/outbox
```

## Titan Money internal plane

```text
Finance ActionRegistry
TitanMoneyAction
FinanceAccessGate
Titan Money idempotency
internal approval modes
GenerativeUiEnvelope
Finance audit/outbox
```

The internal plane registers actions such as:

```text
money.health
money.create_invoice_from_job
money.ingest_payment_evidence
money.confirm_payment_match
money.collection.execute_followup
money.bank_statement.import
money.reconciliation.close
```

## Problem

The advanced money workflow is split from the canonical WorkCore action plane.

That creates duplicated:

```text
risk models
approval concepts
idempotency systems
audit systems
action catalogues
permission paths
```

## Required decision

The **canonical WorkCore BusinessActionDispatcher must become the outer authority**.

Wrap or adapt advanced Titan Money actions behind WorkCore governed actions.

Do not maintain two public automation planes.

---

# 24. Quote lifecycle

The Finance repository implements:

```text
tm_quotes
tm_quote_versions
tm_quote_lines
```

A quote includes:

- tenant company
- WorkCore customer
- site/job linkage
- version
- currency
- validity date
- state
- snapshot hash
- line-level totals
- actor audit data

Lifecycle transitions are validated through `QuoteLifecycle`.

State updates are transactionally locked.

This is a strong foundation.

---

# 25. Quote immutability model

Quotes store version snapshots rather than treating a mutable record as the entire commercial agreement.

That supports:

```text
revision history
customer acceptance evidence
scope comparison
future audit
```

Production completion still needs:

- customer-facing proposal delivery
- acceptance/signature evidence
- optional items
- variation workflow
- quote-to-work-order conversion
- quote-to-invoice linkage
- portal confirmation
- retention policy

---

# 26. Invoice lifecycle

The Finance repository implements:

```text
tm_invoices
tm_invoice_lines
```

with:

```text
draft
approved
issued
delivered
viewed
paid
voided
credited
```

and lifecycle validation.

It stores:

- totals in minor units
- allocated amount
- credited amount
- outstanding balance
- draft snapshot
- issued snapshot hash
- lock version
- WorkCore customer/site/job link
- source quote/source record
- actor audit fields

This is significantly stronger than a simple invoice CRUD table.

---

# 27. Issued-document integrity

When an invoice is issued, Finance records an:

```text
issued_snapshot_hash
```

This is the correct direction for immutable commercial documents.

Production release should ensure:

- issued financial fields cannot be edited in place
- corrections use credit/reversal/reissue
- rendered document matches the stored snapshot
- PDF/hash evidence is retained
- tax calculations are reproducible

---

# 28. Credit notes and allocations

Finance implements credit notes with:

```text
amount
tax
currency
invoice linkage
line items
reason
issued state
allocation
```

Allocation validates:

- currency compatibility
- available credit
- invoice balance
- positive amount
- transaction boundaries

This is a useful accounting control.

---

# 29. Ledger and accounting periods

The global WorkCore bridge includes:

```text
workcore.finance.account.create
workcore.finance.period.create
workcore.finance.period.close
workcore.finance.journal.post
```

Closing a period and posting a journal are marked:

```text
critical
```

This is correct.

They must receive explicit granular permissions and strong approval controls before exposure.

---

# 30. GST and reporting

Finance contains:

```text
GstCalculator
GstReportCalculator
```

and config defaults for Australian GST.

This is useful domain infrastructure but must not be marketed as tax compliance certification without:

- validated BAS/GST reporting rules
- adjustment/credit handling
- rounding tests
- cash/accrual basis tests
- mixed taxable/GST-free lines
- jurisdiction/legal review

---

# 31. Payment methods

Finance's operational payment priority is:

```text
cash
payid
bank_transfer
paypal_card
```

This aligns strongly with Titan Zero.

It can generate payment instructions and QR payloads.

The provider also requires Bacon QR code support.

---

# 32. Payment sessions

The global payment orchestration layer can:

```text
configure provider connection
create payment session
record payment attempt
read payment session
read orchestration summary
```

Payment session tokens:

- use 32 random bytes
- persist only a SHA-256 hash
- return the raw token on creation
- default to an expiry window

This is a good session-token storage pattern.

---

# 33. A payment session is not yet a provider checkout

`workcore.payment.session.create` creates the WorkCore local orchestration record.

It does not by itself prove:

```text
PayPal order created
Stripe intent created
bank redirect created
provider token exchanged
```

A production provider-execution contract is still required.

Recommended:

```text
OperationalPaymentGatewayContract
    createCheckout()
    verifyCallback()
    capture()
    cancel()
    refund()
    queryStatus()
```

with company-scoped credential resolution.

---

# 34. Automatic completed-job invoice workflow exists

Finance contains:

```text
CompletedJobMoneyFlowListener
CreateInvoiceFromCompletedJob
WorkCoreJobInvoiceAssembler
InvoiceEligibilityEvaluator
InvoiceIssuer
PaymentRequestService
```

The intended flow is:

```text
work.job.completed
→ validate completed/billable/evidence/customer
→ assemble invoice lines
→ create invoice
→ apply invoice issue policy
→ generate payment request/QR
→ deliver instructions
→ schedule collections
→ emit audit/outbox
```

This is an excellent Titan Zero workflow design.

---

# 35. Critical automatic-invoicing blocker: source adapter absent

`CreateInvoiceFromCompletedJob` depends on:

```text
WorkCoreCommercialSource
```

The production provider defaults this to:

```text
FailClosedWorkCoreCommercialSource
```

which throws:

```text
Bind the Platform Core WorkCoreCommercialSource adapter before using automatic invoicing.
```

The targeted source search found only:

```text
FailClosedWorkCoreCommercialSource
InMemoryWorkCoreCommercialSource
```

No production adapter binding was confirmed.

## Consequence

The advertised automatic completed-job → invoice pipeline cannot execute against real WorkCore work orders.

## Required adapter

Implement:

```text
NativeWorkCoreCommercialSource
```

using stable Work Operations read contracts for:

```text
completed job
customer
site
billable service/labour/material lines
time
inventory consumption
travel
discounts
evidence completeness
job cost summary
```

---

# 36. Critical automatic-invoicing blocker: listener not registered

`CompletedJobMoneyFlowListener` exists.

No provider or event registration for that class was found in the current targeted repository search.

Therefore:

```text
listener implementation exists
event wiring not confirmed
```

The Finance config flag:

```text
workcore_completion_listener => true
```

does not by itself make the listener live.

## Required correction

Prefer durable outbox consumption:

```text
work.order.completed.v1
→ Commercial invoice consumer
```

rather than an in-process Laravel event dependency.

This supports:

- retries
- idempotency
- replay
- cross-module versioning
- failure diagnostics

---

# 37. CreateInvoiceFromCompletedJob has strong internal controls

The action already checks:

- explicit actor
- invoice draft permission
- issue permission when issuing
- payment-instruction permission
- QR-generation permission
- idempotency key
- request fingerprint
- duplicate source key
- completed job
- billable status
- customer presence
- billable lines
- evidence completeness
- transactional invoice creation
- audit
- outbox
- optional approval
- reusable Generative UI envelope

The missing integration adapter/wiring is therefore the main blocker, not absence of business logic.

---

# 38. Payment evidence and matching

Finance includes:

```text
PaymentEvidenceRepository
ReceivableRepository
PaymentMatchRepository
BankDepositMatcher
PaymentConfirmationService
FinancialExceptionRepository
```

It can rank payment evidence against receivables.

Configured automatic match threshold:

```text
95
```

This is a sensible architecture for PayID/bank-transfer evidence.

Production should require a measured precision/false-match test set before enabling automatic allocation.

---

# 39. Reconciliation

Finance includes:

```text
AustralianBankCsvParser
BankStatementImportService
DatabaseBankImportRepository
DatabaseBankTransactionWriter
ReconciliationRepository
CloseReconciliation
```

The close action requires:

```text
statement and reconciled balances match
no unresolved lines remain
```

and is high risk with explicit approval in the internal action plane.

This is the correct accounting control.

---

# 40. Collections

The default collection sequence is:

```text
3 days before due: helpful
due date: friendly
3 days overdue: friendly reminder
7 days overdue: direct reminder
14 days overdue: firm reminder
21 days overdue: payment-plan offer + approval
30 days overdue: escalation review + approval
```

Delivery surfaces include:

```text
email
chatbot
SMS
task
```

Suppressions include:

```text
dispute open
payment plan active
payment claim pending
paused
paid
voided
credited
```

This is a strong operational collections foundation.

---

# 41. Collections delivery remains an adapter task

Titan Money declares interface support for:

```text
AIChatPro
Chatbot
AI Agent
Pulse
API
web
```

but those configuration booleans are not evidence that the supplied MagicAI runtime has those adapters wired.

Current Finance transport defaults include fail-closed/null boundaries.

Therefore:

```text
collection decision engine: present
MagicAI channel delivery integration: incomplete
```

---

# 42. Outbox and queue integration

Finance uses:

```text
outbox records
relay queue
scheduled actions
Pulse financial actions
Titan Money relay command
```

This architecture depends on correct queue/scheduler operation.

MagicAI's current `AppServiceProvider::jobRuns()` behaviour remains incompatible because it:

- rewrites non-default queues to `default`
- invokes a worker during web boot

That host behaviour must be removed before Finance background operations are trusted.

---

# 43. Finance commands

Finance registers commands including:

```text
RunPulseFinancialActionsCommand
RelayTitanMoneyOutboxCommand
```

These should run through proper scheduler/worker infrastructure.

They should not execute through request boot side effects.

---

# 44. Money-domain catalogue

Finance contains additional subdomains:

## Budgeting

```text
tm_budgets
tm_budget_lines
tm_budget_actuals
tm_budget_variances
tm_budget_forecast_scenarios
```

## Expense claims

```text
tm_expense_claims
tm_expense_claim_receipts
tm_reimbursement_batches
tm_reimbursements
```

## Procurement

```text
tm_purchase_orders
tm_purchase_order_lines
tm_purchase_receipts
tm_purchase_receipt_lines
tm_supplier_credits
```

## Electronic invoicing

```text
tm_einvoice_profiles
tm_einvoice_transmissions
tm_einvoice_events
```

## Workforce loans

```text
tm_workforce_loans
tm_workforce_loan_repayments
```

## Sales

```text
tm_pos_sales
tm_pos_sale_lines
tm_pos_sale_payments
tm_sales_orders
tm_sales_order_lines
```

These increase the need for effective manifest generation and finer plan packaging.

---

# 45. Payroll is not a live product runtime

`WorkPayrollServiceProvider` explicitly registers status:

```text
mode: domain_schema_foundation
runtime_enabled: false
```

with the description that payroll calculation and persistence foundations exist but application runtime remains opt-in.

Therefore:

```text
schema/domain foundation: yes
runtime-enabled payroll product: no
```

The Commercial manifest's `workcore.payroll` capability must not make the UI advertise full payroll availability.

---

# 46. Payroll AI exposure is correctly empty

`PayrollToolRegistry` returns:

```text
[]
```

No payroll tools should be exposed to AI until payroll itself becomes a validated runtime.

---

# 47. Inventory

Inventory is a genuine operational module.

It registers **7 governed writes**:

```text
inventory item create
stock location create
stock movement
stock reserve
reservation consume
reservation release
reorder rule
```

and **2 read models**:

```text
inventory search
stock location search
```

High-risk controls apply to:

```text
stock movement
reservation consumption
```

---

# 48. Inventory AI surface is conservative

The AI tool registry exposes:

## Reads

```text
inventory_search_items
inventory_search_locations
```

## Actions

```text
inventory_reserve_item
inventory_release_reservation
```

It does not expose:

```text
stock adjustment
stock consumption
inventory creation
stock movement
```

This is a good initial autonomy policy.

---

# 49. Supply chain

Supply registers **13 governed actions** including:

```text
supplier create/rate
purchase order create/approve
goods receipt
stock transfer create/dispatch/receive
stock count create/complete
stock adjustment create/post
```

Critical action:

```text
workcore.supply.adjustment.post
```

Read models:

```text
supplier search
purchase order search
```

This is a meaningful procurement and stock-control system.

---

# 50. Supply AI exposure is correctly empty

`SupplyToolRegistry` currently returns:

```text
[]
```

This is appropriate until:

- PO approval
- goods receiving
- stock adjustment
- supplier mutation

have stronger production workflow tests.

---

# 51. Titan Vault is metadata-search only

The Vault provider currently registers:

```text
workcore.vault.document.search
```

with:

```text
metadata_only: true
```

No secret-content write/read plane is exposed.

The AI tool:

```text
vault_search_documents
```

is marked restricted and searches only authorized document metadata.

This is a strong security boundary.

---

# 52. Do not confuse Titan Vault with MagicAI gateway credentials

WorkCore Vault's current capability is not a complete tenant secret manager.

MagicAI's payment gateway secrets are currently platform gateway settings.

WorkCore's operational provider connections expect an opaque:

```text
credentials_reference
```

A production secret resolver is still required.

Recommended final design:

```text
Tenant Credential Vault
    secret encrypted
    company scoped
    purpose constrained
    provider scoped
    rotation/revocation audited

WorkCore record
    stores opaque credential reference only
```

---

# 53. Trust Accounting is highly sensitive

Trust Accounting registers **8 governed writes**:

```text
trust account create
trust matter create
trust receipt record
trust receipt allocate
trust disbursement request
trust disbursement approve
trust disbursement release
trust ledger reverse
```

Every one is:

```text
high or critical risk
requires confirmation
```

Read models:

```text
trust summary
trust matter ledger
```

Metadata labels it:

```text
regulated_client_money
```

That is the correct risk framing.

---

# 54. Trust Accounting must be separately gated

Trust accounting should not be bundled into an ordinary "Commercial" entitlement with no regulatory distinction.

Require:

```text
explicit plan feature
supported jurisdiction
business type eligibility
separation-of-duties policy
strong MFA/assurance for approval/release
immutable audit
reconciliation controls
retention
specialized legal/accounting review
```

It should remain disabled by default for ordinary field-service tenants.

---

# 55. Commercial entitlement is currently too coarse

The parent MagicAI plan projection maps `ext_workcore_commercial` to a wide group including:

```text
workcore.finance
workcore.money.budgeting
workcore.money.expense_claims
workcore.money.procurement
workcore.money.einvoicing
workcore.money.workforce_loans
workcore.money.sales
workcore.payroll
workcore.inventory
workcore.supply
workcore.vault
workcore.trust_accounting
```

One boolean can therefore theoretically unlock:

- unfinished payroll
- regulated trust accounting
- operational Finance
- inventory
- procurement
- vault metadata

This is too broad.

---

# 56. Required entitlement split

Recommended MagicAI plan flags:

```text
ext_workcore_finance
ext_workcore_inventory
ext_workcore_supply
ext_workcore_payroll
ext_workcore_vault
ext_workcore_trust_accounting
```

Optional money-domain flags:

```text
ext_workcore_budgeting
ext_workcore_expense_claims
ext_workcore_procurement
ext_workcore_einvoicing
ext_workcore_workforce_loans
ext_workcore_pos_sales
```

Alternatively retain a Commercial bundle but require runtime readiness/vertical overrides for sensitive modules.

---

# 57. Capability state needs more than installed + entitled

A robust capability state should distinguish:

```text
registered
package installed
module loaded
runtime ready
tenant entitled
tenant configured
actor permitted
integration healthy
```

Payroll demonstrates why.

It can be:

```text
registered = true
installed = true
loaded = true
entitled = true
runtime_ready = false
```

UI and AI tools must obey the full state, not only entitlement.

---

# 58. Commercial workspace

The native WorkCore workspace catalogue defines:

```text
Commercial
```

with sections:

```text
Service Catalogue
Quotes and Estimates
Invoices
Payments and Receivables
Expenses and Payables
Procurement
Reconciliation
Profitability
```

This is the right product information architecture.

---

# 59. Commercial workspace is still a structural shell

The current shared WorkCore workspace Blade renders:

- navigation
- title
- description
- entitlement information
- capability badges
- "foundation ready" status

It does not yet provide full functional:

```text
quote editor
invoice editor
receivables queue
payment evidence review
payment matching
collections queue
bank reconciliation
expense approval
procurement workspace
profitability dashboard
```

So:

```text
workspace navigation: implemented
functional Commercial browser product: incomplete
```

---

# 60. MagicAI legacy Sales should become read-only after cutover

MagicAI CRM/Sales includes legacy:

```text
invoices
payments
proposals
estimates
```

The final architecture is:

```text
MagicAI legacy Sales
→ import source
→ temporary read-only rollback view

WorkCore Commercial
→ sole operational authority
```

Permanent two-way financial synchronization would create an unacceptable double-ledger risk.

---

# 61. One-time financial migration requirements

Legacy MagicAI records need mapping:

```text
Proposal/Estimate → WorkCore Quote
Invoice → WorkCore Invoice
Payment → WorkCore Payment/Allocation
Product → WorkCore Catalogue Item
Project/Job reference → WorkCore Work Order
Customer/Company → WorkCore Customer
```

Preserve:

```text
source ID
source checksum
original number
currency
tax
totals
status
issue/due date
payment allocations
attachments
audit metadata
```

Every financial record must reconcile.

---

# 62. Financial migration acceptance gate

Before cutover:

```text
invoice counts match
invoice totals match
outstanding balances match
payment totals match
credit totals match
tax totals reconcile
customer balances reconcile
source-to-target map complete
```

Any unmatched item enters a reconciliation queue.

No silent financial migration loss.

---

# 63. AI Finance surface is intentionally empty

`FinanceToolRegistry` returns:

```text
[]
```

This is the right current state.

Do not expose:

- journal posting
- invoice issuing
- payment confirmation
- refunds
- period closure
- trust disbursement

to general-purpose AI until the action/governance systems are unified and tested.

---

# 64. Future safe Finance AI sequence

Recommended first tools:

## Read only

```text
finance_get_summary
finance_get_invoice
finance_search_receivables
finance_get_quote
finance_get_profitability
```

Then low-risk draft/proposal tools:

```text
finance_draft_quote
finance_prepare_invoice_draft
finance_propose_collection_message
```

Keep actual issue/post/allocate/close operations under explicit governed action approval.

---

# 65. MagicAI AI provider remains separate from money authority

MagicAI may help generate:

```text
quote wording
scope summaries
invoice explanations
collection drafts
financial summaries
customer-facing payment instructions
```

But financial truth remains deterministic WorkCore data.

The LLM must never calculate or invent authoritative balances when deterministic Finance services exist.

---

# 66. Customer portal/payment-page direction

A safe customer-facing payment page should read only a narrow WorkCore projection:

```text
invoice public ID
customer-safe document number
amount due
currency
due date
allowed payment methods
payment reference
QR/payment URL
payment status
```

It must not expose:

```text
internal ledger
bank reconciliation
costing
margin
provider credentials
internal customer notes
company-wide finance data
```

---

# 67. Operational PayPal integration gap

Current state:

```text
PayPal instruction adapter exists
provider connection model exists
PayPal webhook interfaces exist
default resolver/verifier fail closed
live company-scoped PayPal transport not confirmed
```

MagicAI has a mature PayPal SaaS implementation but it is coupled to platform subscriptions.

Required:

```text
WorkCorePayPalGatewayAdapter
```

or:

```text
MagicAIPayPalOperationalTransportAdapter
```

that uses company-scoped WorkCore credentials and never touches MagicAI subscription records.

---

# 68. Webhook safety requirement

If operational payment webhooks are enabled later, require:

```text
provider signature verification
company connection resolution
external event inbox
event ID idempotency
timestamp/replay limits
amount/currency validation
payment-request correlation
auditable status transition
no browser session auth dependency
```

Finance already contains an external event inbox contract and PayPal webhook components, which are useful foundations.

---

# 69. Public MagicAI webhook route is not a template to copy blindly

MagicAI exposes generic platform webhook routes to `PaymentProcessController`.

Those are designed for its own payment subsystem.

WorkCore should have provider-specific operational webhook endpoints and validation.

Do not funnel WorkCore customer-payment webhooks into MagicAI's SaaS `PaymentProcessController`.

---

# 70. CSRF host issue remains

MagicAI's broad:

```text
dashboard/*
```

CSRF exemption remains unsafe.

Once functional WorkCore Commercial web forms are added, this becomes especially serious for:

```text
quote changes
invoice issue
provider configuration
payment matching
expense approvals
stock adjustments
```

Restore normal CSRF protection.

---

# 71. Queue host issue remains critical for Finance

MagicAI's current boot logic that rewrites queue names and runs a worker during web boot is incompatible with:

- Finance outbox
- collections
- payment processing
- bank import
- reconciliation jobs
- notifications
- provider webhooks
- scheduled entitlement refresh

Remove it before production Finance rollout.

---

# 72. Scan 21 subscription-schema blocker also blocks Commercial

WorkCore's MagicAI entitlement resolver currently defaults to subscription columns that do not match the supplied MagicAI 11 schema:

```text
expects status
MagicAI has stripe_status

expects starts_at
MagicAI has no starts_at
```

Until fixed, `ext_workcore_commercial` entitlement projection can fail.

That means Commercial workspace and action visibility may never become valid even when the plan flag exists.

---

# 73. Commercial plan feature must be tested against real MagicAI schema

Required test fixture:

```text
real MagicAI plans columns
real MagicAI subscriptions columns
stripe_status values
trial_ends_at
ends_at
lifetime plans
bank transfer statuses
cancelled/expired states
```

Then verify all Commercial sub-capabilities project deterministically.

---

# 74. Money amounts must remain deterministic

Finance uses integer minor-unit amounts in core operational records.

This is correct.

Never use floating-point LLM-generated amounts as accounting truth.

All calculations should flow through:

```text
DecimalMoneyParser
GST calculators
posting templates
invoice line arithmetic
ledger services
```

---

# 75. Trust accounting cannot inherit generic owner omnipotence blindly

The current WorkCore owner/admin RBAC path grants broad `All`.

For ordinary business records this may be acceptable.

For trust money, consider explicit non-bypass controls:

```text
requester cannot approve own disbursement
approver cannot release if separation required
release requires strong assurance
ledger reversal requires separate authority
```

This should be modeled as regulated action policy above generic company-owner access.

---

# 76. Payment-provider configuration needs sensitive-field schemas

`UpsertPaymentProviderConnection` delegates arbitrary payload to the repository.

The repository stores:

```text
configuration JSON
credentials_reference
```

There is no evidence in the scanned action that raw secret-like keys are stripped from `configuration`.

Required:

```text
provider-specific JSON schema
secret-key denylist
credential reference validation
redaction in audit/event payload
```

A bad client must not be able to store raw API secrets in a plaintext configuration JSON blob.

---

# 77. Payment session attempt numbering needs concurrency testing

The repository computes default attempt number from:

```text
max(attempt_number) + 1
```

Without a demonstrated unique constraint/lock in this method, concurrent attempts may choose the same number.

Production test:

```text
two simultaneous payment-attempt writes
→ unique sequential attempt numbers
or
→ harmless unique IDs with deterministic conflict handling
```

---

# 78. WorkCore Finance and Commercial data authority

Final authority matrix:

| Domain | MagicAI | WorkCore Commercial | Authority |
|---|---|---|---|
| SaaS plan | Yes | consumes entitlement | MagicAI |
| SaaS subscription | Yes | projects feature access | MagicAI |
| Platform credits | Yes | none | MagicAI |
| Customer quote | legacy donor | Yes | WorkCore |
| Customer invoice | legacy donor | Yes | WorkCore |
| Customer payment | platform gateway transport possible | Yes | WorkCore |
| Receivable | No | Yes | WorkCore |
| Ledger | No | Yes | WorkCore |
| GST operational reporting | No | Yes | WorkCore |
| Bank reconciliation | No | Yes | WorkCore |
| Inventory | No | Yes | WorkCore |
| Supply/procurement | No | Yes | WorkCore |
| Payroll | no final authority | foundation only | Not production-ready |
| Trust accounting | No | Yes, regulated | WorkCore with special gate |
| Provider transport | Yes | adapter consumer | Shared through adapter |
| Provider secrets | platform-global currently | needs tenant vault refs | Separate scopes |

---

# 79. Critical blockers

## C-01 — Finance declares itself not production-ready

Source:

```text
production_ready => false
```

Release implication:

```text
block production accounting claims
```

---

## C-02 — Global Finance permission names do not match Finance permission catalogue

Global bridge:

```text
finance.manage
finance.view
```

Finance catalogue:

```text
money.*
```

Impact:

```text
ordinary role authorization can fail
separation of duties is collapsed
```

---

## C-03 — Completed-job invoice adapter is fail closed

No production `WorkCoreCommercialSource` implementation was confirmed.

Impact:

```text
automatic job → invoice fails
```

---

## C-04 — Completed-job listener is not confirmed registered

Implementation exists; registration not found.

Impact:

```text
job completion does not trigger money flow
```

---

## C-05 — Two Finance governance planes

Impact:

```text
duplicate risk
duplicate approval
duplicate idempotency
duplicate audit
inconsistent automation behaviour
```

---

## C-06 — Commercial entitlement projection inherits MagicAI schema mismatch

Impact:

```text
Commercial entitlement can fail before UI/action use
```

---

## C-07 — MagicAI queue boot behaviour is incompatible with Finance

Impact:

```text
unsafe background execution
queue isolation loss
```

---

## C-08 — MagicAI dashboard CSRF exemption affects future financial forms

Impact:

```text
unsafe state-changing dashboard actions
```

---

# 80. High blockers

## H-01 — Live operational PayPal transport not confirmed

Instruction generation exists; real company merchant execution remains incomplete.

## H-02 — Provider connection configuration can carry arbitrary JSON

Need secret filtering and schemas.

## H-03 — Finance direct routes are disabled

Correct for safety, but functional adapters/endpoints must replace them.

## H-04 — Functional Commercial workspace is incomplete

Navigation exists; operational screens are not finished.

## H-05 — Payroll capability is advertised while runtime is disabled

Need readiness-aware capability visibility.

## H-06 — Trust accounting is bundled too broadly

Needs separate regulated entitlement.

## H-07 — Finance AI interface flags overstate integration

`FinanceToolRegistry` is empty and host adapters are incomplete.

## H-08 — Collection channel delivery adapters remain incomplete

## H-09 — Operational webhook contract not fully wired

## H-10 — One-time MagicAI Sales/Finance migration is not implemented

---

# 81. Medium blockers

1. Attempt-number concurrency needs proof.
2. Gateway secret-at-rest policy needs improvement.
3. MagicAI payment proof storage is public and must never be reused.
4. Company billing-account link is not yet explicit.
5. Financial document rendering/hash retention needs E2E proof.
6. Accountant export and e-invoicing need provider/jurisdiction verification.
7. POS and workforce-loan subdomains need product readiness gates.
8. Vault remains metadata-only.
9. Inventory/Supply AI exposure should remain conservative until business tests pass.
10. Menu/cache invalidation needs company + entitlement revision correctness.

---

# 82. Required implementation sequence

## Phase 1 — Host safety

1. Fix MagicAI subscription column mapping.
2. Remove MagicAI boot-time queue worker.
3. Restore dashboard CSRF.
4. Keep direct Sanctum Finance routes disabled.
5. Verify provider-map registration.

## Phase 2 — Finance authorization

6. Replace `finance.manage` / `finance.view`.
7. Map each action/read model to granular permission.
8. Seed roles.
9. Add separation-of-duty rules.
10. Add critical-action assurance requirements.

## Phase 3 — Governance convergence

11. Wrap internal Titan Money actions in canonical WorkCore governed actions.
12. Use one approval envelope.
13. Use one external action catalogue.
14. Preserve internal deterministic finance services, not duplicate public action systems.

## Phase 4 — Work Operations bridge

15. Implement production WorkCoreCommercialSource.
16. Define completed-work event contract.
17. Register durable invoice consumer.
18. Add job invoice line/cost mapper.
19. Add retry/replay/dead-letter tests.

## Phase 5 — Payments

20. Harden provider connection schema.
21. Add tenant credential vault resolver.
22. Add operational PayPal adapter.
23. Add provider webhook inbox.
24. Add PayID/bank evidence ingestion flow.
25. Validate automatic match threshold.
26. Add customer payment page.

## Phase 6 — UI

27. Build Quote workspace.
28. Build Invoice workspace.
29. Build Receivables workspace.
30. Build Payment Evidence/Matching workspace.
31. Build Reconciliation workspace.
32. Build Procurement/Inventory surfaces.
33. Build Profitability dashboard.

## Phase 7 — Migration/cutover

34. Import MagicAI Sales/finance data.
35. Reconcile totals.
36. Put legacy Sales into read-only mode.
37. Enable WorkCore Commercial menus.
38. Monitor.
39. Retire old write paths after rollback window.

---

# 83. Required test matrix

## Permissions

- ordinary bookkeeper can view invoices
- ordinary bookkeeper cannot close periods
- dispatcher cannot post journals
- invoice drafter cannot issue without issue permission
- provider connection requires integration-management permission
- trust requester cannot approve/release own request where policy requires separation
- owners do not bypass special regulated controls

## Quotes/invoices

- quote version snapshot remains immutable
- invalid quote transition rejected
- invoice issue hashes snapshot
- issued invoice cannot be silently edited
- credit cannot exceed available amount
- allocation cannot exceed balance
- duplicate source job creates one invoice

## Job completion

- completed eligible job produces one invoice
- incomplete evidence blocks issue
- non-billable job blocks invoice
- missing customer blocks invoice
- retry replays safely
- duplicate completion event does not duplicate invoice
- job from Company B cannot invoice in Company A

## Payments

- PayID instructions correct
- bank transfer instructions correct
- PayPal checkout uses Company A credentials only
- wrong provider credential reference rejected
- raw credential in configuration rejected
- webhook signature required
- replayed webhook ignored
- amount/currency mismatch quarantined
- payment session token hash only stored
- session expiration enforced

## Evidence/reconciliation

- payment proof stored privately
- evidence hash retained
- automatic match below threshold does not allocate
- threshold match only auto-confirms policy-safe case
- concurrent matching allocates once
- reconciliation cannot close with unresolved items
- reconciliation balances must match

## Ledger/tax

- invoice issue posts balanced journal
- payment allocation posts balanced journal
- credit note posts correct reversal/credit
- closed period rejects posting
- GST rounding tests
- cash/accrual basis test
- multi-currency rejection/conversion policy explicit

## Inventory/Supply

- reserve/release idempotent
- stock cannot go below allowed policy
- PO approval requires correct role
- goods receipt updates stock once
- critical stock adjustment requires confirmation
- AI cannot post stock adjustment

## Payroll

- runtime not shown as available while disabled
- no AI tools exposed

## Trust

- trust account tenant scoped
- receipt allocation cannot exceed receipt
- disbursement cannot release before required approval
- reverse ledger preserves immutable history
- cross-company trust access denied
- critical confirmation grant single-use

## MagicAI host

- `ext_workcore_commercial` projects from real subscription schema
- Commercial menu hidden when not entitled
- company switch changes finance context
- queues preserve Finance queue names
- dashboard write requires CSRF
- legacy Sales can run read-only during rollback window

---

# 84. Revisions to earlier documentation

## Scan 08 — Plans/subscriptions/billing

Retain:

```text
MagicAI owns SaaS billing
WorkCore owns operational finance
```

Update with:

```text
WorkCore entitlement projection exists
but current subscription column defaults do not match supplied MagicAI 11
```

---

## Scan 11 — Sales/estimates/invoices/payments

Retain as host analysis.

Current implementation authority is now:

```text
Scan 22 + WorkCore Commercial current main
```

Important current corrections:

```text
WorkCore has substantial Titan Money implementation
direct Finance routes intentionally disabled in MagicAI
global governed Finance bridge exists
automatic job invoicing is blocked by missing production source adapter/event wiring
```

---

## Scan 18 — blocker register

Add:

```text
Finance global permission mismatch
dual governance planes
job-invoice source adapter missing
job-completion listener not wired
Commercial entitlement schema mismatch
```

---

## Scan 21

Retain:

```text
MagicAI subscription schema blocker
native workspaces/menu
CRM authority
```

Scan 22 extends that model to operational Finance.

---

# 85. Decisions established by Scan 22

1. WorkCore Commercial is operational Finance authority.
2. MagicAI remains SaaS/platform billing authority.
3. MagicAI subscription records are never WorkCore customer ledger records.
4. Provider transport may be reused only through an operational adapter.
5. Company-scoped WorkCore payment credentials must be separate from platform-global gateway settings.
6. MagicAI public bank-transfer proof storage will not be reused.
7. Direct Titan Money Sanctum routes remain disabled in MagicAI.
8. Canonical WorkCore BusinessActionDispatcher becomes the external Finance action plane.
9. Titan Money internal action services may remain implementation services but not a competing public governance system.
10. `finance.manage` / `finance.view` must be replaced with granular Finance permissions.
11. Automatic job invoicing is blocked until a production WorkCoreCommercialSource exists.
12. Job completion should enter Finance through a durable versioned event/outbox consumer.
13. Finance is not production-ready while its config says `production_ready=false`.
14. Payroll remains a disabled foundation.
15. Trust Accounting requires separate regulated entitlement and policy.
16. Finance AI tools remain disabled initially.
17. Inventory AI may retain narrow reserve/release capability.
18. Vault AI stays restricted to metadata search.
19. Legacy MagicAI Sales becomes migration/read-only, not a parallel ledger.
20. Financial migration requires full numerical reconciliation before cutover.

---

# 86. Recommended target architecture

```text
┌────────────────────────────────────────────────────────────┐
│                         MagicAI 11                         │
│ auth • plans • subscriptions • provider transports • UI  │
└──────────────────────────────┬─────────────────────────────┘
                               │
                     host adapter contracts
                               │
┌──────────────────────────────▼─────────────────────────────┐
│                       WorkCore Parent                      │
│ tenancy • permissions • entitlement • actions • audit    │
│ confirmation • event/outbox • read models                │
└──────────────────────────────┬─────────────────────────────┘
                               │
┌──────────────────────────────▼─────────────────────────────┐
│                    WorkCore Commercial                    │
│                                                           │
│ Finance/Titan Money                                       │
│   quotes → invoices → receivables → payments → ledger    │
│   evidence → matching → receipt → reconciliation          │
│                                                           │
│ Inventory • Supply • Vault metadata • Trust Accounting   │
│ Payroll foundation                                        │
└───────────────┬─────────────────────────┬──────────────────┘
                │                         │
        Work Operations events      Payment adapters
                │                         │
      completed work / costs       Cash / PayID / Bank
                │                  / operational PayPal
                ▼                         ▼
          invoice assembler        customer payment page
```

---

# 87. Definition of done for Commercial integration

Commercial is ready for production only when all of the following are true:

```text
[ ] Finance config production_ready is deliberately changed to true
[ ] MagicAI subscription entitlement fixture passes
[ ] Finance permission vocabulary is reconciled
[ ] global/internal governance planes are unified
[ ] WorkCoreCommercialSource production adapter exists
[ ] completed-job event consumer is wired
[ ] duplicate completed-job events create one invoice
[ ] functional Commercial workspaces exist
[ ] PayID/bank evidence flow is private and audited
[ ] operational PayPal uses company credential references
[ ] provider webhooks are verified and idempotent
[ ] reconciliation invariants pass
[ ] ledger balance invariants pass
[ ] GST tests pass
[ ] queue/scheduler host defect is removed
[ ] CSRF host defect is removed
[ ] migration reconciles legacy financial totals
[ ] payroll is either completed or hidden
[ ] trust accounting is separately gated
[ ] critical multi-company tests pass
```

---

# 88. Next combined scan

```text
23-workcore-work-operations-and-magicai-integration-deep-scan.md
```

It should inspect:

```text
WorkCore Work Operations
+
MagicAI scheduling/calendar
+
mobile/PWA
+
customer communication
+
maps
+
notifications
+
Commercial job completion boundary
```

Primary topics:

- work orders
- scheduling
- appointments
- dispatch
- recurring services
- forms/checklists
- repairs
- fleet
- trade compliance
- field-worker UI
- customer status
- job completion events
- invoice-readiness handoff
- offline/mobile implications
- AI operations tools
- MagicAI host integration

---

# Evidence sources

## WorkCore Commercial

```text
native-extensions/WorkCoreCommercial/extension.manifest.json
native-extensions/WorkCoreCommercial/System/WorkCoreCommercialServiceProvider.php
native-extensions/WorkCoreCommercial/config/workcore-commercial.php
packages/workcore-commercial/files.sha256.json
packages/workcore-commercial/docs/original-composition-manifest.json

packages/workcore-commercial/src/Domains/WorkCore/System/Modules/Finance/WorkCoreFinanceServiceProvider.php
packages/workcore-commercial/src/Domains/WorkCore/System/Modules/Finance/config/titan-money.php
packages/workcore-commercial/src/Domains/WorkCore/System/Modules/Finance/config/permissions.php
packages/workcore-commercial/src/Domains/WorkCore/System/Modules/Finance/Support/MoneyDomainCatalogue.php
packages/workcore-commercial/src/Domains/WorkCore/System/Modules/Finance/Repositories/EloquentFinanceRepository.php
packages/workcore-commercial/src/Domains/WorkCore/System/Modules/Finance/Repositories/EloquentPaymentOrchestrationRepository.php
packages/workcore-commercial/src/Domains/WorkCore/System/Modules/Finance/Application/Actions/CreateInvoiceFromCompletedJob.php
packages/workcore-commercial/src/Domains/WorkCore/System/Modules/Finance/Integrations/WorkCore/CompletedJobMoneyFlowListener.php
packages/workcore-commercial/src/Domains/WorkCore/System/Modules/Finance/Infrastructure/FailClosed/FailClosedWorkCoreCommercialSource.php
packages/workcore-commercial/src/Domains/WorkCore/System/Modules/Finance/Infrastructure/Payments/PayPalPaymentAdapter.php
packages/workcore-commercial/src/Domains/WorkCore/System/Modules/Finance/AI/FinanceToolRegistry.php

packages/workcore-commercial/src/Domains/WorkCore/System/Modules/Payroll/Providers/WorkPayrollServiceProvider.php
packages/workcore-commercial/src/Domains/WorkCore/System/Modules/Payroll/AI/PayrollToolRegistry.php

packages/workcore-commercial/src/Domains/WorkCore/System/Modules/Inventory/Providers/WorkInventoryServiceProvider.php
packages/workcore-commercial/src/Domains/WorkCore/System/Modules/Inventory/AI/InventoryToolRegistry.php

packages/workcore-commercial/src/Domains/WorkCore/System/Modules/Supply/Providers/WorkSupplyServiceProvider.php
packages/workcore-commercial/src/Domains/WorkCore/System/Modules/Supply/AI/SupplyToolRegistry.php

packages/workcore-commercial/src/Domains/WorkCore/System/Modules/TitanVault/Providers/WorkTitanVaultServiceProvider.php
packages/workcore-commercial/src/Domains/WorkCore/System/Modules/TitanVault/AI/TitanVaultToolRegistry.php

packages/workcore-commercial/src/Domains/WorkCore/System/Modules/TrustAccounting/Providers/WorkTrustAccountingServiceProvider.php

packages/workcore-shared-foundation/src/Domains/WorkCore/Config/workcore.php
native-extensions/WorkCore/config/workcore-native.php
```

## MagicAI 11 host

```text
app/Services/Payment/Enums/PaymentGatewayEnum.php
app/Services/PaymentGateways/TransferService.php
app/Listeners/BankTransferListener.php
app/Models/PaymentProof.php
app/Models/Gateways.php
app/Http/Controllers/Finance/PaymentProcessController.php
routes/panel.php
routes/webhooks.php
database/migrations/2023_06_01_145426_create_gateways_table.php
database/migrations/2019_05_03_000002_create_subscriptions_table.php
app/Providers/AppServiceProvider.php
app/Http/Middleware/VerifyCsrfToken.php
```

---

# Evidence limitations

This is a static source and repository scan.

It did not:

- execute a real WorkCore invoice
- post a real ledger
- reconcile a real bank statement
- create a real PayPal operational checkout
- exercise a provider webhook
- test Australian GST against production accounting software
- run a completed WorkCore work order into Finance
- test a real MagicAI plan entitlement projection
- migrate a live MagicAI Sales ledger
- perform legal review of trust accounting
- perform payroll award/tax calculations
- run full concurrency tests

Where source advertises capability but runtime integration is missing, this report marks the capability incomplete rather than assuming it works.
