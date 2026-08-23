# Akros data authority and integration contracts

Decision asset for **[Define Akros data authority and integration contracts](https://github.com/TSNheathen/wayrepo/issues/8)**.

## Authority and scope

Accepted OntOS terminology and architectural rules have highest authority. This decision specializes them for Akros; it does not create a parallel architecture.

The decision prepares the Akros side of each integration contract. Discovering the systems behind Symmy, choosing provider products, and defining fields, endpoints, schedules, queues, exact backoff, or deployment topology are not prerequisites. Current Akros/WRShop and Symmy supply evidence, not target architecture.

Symmy is an optional, replaceable Integration Hub alongside direct Connectors. A fact passing through Symmy does not give Symmy authority over it. ABRA is not an assumed dependency, and the WRShop SOAP/FTP, AbraG3, AbraApi, and other legacy Connector implementations do not survive the migration. Their business rules, evidence, and required mappings may survive through governed replacement contracts.

## Canonical authority

Authority is assigned per fact or lifecycle transition. Storefront and Commerce Operations compose governed experiences; neither is a System of Record.

| Fact or transition | System of Record |
| --- | --- |
| Party identity and deduplication | Party Registry |
| Counterparty relationship | Party Registry |
| Authentication credentials and sessions | BetterAuth, under the higher-authority OntOS architecture |
| Principal binding, authorization context, and audit identity | Core |
| Retail profile, B2B eligibility, buyer/approver roles, purchasing limits, and preferences | Akros commerce domains |
| Channel access and purchasing policy | Akros Commerce Policy |
| Product identity, variants/configurations, classification, and relationships | Catalog |
| Channel/Counterparty visibility and sellability | Assortment plus Akros Commerce Policy |
| Akros-authored descriptions, media, merchandising, navigation, legal pages, and SEO | Akros Content |
| Imported source fact | Its declared issuer for the raw fact; the receiving Akros domain owns its validated commerce representation |
| Applicable selling price, discount, fee, tax input, and quotation | Pricing |
| Accepted price and tax | Immutable Order snapshot |
| Externally reported physical stock | Its declared issuer |
| Commerce-created reservation | Inventory |
| Customer-facing sellability and delivery promise | Availability |
| Accepted purchase and commercial snapshot | Order |
| External processing acceptance, rejection, or reference | The receiving system for its outcome; Order records the correlated result |
| Statutory Invoice or credit document | The issuing financial system |
| Customer-facing financial-document access | Akros projection or Customer Archive |
| Provider transaction and settlement outcome | Payment provider |
| Order-level payment state and reconciliation | Payment |
| Physical warehouse or carrier event | The executing system |
| Consolidated customer-facing preparation, delivery, tracking, and exception lifecycle | Fulfillment |
| External identifier | Its issuing system |
| Correlation between Akros and external identifiers | Connector Registry |

Externally supplied data exposes its issuer and freshness. Commerce Operations may invoke permissioned correction or recovery Actions but never acquires authority by doing so.

## Required Akros-side flows

| Area | Required flow and boundary rule |
| --- | --- |
| Product and Catalog | Authoritative source facts arrive through governed imports; validated canonical commerce projections may be published outward. |
| Assortment and Content | Akros-owned publication flows outward. External input is accepted only from an explicitly declared source and cannot silently replace local merchandising or content. |
| Pricing | Declared price inputs arrive at Pricing, which derives contextual offers. Order freezes accepted terms. |
| Inventory and Availability | Source stock arrives as a versioned fact. Akros reservations cannot be overwritten by a stock import, and Availability derives the sell-and-deliver promise. |
| Party and Counterparty | Identity exchange is governed in both directions. Matching, merge, and correction pass through Party Registry; Akros purchasing roles and policy cannot be overwritten externally. |
| Order | A confirmed Order is handed off after commit. Correlated acceptance, rejection, processing, and later lifecycle outcomes return through governed Actions. |
| Invoice and credit document | The issuer provides canonical metadata and an authorized Evidence Artifact reference or copy for Akros access. |
| Payment | Akros sends an idempotent request where needed; authorization, settlement, cancellation, refund, and reconciliation outcomes return to Payment. |
| Fulfillment | Akros sends an execution request; handoff, tracking, delivery, return, and exception outcomes return to Fulfillment. |
| External identifiers | The issuer returns its identifier; the Connector Registry establishes correlation before any dependent outcome is accepted. |
| Feeds, email, and measurement | Akros publishes derived facts. Delivery or processing outcomes return when they are operationally significant. |

Omission is never interpreted as deletion. Retirement, cancellation, correction, and other destructive meanings require explicit lifecycle operations.

## Connector contract

The Akros Integration Boundary supports three routing choices per flow: a direct Connector, the optional Symmy Connector, or both under an explicit coexistence rule. Provider selection and implementation placement follow later; neither may weaken these guarantees.

Every import is a source-identified, versioned fact submitted through a CoreSDK-governed domain Action. Every outbound handoff is created durably after the originating Action commits. Every callback or external outcome is a correlated, idempotent domain Action rather than a direct state overwrite. Document delivery uses authorized Evidence Artifact references. Backfill uses the same validated contract as live traffic, and reconciliation reports differences without silently changing canonical facts.

The minimum operational guarantees are:

- a stable business idempotency key for every externally caused operation;
- exact-result replay for the same key and content, with conflicting content rejected;
- independent durable outcomes for batch items;
- no automatic retry of business rejection;
- bounded retry of transient failure followed by visible recovery work;
- replay of the immutable original operation without a second business effect;
- resumable, checkpointed, counted, and reconciled backfill;
- reconciliation by identifiers, lifecycle state, counts, and financial totals where applicable;
- quarantine of stale or conflicting authoritative facts rather than arrival-order overwrite; and
- durable confirmed Orders regardless of outbound-system availability.

## Runtime and recovery boundary

Browsing and Checkout use validated local projections rather than synchronously depending on Symmy. Price, tax, Availability, and eligibility are revalidated before confirmation; a launch-critical fact that is too stale fails clearly before confirmation. Required payment authorization may interact synchronously with its provider through an idempotent attempt. Order handoff and other post-confirmation exchanges are asynchronous.

Commerce Operations exposes freshness, backlog, attempts, ownership, and reconciliation state. Manual retry, replay, backfill, correction, or abandonment requires permission, preview, reason, and audit attribution. High-risk recovery follows OntOS authorization and policy, including approval where the accepted policy requires it.

## Downstream decisions

- Commerce-foundation selection decides where the contracts are implemented.
- Critical-workflow and consistency design supplies concrete lifecycle state machines and test seams.
- Migration and cutover decide coexistence, open-obligation treatment, mapping retention, rollback, and legacy shutdown.
- The later specification defines provider products, field schemas, APIs, events, schedules, infrastructure, operational thresholds, and runbooks.
