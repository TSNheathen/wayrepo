# OntOS customer and Business Module composition evidence

> **Handoff status (2026-08-23):** This investigation was pinned to OntOS `develop` at `17ba8f8` and describes contradictions that existed before the same handoff updated OntOS in commit [`32f6b91`](https://github.com/TechsioCZ/ontos/commit/32f6b91). Read phrases such as “current glossary” and mutable `origin/develop` references below as pre-correction evidence at the pinned commit, not as claims about the repository after `32f6b91`.

Research support for **Decide the OntOS kernel contract**. This note verifies the customer/module correction against first-party local architecture documents, the then-current OntOS `app/`, the Akros deployment snapshot, and the legacy WRShop engine. It deliberately does not inspect `context/ontos/mvp/` or `context/ontos/mvp2/`.

## Executive finding

The correction is supported. **OntOS is the product; Core is an internal OntOS kernel; business capabilities belong to OntOS Business Modules.** Akros must not be modeled as a sibling product consuming a separately shareable Core, and commerce must not be modeled as a family of Akros-only modules. The authoritative app-local architecture on `origin/develop` explicitly makes every MicroVertical a complete, independently deployable Business Module and makes the vertical boundary a non-negotiable physical deployment seam. The current CRM implementation realizes that contract with its own frontend remote, backend entrypoint, schema/migrations, build identity, public URL, and worker. This proves independently deployable module seams; it does not by itself prove that the Shell already degrades gracefully when one deployed module fails.

The sources also support a reusable customer-composition model: a Tenant is the customer/environment isolation boundary, and module activation is already tenant-scoped. They do **not** settle whether production will use one deployment per customer or a shared multi-tenant deployment. Customer composition and physical deployment therefore need separate canonical terms.

Akros is confirmed by the product owner/Head of Engineering as the first commerce customer. N1 is confirmed by the same authority as the next customer and an existing bikeshop on WRShop. The local code strongly corroborates N1-specific bikeshop behavior through dedicated `PohodaSqlN1` and `PohodaServerN1` adapters, an `IS_N1BIKE` runtime branch, and a `VPrBikeShop` catalogue-selection flag, but the snapshot does not independently prove N1's contractual customer status or current production activity.

## Evidence classes

| Class | Meaning in this note |
| --- | --- |
| Canonical OntOS architecture | First-party OntOS `CONTEXT.md`, glossary, architecture pack, ADRs, and current handoff documents. These establish intended vocabulary and boundaries but contain some older product/deployment assumptions. |
| Current first-party implementation | Architecture and code read from OntOS `origin/develop` at `17ba8f8e85cf1e25e4959f7e28c910dca43f0f2b` (2026-08-19), especially `app/docs/architecture/`, CRM, and generated topology. The checked-out `main` at `1bb6685` is stale and is not used as authority for the current module topology. |
| Production/deployment evidence | The captured Akros package. It proves deployed code shape, not database-controlled activation or traffic. |
| Legacy behavior evidence | WRShop source, including customer-specific adapters. It proves implemented behavior and useful acceptance scenarios, not the target architecture or current use. |
| User-confirmed fact | A fact stated by the product owner/Head of Engineering in the live Wayfinder exchange but not fully established by the checked-in sources. |

## 1. OntOS, Core, and Business Modules

### Confirmed contract

- OntOS is explicitly named as “the system being built”; its product-context glossary describes it as an ERP with platform-shaped foundations (`origin/develop:docs/CONTEXT.md:3-16`). Core is therefore part of OntOS, not a peer product.
- An OntOS Business Module is a product/business capability **in OntOS**, normally implemented as an UltraModern.js MicroVertical and exposing an OntOS Module Manifest (`origin/develop:docs/CONTEXT.md:43-60`; `origin/develop:docs/02_GLOSSARY.md:15-29`).
- A Foundational Module models shared business reality needed by multiple modules but deliberately remains outside Core because its domain concepts evolve through customer discovery (`origin/develop:docs/CONTEXT.md:51-57`). This is the correct category for genuinely shared business concepts; they do not become Core merely because several modules need them.
- Core owns tenant/legal-entity and principal context, authentication binding, authorization integration, policy hooks, tenant module state, governed Actions, audit/events/outbox, media/search/reporting foundations, worker checkpoints, projections, and observability (`origin/develop:docs/06_CORE_KERNEL.md:1-9`). Core must not own product-specific behavior, and the architecture explicitly rejects generalizing business logic from apparent similarity before a second real use case proves identical meaning (`origin/develop:docs/06_CORE_KERNEL.md:29-33,49-51`).
- Modules own explicit domain tables and behavior; Core owns cross-cutting runtime guarantees (`origin/develop:docs/03_ARCHITECTURE_OVERVIEW.md:21-31`; `origin/develop:docs/08_CANONICAL_ENTITY_MODEL.md:9-49`). Cross-module consumption is through public contracts, not another module's tables, private handlers, routes, migrations, or utilities (`origin/develop:docs/05_MICROVERTICALS.md:75-87`).

### Existing Wayfinder contradiction

At the start of this Wayfinder session, the Akros glossary said Akros was a sibling of OntOS, Core was a shared foundation consumed by both products, and Akros Commerce Modules were not OntOS Business Modules. The then-current commerce-foundation decision repeated that shape. Those pre-correction statements contradicted both the canonical OntOS sources and the newly confirmed product direction and were superseded in Wayrepo commit `ae1fe87`.

The corrected relationship is:

```text
OntOS product
├── Core (small, mandatory system kernel)
├── OntOS Business Modules / Foundational Modules
│   ├── ticketing
│   ├── commerce capabilities
│   ├── ERP capabilities
│   └── shared business capabilities where semantics truly match
├── Shell / back-office composition
└── optional channel applications, including customer storefronts
```

Commerce is one reusable Application Composition: a dependency-closed DAG of OntOS Foundational and Business Modules. Akros and N1 are Customer Configurations of that composition, not owners of forked kernels or customer-named commerce domain implementations.

## 2. Current `app/` architecture and implementation

The app-local architecture on `origin/develop` is explicit and authoritative: its agent contract says `app/docs/` governs implementation and wins over conflicting repository-level context (`origin/develop:app/AGENTS.md:1-19`).

- Every MicroVertical is defined as a complete, independently deployable Business Module that owns its domain model, database schema/migrations, repositories, Effect services, BFF contract/implementation/client, and feature UI (`origin/develop:app/docs/architecture/MICROVERTICALS.md:1-8`).
- The vertical seam is a strict physical deployment seam. Every MicroVertical must be deployable to its own server/process; changing co-location requires deployment configuration or adapter selection, not changes to consuming business logic. Cross-vertical implementation imports, database/repository access, internal service calls, and shared transactions are forbidden (`origin/develop:app/docs/architecture/MICROVERTICALS.md:10-42`).
- Synchronous cross-module calls use the provider's published Effect client; asynchronous communication uses published Outbox schemas; every receiver authenticates and authorizes independently even when co-located (`origin/develop:app/docs/architecture/MICROVERTICALS.md:23-37`).
- Deployment identity (`appId`) and business-capability identity (`moduleId`) are distinct. V0 supports one `business_module` per MicroVertical deployment, while installation and per-tenant activation are separate (`origin/develop:app/docs/architecture/MODULE_MANIFESTS.md:1-15,17-34`).
- The app-local manifest documentation explicitly says the older repository-level, jointly deployed model must be reconciled and that the app-local contract is authoritative for independently deployed MicroVerticals (`origin/develop:app/docs/architecture/MODULE_MANIFESTS.md:125-131`).

CRM is the current concrete proof:

- The Shell's topology lists CRM as its remote, while CRM has its own Module Federation remote and Effect backend-federation delivery unit (`origin/develop:app/topology/reference-topology.json:1-40,153-205`).
- CRM has a Cloudflare worker, Node remote adapter, independently versioned UI/API build boundary, typed fallback, public URL, readiness endpoint, and separate delivery-unit identity (`origin/develop:app/topology/reference-topology.json:206-277,278-303`).
- CRM's manifest declares `crm.core` as a tenant-scoped `business_module` with its own Actions, APIs, pages, and Shell contributions (`origin/develop:app/verticals/crm/vertical.manifest.ts:1-83,95-228`).
- CRM owns a separate `crm` database schema and migration history. Its `customers` and `contacts` tables are tenant-scoped and protected by governed row-level security (`origin/develop:app/README.md:123-129`; `origin/develop:app/verticals/crm/src/db/schema.ts:1-21,27-69,71-111`).
- Authentication remains Shell/Core, never a MicroVertical. The current app-local contract permits one BetterAuth user to hold active bindings to multiple tenant-scoped Principals while exactly one active tenant is selected and revalidated per session (`origin/develop:app/docs/architecture/MICROVERTICALS.md:96-116`). This is logical tenant support, not a physical deployment choice.

CRM currently names its aggregates `Customer` and `Contact`, whereas the root OntOS vocabulary distinguishes Party, Counterparty, Principal, and Legal Entity. CRM therefore proves the owner-local Business Module/data seam, not that `Customer` is the final cross-module identity model. The CRM/Party Registry relationship needs explicit domain reconciliation before commerce depends on it.

**What this proves:** one modular monorepo, explicit Core-vs-Business-Module ownership, independent deployability as an architectural invariant, owner-local schemas/transactions, and contract-only communication across verticals.

**What it does not prove:** independent deployability does not require every module to run on a separate host at all times, and it does not prove the desired failure behavior has passed tests. Nor does it decide whether customer deployments and data planes are isolated per customer or shared across tenants. Graceful module-failure behavior and customer deployment topology require separate decisions and proof. At pinned commit `17ba8f8`, the older architecture pack still called the system a jointly deployable modular monolith; the authoritative app-local contract explicitly superseded that implementation rule. Commit `32f6b91` subsequently aligned the root overview while leaving deeper reconciliation to OntOS.

## 3. Customer facts and legacy corroboration

### Akros

**User-confirmed:** Akros is the first customer to receive the OntOS commerce/back-office capability set. Its customer-facing storefront will probably be a separate app, potentially in the same monorepo, while management/back-office behavior is supplied by OntOS Business Modules.

**Source-confirmed:** the Akros capture is a thin customer deployment over the shared WRShop engine: both storefront and CMS bootstrap `engine/wrshop/run.php` (`context/team-1633516729428-akros-124b361ce570/public_html/index.php:1-3`; `context/team-1633516729428-akros-124b361ce570/public_html/cms/index.php:1-4`). Its Nginx configuration serves `www.akros.cz` and `b2b.akros.cz` from the same deployment (`context/team-1633516729428-akros-124b361ce570/conf.d/nginx.conf:1-8`). Its business-system client exposes catalogue, prices, customers, categories, orders, invoices, relations, and stock exchanges (`context/team-1633516729428-akros-124b361ce570/project/is/client/client.php:28-198`). This proves broad implemented/deployed seams, not which are active.

No checked-in source independently says “Akros is the first OntOS customer.” That ordering remains a user-confirmed roadmap fact.

### N1 bikeshop

**User-confirmed:** N1 is the next concrete customer after Akros and is already a customer on the old legacy engine.

**Strong legacy corroboration:** WRShop contains two N1-specific Pohoda integration families. `PohodaSqlN1` defines separate `IS_N1`, `IS_N1BIKE`, and `IS_N1PRE` branches and schedules product, stock, price, image, category, related-product, producer, parameter, customer balance, and individual-price imports (`context/team-1633516729428-engine-a6883ea926b4/wrshop/Module/Is/PohodaSqlN1/BaseControl.php:19-81`). The N1 Bike branch selects only source products whose `VPrBikeShop` flag is set and derives quantity from stock minus reservations (`context/team-1633516729428-engine-a6883ea926b4/wrshop/Module/Is/PohodaSqlN1/Manager/ProductManager.php:37-68,97-169,182-201`). The companion server integration exports customers and orders to the N1/Pohoda boundary (`context/team-1633516729428-engine-a6883ea926b4/wrshop/Module/Is/PohodaServerN1/Control/CustomerControl.php:20-79`; `context/team-1633516729428-engine-a6883ea926b4/wrshop/Module/Is/PohodaServerN1/Control/OrderControl.php:100-159`). N1 also performs checkout-time stock refresh/validation against its source (`context/team-1633516729428-engine-a6883ea926b4/wrshop/Module/Is/PohodaSqlN1/Service/BasketValidationService.php:16-69`).

This is strong evidence of implemented N1 bikeshop-specific behavior. The snapshot does not contain a customer contract, deployment configuration, database, telemetry, or operator statement. It therefore cannot independently prove current production activity, commercial status, or exact migration scope.

## 4. Business-capability overlap

The overlap supports reusable OntOS Business Modules and Foundational Modules, but not a larger Core. The following conclusions separate demonstrated common semantics from mere naming similarity.

| Capability | Evidence | Recommended boundary |
| --- | --- | --- |
| Product / Catalog | WRShop's central product model has explicit product, localized product, visibility, availability, producer, attachment, image, category, package, relationship, price, store-quantity, feed, and configuration tables (`context/team-1633516729428-engine-a6883ea926b4/wrshop/Manager/ProductManager.php:8-39`). Akros and N1 both use this shared engine, and N1 overlays customer/source-specific import policy. OntOS's later manufacturing direction also names products and BOM/material reservations (`origin/develop:docs/11_V0_SCOPE_AND_MODULES.md:65-67`). | A reusable OntOS Catalog/Product Business Module is strongly justified, with customer policy/configuration and Connector mappings outside its canonical identity. It is not Core. Exact separation between Catalog, Assortment, Content, and Product Configuration still needs a module-boundary decision. |
| Storage / Inventory / Availability | N1 source tables distinguish store, stock, product stock, set stock, price, and reservation-adjusted quantity (`context/team-1633516729428-engine-a6883ea926b4/wrshop/Module/Is/PohodaSqlN1/Manager/ProductManager.php:9-23,97-169`), and checkout refreshes stock without making the adapter the cart owner (`context/team-1633516729428-engine-a6883ea926b4/wrshop/Module/Is/PohodaSqlN1/Service/BasketValidationService.php:16-69`). OntOS ERP/manufacturing planning requires material reservation (`origin/develop:docs/11_V0_SCOPE_AND_MODULES.md:65-67`). | A shared Inventory/Storage Business Module is plausible where ERP and commerce truly use the same stock, location, reservation, and movement semantics. Availability/sellability remains a commerce promise derived from inventory and external facts. Rich ERP warehouse UI is a presentation/workflow difference, not evidence for a second stock authority. |
| Product translations / localization | The legacy product model separates `product` and `product_lang`; order, payment, shipping, status, country, and price-type reads also select language-specific tables (`context/team-1633516729428-engine-a6883ea926b4/wrshop/Manager/ProductManager.php:14-24`; `context/team-1633516729428-engine-a6883ea926b4/wrshop/Manager/OrderManager.php:12-42,63-77`). Current CRM separately publishes Czech and English locale assets, while the general MicroVertical contract makes localized URLs/UI owner-local (`origin/develop:app/verticals/crm/package.json:1-10`; `origin/develop:app/README.md:20-30`). | Keep UI localization infrastructure separate from translated business content. Product titles/descriptions/attributes belong to Catalog/Content contracts; reusable translation-provider orchestration may be a service/Connector, not the System of Record and not automatically Core. |
| Pricing | OntOS property scope already names pricing but assigns rent/reservation policy outside Core (`origin/develop:docs/01_CONTEXT_AND_CONSTRAINTS.md:9-11`; `origin/develop:docs/06_CORE_KERNEL.md:29-33`). Legacy commerce has prices, price types, discounts, currencies, VAT, quantity breaks, and N1 individual customer pricing (`context/team-1633516729428-engine-a6883ea926b4/wrshop/Manager/PriceManager.php:8-17,40-77`; `context/team-1633516729428-engine-a6883ea926b4/wrshop/Module/Is/PohodaSqlN1/Control/PriceIndividualControl.php:94-169`). | Pricing must be an OntOS Business Module or a set of domain modules. Do not assume rental pricing and commerce selling-price calculation are one model merely because both say “price”; prove shared concepts and invariants before extracting a Foundational Module. |
| Party / Counterparty | OntOS already defines Party and Counterparty across suppliers, corporate buyers, wholesalers, tenants, guests, and external managers (`origin/develop:docs/CONTEXT.md:107-121`; `origin/develop:docs/02_GLOSSARY.md:103-125`). Wayfinder proposed Party Registry for their shared identity boundary. | Party Registry is a plausible Foundational Module, not an Akros service and not Core, but its ownership and relationship to the current CRM Customer model require OntOS confirmation in [TechsioCZ/ontos#95](https://github.com/TechsioCZ/ontos/issues/95). Core retains Principal/authentication/authorization context; modules own role-specific profiles and policies. |
| Orders | Legacy commerce has a rich commerce Order aggregate and N1-specific outbound order conversion (`context/team-1633516729428-engine-a6883ea926b4/wrshop/Manager/OrderManager.php:8-47`; `context/team-1633516729428-engine-a6883ea926b4/wrshop/Module/Is/PohodaServerN1/Control/OrderControl.php:100-159`). OntOS future manufacturing mentions production orders (`origin/develop:docs/11_V0_SCOPE_AND_MODULES.md:65-67`). | Commerce Order is reusable across commerce customers, but “order” is not one universal ERP aggregate. Purchase Order, Sales Order, Production Order, Work Order, and commerce Order need distinct ownership unless a later domain analysis proves a shared contract. |
| Documents / evidence | Core owns media/evidence infrastructure, while `documents.center` owns document-specific classification, expiration, versions, permissions, and workflows (`origin/develop:docs/06_CORE_KERNEL.md:7-27`; `origin/develop:docs/11_V0_SCOPE_AND_MODULES.md:43-45`). Akros integrations already exchange invoices/documents (`context/team-1633516729428-akros-124b361ce570/project/is/client/client.php:154-198`). | Share the Core media/evidence substrate and an OntOS Documents Business Module. Invoice/credit-document authority remains with its issuing financial system or billing module; a document module must not acquire business authority merely by storing bytes. |
| Reporting | Core supplies search/reporting foundations, while each module declares report descriptors and owns report meaning (`origin/develop:docs/06_CORE_KERNEL.md:7-27,41-45`; `origin/develop:docs/05_MICROVERTICALS.md:15-25,75-87`). `reporting.basic` composes domain facts (`origin/develop:docs/11_V0_SCOPE_AND_MODULES.md:51-53`). | Core owns report execution/discovery plumbing; modules own metrics and read models; a reporting Business Module may compose cross-module operational views without becoming the canonical writer. |
| Back-office operations | A MicroVertical owns its full UI/backend business slice, while every Action, page, API, public component, search provider, report, and worker is a structured Core-governed entrypoint (`origin/develop:app/docs/architecture/MICROVERTICALS.md:1-8,44-65`; `origin/develop:app/README.md:27-46`). CRM contributes its pages/navigation to the Shell through its tenant-scoped manifest (`origin/develop:app/verticals/crm/vertical.manifest.ts:29-50,95-228`). | OntOS Shell/back office composes enabled Business Modules. “Commerce Operations” is a customer/operator experience over those Actions, not a parallel product and not an alternate direct-write admin system. |
| Integrations | Core owns transactional outbox/worker foundations; provider-specific export shape remains a module/integration responsibility (`origin/develop:docs/06_CORE_KERNEL.md:21-33`; `origin/develop:docs/04_C4_MODEL.md:23-25,43-45`). Akros and N1 demonstrate different ABRA/POHODA mappings over shared commerce behavior. | Keep durable dispatch, identity, audit, and worker foundations in Core. Put provider-neutral business handoff contracts in the owning Business Module. Prefer Symmy–Provider Integrations where Symmy supplies the business-system route; use an owner-local Direct Provider Adapter for provider families outside Symmy or a missing Symmy route. |

## 5. Confirmed composition terminology

The OntOS glossary defines **Tenant**, **Tenant Module State**, **OntOS Business Module**, **Foundational Module**, and module/runtime entrypoints, but it does not name reusable application composition, customer-specific configuration, or lifecycle environment independently from physical deployment. The product/architecture handoff confirms the following terms:

### Application Composition

A named, reusable, dependency-closed directed acyclic graph of OntOS Foundational and Business Modules serving a coherent business purpose. It defines required modules, permitted optional modules, and their dependency rules. Commerce is one Application Composition shared by Akros, N1, and later commerce customers.

### Customer Configuration

A customer-specific declarative configuration of an Application Composition. It may select permitted optional modules and define customer policies, settings, branding, locales, Connectors, and integration participation. It must not fork Core, change shared module contracts, or create customer-specific module implementations. Akros and N1 are Customer Configurations of the Commerce Application Composition.

### Environment

A topology-neutral lifecycle context in which a Customer Configuration operates, such as Production, Staging, or Development. Environment identity does not imply geography, data residency, isolated deployment, or shared multi-tenancy.

### Channel Application

A customer- or partner-facing application that composes public Business Module contracts for a channel, such as an Akros Storefront. It may live in the OntOS monorepo and deploy separately, but it owns presentation/journey concerns rather than canonical domain state. The Shell/back office is another composition surface, not the only permissible application.

### Module Delivery Unit

The versioned frontend/backend artifact through which an OntOS Business Module can be built, deployed, addressed, health-checked, and rolled back. It is an implementation/deployment concept, not the Business Module itself. The current topology uses `kind: "microvertical-delivery-unit"` and binds CRM UI/API to one build identity (`origin/develop:app/topology/reference-topology.json:168-205,269-277`).

### Deployment Topology

The physical mapping of Customer Configurations, Environments, Tenants, Application Composition modules, data stores, workers, and Channel Applications onto running infrastructure. Deployment Topology decides isolation, shared multi-tenancy, infrastructure placement, and regional or residency constraints without changing module ownership or creating a Core fork.

These terms prevent three recurring category errors: equating a commercial customer with a Tenant or CRM Customer, equating Customer Configuration with a product fork, and equating Application Composition or Environment with Deployment Topology.

## 6. Decisions this evidence supports

1. **Supersede the sibling-product premise.** OntOS is the only product in this architecture. Core is its small mandatory kernel. Commerce capabilities are OntOS Business Modules/Foundational Modules used first for Akros and next for N1.
2. **Keep customer names out of reusable module identities.** Use declarative Customer Configurations, policies, Channel Applications, and Connectors for Akros/N1 specialization.
3. **Preserve the strict Core boundary.** Authentication, authorization, tenant/legal-entity/principal context, module state, governed Actions, audit/events/outbox, media/evidence, search/reporting foundations, worker mechanics, and cross-module references belong in Core. Commerce, ERP, catalogue, inventory, pricing, documents workflow, orders, and provider mappings do not.
4. **Treat Akros as the first proof, not the sole semantics owner.** N1 is already a concrete second commerce customer, and its Pohoda/bikeshop behavior should inform module acceptance scenarios immediately.
5. **Preserve both dependency closure and independent deployability.** The Commerce Application Composition is a dependency-closed DAG, while every MicroVertical remains independently deployable even when several are co-located. Separately defer per-customer isolated deployment versus shared multi-tenancy; this does not weaken the vertical seam.
6. **Reconcile source contradictions explicitly.** At pinned commit `17ba8f8`, the older OntOS pack put e-shop outside OntOS and described one jointly deployable modular monolith, while the authoritative app-local architecture mandated independently deployable modules and the newly confirmed product direction put commerce modules inside OntOS. Commit `32f6b91` performed the first root-document alignment; the remaining decision-record and implementation gaps are now tracked in the linked OntOS architect-review issues.

## Remaining unknowns

- Whether one commercial Customer may own multiple OntOS Tenants, and whether Akros/N1 each map to exactly one Tenant.
- Which commerce capabilities are Foundational Modules versus ordinary Business Modules; similarity alone is not enough.
- Whether Product/Catalog and Inventory/Storage become single modules or smaller cooperating modules.
- Which domain owns translated product content versus editorial/storefront content.
- Whether Storefronts share one application codebase with tenant branding/configuration or use separate Channel Applications.
- Whether Production Environments use isolated deployments per Customer Configuration, shared multi-tenancy, or a supported mix.
- What failure-isolation SLO the Shell must provide when a Module Federation remote or module backend is unavailable.
- Which N1 legacy behaviors are active and must migrate; the source snapshot does not answer runtime state.
