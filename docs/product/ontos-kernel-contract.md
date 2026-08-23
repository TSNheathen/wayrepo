# OntOS kernel contract

Decision asset for **[Decide the shared Ontos kernel contract](https://github.com/TSNheathen/wayrepo/issues/10)**.

## Product model

OntOS is the product. Core is its small mandatory kernel; it is not a separately consumed product, a customer runtime, or a forkable foundation. OntOS Foundational and Business Modules own business meaning and compose into applications.

**Commerce** is one reusable Application Composition: a versioned, dependency-closed directed acyclic graph of Foundational and Business Modules. **Akros** and **N1** are declarative Customer Configurations of that same composition. Customer variation uses permitted modules, policies, settings, branding, locales, Connectors, and Channel Applications. It never creates a Core fork, a module fork, or customer-named copies of reusable implementations.

Production, Staging, and Development are topology-neutral Environments. Whether Customer Configurations are isolated, multi-tenant, regionally placed, or combined remains a Deployment Topology decision.

Composition vocabulary: [OntOS application-composition vocabulary](./ontos-application-composition-vocabulary.md).

## Core admission rule

A capability belongs in Core only when it is a business-neutral enforcement or runtime mechanism required by OntOS modules generally. Core hides that complexity behind a small interface. Similar domain names, shared screens, or use by several modules do not qualify business meaning for Core.

Core owns:

- trusted Principal, Tenant, and operation-declared optional/required/forbidden Legal Entity context;
- non-secret authentication bindings, BetterAuth integration, SpiceDB authorization integration, and business-policy enforcement hooks;
- governed Action and read execution, operation identity, transaction scope, authorization, evidence, and typed outcomes;
- module manifests, installed-module catalog, module state, structured entrypoint gates, and generic Application Composition dependency validation;
- Action invocation and idempotency records, audit and data-access evidence, domain-event and outbox infrastructure;
- worker matching, leases, recovery, retry/backoff, dead-letter state, and checkpoints;
- the minimal ResourceRef convention;
- generic media storage/access and Evidence Registry infrastructure;
- generic search/report registration, execution, authorization, and observability mechanisms; and
- common operational telemetry needed to diagnose and recover those mechanisms.

Shell owns BetterAuth credentials, sessions, cookies, and other secret provider mechanics. Core owns the non-secret bindings and trusted operational identity needed by governed work. Authentication is a Shell/Core capability, never an Auth Business Module.

Business Modules own:

- domain models, invariants, lifecycles, Actions, reads, policies, schemas, migrations, repositories, events, reports, search documents, UI, and tests;
- Party/Counterparty meaning, Catalog/Product, translated product content, Inventory/Storage, Availability, Pricing, purchasing, Orders, Payments, Fulfillment, Documents workflows, CRM, Ticketing, property/rental, finance, accounting, and every other business capability;
- media purpose, document classification and workflows, retention classifications, search meaning/ranking, report definitions, and financial correctness; and
- provider-neutral business handoff contracts and the validated local representation of externally supplied facts.

Foundational Modules remain outside Core even when most Application Compositions use them. Party Registry, Documents, or a future shared Product/Inventory capability can evolve with customer discovery without expanding the kernel.

## Core interface and module seam

Modules cross Core through typed descriptors and governed entrypoints. Callers provide declared input and trusted transport context; Core performs context resolution, dependency/module gates, authorization, policy evaluation, transaction setup, evidence, and result validation before releasing an outcome. Business handlers receive only owner-local scoped interfaces, never Core repositories or a general database executor.

Every MicroVertical remains independently deployable, even when several are co-located:

- `moduleId` identifies the stable business capability; deployment identity remains separate;
- one module cannot import another module's implementation, schema, repository, migration, handler, or executable policy;
- synchronous calls use the provider's published typed client;
- asynchronous calls use published event/outbox contracts;
- modules never share a business transaction or write another module's tables; and
- each trust boundary independently authenticates, gates, authorizes, and validates the request.

This supersedes the older jointly deployed modular-monolith assumption. It does not decide customer isolation, multi-tenancy, geography, repository placement, or whether independently deployable modules are always placed on separate infrastructure.

## Application Composition enforcement

The Commerce Application Composition owns its versioned dependency DAG. Core understands only the generic graph contract, not commerce meaning.

Core enforces that:

- the graph is acyclic and references installed, compatible module contracts;
- a Customer Configuration selects only required or permitted optional modules;
- a module can be activated only when its required dependencies are installed and active;
- installation, activation, and entrypoint execution preserve dependency closure; and
- an unavailable dependency produces an explicit unavailable/degraded result for affected entrypoints without rewriting or cascading persisted module states or disabling unrelated modules.

This dependency contract supersedes the current app-local rule that module lifecycles can never depend on another module's installation or activation.

## Action idempotency and durable work

Core guarantees business-effect duplicate prevention: the same idempotency identity and request content cannot commit the governed operation twice, while reuse with conflicting content is rejected. Core durably records the invocation state and enough identity/evidence to establish whether the effect committed.

Core does not store arbitrary business response payloads. The owning module returns a stable ResourceRef or operation receipt and reconstructs a repeated response from canonical state when its interface requires exact replay. External handoff interfaces may persist their own bounded result records when a repeated acknowledgement must be byte-for-byte or semantically identical.

An outbound effect begins only from committed canonical state and a durable outbox record. Core worker infrastructure owns matching, leases, abandoned-claim recovery, bounded retry/backoff, dead-letter state, and checkpoints. The consuming Business Module owns its handler semantics and must make an external effect safe under redelivery.

## ResourceRef

ResourceRef contains only Tenant, owning module, resource type, and resource ID. It provides stable cross-module addressability without transferring ownership.

- Legal Entity is trusted operational context, not part of resource identity.
- Core gates resolution and authorization but does not own resource lifecycle or relationship meaning.
- Labels, navigation, relationship types, external identifiers, provider status, and display snapshots remain module-owned.
- A provider identifier belongs in the Connector Registry with its provenance; it never becomes ResourceRef identity.

## Media, evidence, search, and reporting

Core owns generic mechanisms:

- content-addressed media metadata, storage adapters, authorized access, and safe links;
- Evidence Registry records, hashes, provider immutability verification, legal-hold/retention enforcement primitives, and audited access;
- permissioned provider discovery and execution for search and reports; and
- freshness, failure, rebuild/recovery, and observability contracts for shared execution.

Business Modules declare purpose and meaning. Product media, attachments, contracts, invoices, merchandising, classification, retention policy, search documents/facets/ranking, report calculations, and financial reconciliation remain with their owning domains.

## Symmy and External Business Systems

Symmy is outside Core. OntOS crosses one **Symmy Connector** seam. Owning Business Modules publish provider-neutral business handoff contracts; the Symmy Connector adapts those contracts to Symmy without acquiring business authority. Provider-specific behavior remains downstream in named integrations such as Symmy–POHODA Integration or Symmy–ABRA Integration.

N1's current direct POHODA integration is legacy behavior and migration evidence, not the target OntOS interface. No POHODA, ABRA, HELIOS, Symmy, commerce, or ERP schema enters Core.

## Production-commerce gates

Commerce may rely on Core in production only after evidence proves:

- Application Composition DAG validation and runtime enforcement;
- Tenant and Legal Entity isolation, including row-level-security failures and cross-context negative cases;
- fail-closed authentication, authorization, policy evaluation, and production secret configuration;
- independent module outage/degradation without unrelated module failure;
- Action duplicate prevention and request-content conflict handling;
- worker lease expiry, abandoned-claim recovery, retry/backoff, dead-letter operations, governed replay, and recovery observability;
- cross-Tenant ResourceRef denial and authorized unavailable/deleted behavior;
- production object storage, media processing/access, evidence immutability, retention, legal hold, and audit behavior; and
- permission-safe search/report execution and recovery.

Commerce lifecycle state machines, compensation, cross-module workflow consistency, provider reconciliation, and cutover guarantees remain with their dedicated decisions. They consume Core mechanisms without becoming Core responsibilities.

## Explicitly rejected or deferred

Rejected from Core:

- customer or module forks;
- Commerce, ERP, Ticketing, CRM, property/rental, or provider-specific semantics;
- a generic entity/EAV store, workflow engine, relation registry, plugin marketplace, or commerce engine;
- direct cross-module database access, shared business transactions, synchronous dual writes, or private implementation imports; and
- deployment-provider, geography, or customer-isolation assumptions.

Deferred to downstream decisions:

- the exact Commerce module DAG and module splits;
- Storefront and other Channel Application placement;
- per-customer isolation versus shared multi-tenancy and regional placement;
- commerce workflow and consistency mechanics;
- migration/coexistence/cutover; and
- production acceptance thresholds and operational runbooks.

## Source reconciliation

Canonical OntOS product/domain vocabulary remains the highest authority. For current implementation behavior, the newer app-local architecture on `origin/develop` governs where it explicitly supersedes the older jointly deployed documents. This decision adds the confirmed commerce/customer composition and corrects earlier Wayfinder statements that treated Akros as a sibling product, Core as separately shared, commerce modules as Akros-only, Symmy as optional, or direct provider Connectors as target architecture.

Evidence: [OntOS customer and Business Module composition](../research/ontos-customer-module-composition-evidence.md) and [Czech ERP families and the OntOS commerce boundary](../research/czech-erp-families-ontos-commerce-boundary.md).
