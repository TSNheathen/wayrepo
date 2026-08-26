# Akros product and domain boundary model

Decision asset for **[Define the product and domain boundary model](https://github.com/TSNheathen/wayrepo/issues/7)**.

## Direction — corrected by the OntOS kernel decision

OntOS is the product. Core is its small internal kernel, and business capabilities live in reusable OntOS Foundational and Business Modules. Commerce is one Application Composition of those modules. Akros and N1 are declarative Customer Configurations of that same composition; they are not sibling products, module families, or Core forks.

Customer variation belongs in permitted module selection, policy, settings, branding, locales, Connectors, and Channel Applications. Reusable behavior belongs in shared modules. Accepted OntOS terminology and architecture have highest authority.

## Ownership map

| Boundary | Owns | Does not own |
| --- | --- | --- |
| **Core** | Business-neutral OntOS identity, governance, execution, module/composition enforcement, durable-work, evidence, addressing, and shared execution mechanisms | Any commerce, ERP, customer, or provider-specific business meaning |
| **Foundational Modules** | Shared business reality with stable meaning across multiple modules or Application Compositions | Core mechanisms or customer-specific policy |
| **Commerce Business Modules** | Reusable commerce behavior across Catalog, Assortment, Pricing, Availability, Cart, Checkout, Order, Payment, Fulfillment, Aftercare, and Claim | Akros/N1 branding, customer configuration, or facts assigned to an External Business System |
| **Commerce Application Composition** | The dependency-closed DAG, required/optional modules, and dependency rules shared by commerce customers | Customer variation or Deployment Topology |
| **Akros Customer Configuration** | Permitted module selection, commercial policy/configuration, content, brand, locales, integration participation, and Channel Applications | Core, shared module contracts, or forked implementations |
| **Storefront** | Customer-facing composition, presentation, URLs, and SEO | Canonical commerce facts and lifecycle authority |
| **Commerce Operations** | Staff-facing workflow composition and Assisted Support | An unrestricted alternative to domain-owned behavior |
| **External Business System** | Only the facts and lifecycle decisions for which it is the assigned System of Record | Automatic authority over all Akros business data |
| **Symmy Connector** | The OntOS boundary to the preferred, non-exclusive Symmy Integration Hub for business-system routes it provides | Provider-specific downstream routes, canonical commerce policy, or business authority |
| **Direct Provider Adapter** | Owner-local exchange with a provider family outside Symmy or a business-system route Symmy does not provide | Core behavior, cross-module authority, or a preserved legacy Connector |
| **Infrastructure** | Operational execution, deployment, storage, observability, backup, and recovery | Business meaning or business ownership |

## Canonical language

OntOS definitions of **Party**, **Counterparty**, **Principal**, and **Legal Entity** govern. These concepts are not synonyms:

- a Party is the real-world person or organization;
- a Counterparty is a Party in a commercial or contractual relationship with a managed Legal Entity;
- a Principal is the actor exercising access or authority;
- a Legal Entity is a managed accounting or operating company inside an OntOS Tenant.

Party identity, CRM Customer, Counterparty, and commerce-profile ownership must use one OntOS-approved boundary. **Party Registry** is the current Wayfinder proposal, not yet a canonical OntOS module decision; confirmation and migration implications are tracked in [TechsioCZ/ontos#95](https://github.com/TechsioCZ/ontos/issues/95). Commerce-specific roles and policies remain with Commerce Business Modules and Customer Configuration; property/rental roles remain with their owning modules. Shared use never removes Tenant and Legal Entity isolation.

Use **External Business System**, **System of Record**, and **Connector** rather than assuming an ERP topology. Use **Commerce Operations** rather than treating “Admin” or “Back Office” as a business owner.

## Sharing rule

A capability belongs in Core only when it is a business-neutral mechanism required by OntOS modules generally. Shared business meaning belongs in a Foundational or Business Module, even when most compositions use it. Similar names, data shapes, or screens are not enough.

Commerce behavior remains in Commerce Business Modules. Akros-specific variation remains declarative Customer Configuration. N1 is the concrete second commerce configuration that tests reuse immediately.

## Roadmap consequences

- Deliver Akros through end-to-end business outcomes on the shared Commerce Application Composition, then onboard N1 without forking Core or modules.
- Keep the composition dependency-closed and Customer Configurations declarative.
- Keep Storefront and Commerce Operations dependent on stable commerce behavior rather than making either experience the business owner.
- Discover authority per fact and lifecycle transition; do not assume ABRA or any other External Business System is globally authoritative.
- Prefer the Symmy Connector for POHODA, ABRA, HELIOS, and comparable business-system exchange that Symmy provides; name those downstream routes as Symmy–Provider Integrations. Use owner-local Direct Provider Adapters for payments and other provider families outside Symmy, or where Symmy lacks a required route. N1's direct POHODA code is legacy evidence only.
- Keep Deployment Topology separate from Application Composition and Environment.
- Record a conflict with an accepted OntOS rule explicitly and reopen that rule when necessary; never work around it silently in Akros.

## Deferred decisions

This roadmap boundary does not prescribe modules, APIs, Actions, events, authorization sequences, state machines, schemas, deployment topology, provider contracts, or failure mechanics. Those details belong to later specifications and to the dedicated decisions for data authority, the OntOS kernel contract, commerce foundation, application shape, consistency, migration, and production readiness.
