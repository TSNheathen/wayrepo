# Akros data authority and integration contracts

Decision asset for **[Define Akros data authority and integration contracts](https://github.com/TSNheathen/wayrepo/issues/8)**.

## Authority and scope

Accepted OntOS terminology and architectural rules have highest authority. Commerce is one OntOS Application Composition; Akros and N1 are Customer Configurations of it. This decision specializes integration authority for the Akros configuration without creating a parallel product or architecture.

The decision prepares the OntOS side of each commerce integration contract. Choosing provider products and defining fields, endpoints, schedules, queues, exact backoff, or Deployment Topology are not prerequisites. Current Akros/WRShop integrations supply evidence, not target implementations.

OntOS crosses one Symmy Connector seam. Provider-specific behavior stays downstream in named integrations such as Symmy–POHODA Integration or Symmy–ABRA Integration. A fact passing through Symmy does not give Symmy authority over it. N1's direct POHODA code and the WRShop SOAP/FTP, AbraG3, AbraApi, and other legacy Connector implementations do not survive as target architecture. Their business rules, evidence, and required mappings may survive through governed replacement contracts.

## Canonical authority

Authority is assigned per fact or lifecycle transition. Storefront and Commerce Operations compose governed experiences; neither is a System of Record.

| Fact or transition | System of Record |
| --- | --- |
| Party identity and deduplication | Party Registry |
| Counterparty relationship | Party Registry |
| Authentication credentials and sessions | BetterAuth, under the higher-authority OntOS architecture |
| Principal binding, authorization context, and audit identity | Core |
| Retail profile, B2B eligibility, buyer/approver roles, purchasing limits, and preferences | Owning Commerce Business Modules |
| Channel access and purchasing policy | Commerce policy plus Customer Configuration |
| Product identity, variants/configurations, classification, and relationships | Catalog |
| Channel/Counterparty visibility and sellability | Assortment plus Commerce policy and Customer Configuration |
| Customer-authored descriptions, media, merchandising, navigation, legal pages, and SEO | Owning Content/Catalog module plus Customer Configuration |
| Imported source fact | Its declared issuer for the raw fact; the receiving Business Module owns its validated commerce representation |
| Applicable selling price, discount, fee, tax input, and quotation | Pricing |
| Accepted price and tax | Immutable Order snapshot |
| Externally reported physical stock | Its declared issuer |
| Commerce-created reservation | Inventory |
| Customer-facing sellability and delivery promise | Availability |
| Accepted purchase and commercial snapshot | Order |
| External processing acceptance, rejection, or reference | The receiving system for its outcome; Order records the correlated result |
| Statutory Invoice or credit document | The issuing financial system |
| Customer-facing financial-document access | Commerce projection or Customer Archive |
| Provider transaction and settlement outcome | Payment provider |
| Order-level payment state and reconciliation | Payment |
| Physical warehouse or carrier event | The executing system |
| Consolidated customer-facing preparation, delivery, tracking, and exception lifecycle | Fulfillment |
| External identifier | Its issuing system |
| Correlation between OntOS and external identifiers | Connector Registry |

Externally supplied data exposes its issuer and freshness. Commerce Operations may invoke permissioned correction or recovery Actions but never acquires authority by doing so.

## Required Akros-side flows

| Area | Required flow and boundary rule |
| --- | --- |
| Product and Catalog | Authoritative source facts arrive through governed imports; validated canonical commerce projections may be published outward. |
| Assortment and Content | Customer Configuration publication flows outward through their owning modules. External input is accepted only from an explicitly declared source and cannot silently replace local merchandising or content. |
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

## Symmy Connector contract

OntOS publishes provider-neutral handoff contracts through the Symmy Connector. POHODA, ABRA, HELIOS, and other provider-specific routes remain downstream Symmy–Provider Integrations. Customer Configuration selects integration participation; it cannot replace the Connector seam or weaken these guarantees.

Every import is a source-identified, versioned fact submitted through a CoreSDK-governed domain Action. Every outbound handoff is created durably after the originating Action commits. Every callback or external outcome is a correlated, idempotent domain Action rather than a direct state overwrite. Document delivery uses authorized Evidence Artifact references. Backfill uses the same validated contract as live traffic, and reconciliation reports differences without silently changing canonical facts.

The minimum operational guarantees are:

- a stable business idempotency key for every externally caused operation;
- Core-level prevention of a second committed business effect for the same key and content, with conflicting content rejected;
- module- or Connector-owned reconstruction of the same bounded acknowledgement when its public interface requires repeat-response replay;
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
