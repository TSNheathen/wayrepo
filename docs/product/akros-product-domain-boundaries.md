# Akros product and domain boundary model

Decision asset for **[Define the product and domain boundary model](https://github.com/TSNheathen/wayrepo/issues/7)**.

## Direction

Akros and OntOS are sibling products. They may share Core and genuinely common business foundations, but Akros is not an OntOS Business Module and OntOS is not the name of the shared platform. Accepted OntOS terminology and general architectural rules have highest authority; Akros may specialize them for commerce but must not silently redefine them.

The concrete comparison for sharing decisions is OntOS V1's property/rental delivery. Existing Ticketing provides implementation evidence for shared foundations, but does not by itself justify shared commerce abstractions.

## Ownership map

| Boundary | Owns | Does not own |
| --- | --- | --- |
| **Core** | General foundations genuinely needed by both Akros and OntOS | Commerce lifecycles or product-specific business policy |
| **Shared business foundations** | Business concepts whose meaning is demonstrably the same in both products, including shared Party identity and Counterparty relationships | Akros purchasing policy or OntOS property/rental policy |
| **E-commerce domains** | Reusable commerce behavior across Catalog, Assortment, Pricing, Availability, Cart, Checkout, Order, Payment, Fulfillment, Aftercare, and Claim | Akros branding and presentation, or facts assigned to an External Business System |
| **Akros** | Its B2C and B2B Channels, commercial policy, content, brand, presentation, customer journeys, and operating model | Core foundations or globally generic business behavior |
| **Storefront** | Customer-facing composition, presentation, URLs, and SEO | Canonical commerce facts and lifecycle authority |
| **Commerce Operations** | Staff-facing workflow composition and Assisted Support | An unrestricted alternative to domain-owned behavior |
| **External Business System** | Only the facts and lifecycle decisions for which it is the assigned System of Record | Automatic authority over all Akros business data |
| **Connector** | Replaceable exchange between Akros and one External Business System | Canonical commerce policy or business authority |
| **Infrastructure** | Operational execution, deployment, storage, observability, backup, and recovery | Business meaning or business ownership |

## Canonical language

OntOS definitions of **Party**, **Counterparty**, **Principal**, and **Legal Entity** govern. These concepts are not synonyms:

- a Party is the real-world person or organization;
- a Counterparty is a Party in a commercial or contractual relationship with a managed Legal Entity;
- a Principal is the actor exercising access or authority;
- a Legal Entity is a managed accounting or operating company inside an OntOS Tenant.

Party Registry is the shared boundary for Party identity and Counterparty relationships. Product-specific roles and policies remain with their product: Akros owns Retail Customer and Counterparty purchasing behavior, while OntOS owns its property/rental roles. Shared use never removes Tenant and Legal Entity isolation.

Use **External Business System**, **System of Record**, and **Connector** rather than assuming an ERP topology. Use **Commerce Operations** rather than treating “Admin” or “Back Office” as a business owner.

## Sharing rule

A capability belongs in Core only when Akros and the concrete OntOS V1 use case require the same general behavior. Shared business behavior belongs outside Core unless both products need the same meaning and ownership. Similar names, data shapes, or screens are not enough.

Commerce behavior remains in the e-commerce domains, and Akros-specific behavior remains in Akros. Prefer a clear product boundary over a premature universal abstraction. Sharing must help delivery and must not delay production Akros for conceptual purity.

## Roadmap consequences

- Deliver Akros through end-to-end business outcomes while allowing proven shared foundations to progress in parallel.
- Keep Storefront and Commerce Operations dependent on stable commerce behavior rather than making either experience the business owner.
- Discover authority per fact and lifecycle transition; do not assume ABRA or any other External Business System is globally authoritative.
- Keep Connectors replaceable and infrastructure subordinate to business ownership.
- Record a conflict with an accepted OntOS rule explicitly and reopen that rule when necessary; never work around it silently in Akros.

## Deferred decisions

This roadmap boundary does not prescribe modules, APIs, Actions, events, authorization sequences, state machines, schemas, deployment topology, provider contracts, or failure mechanics. Those details belong to later specifications and to the dedicated decisions for data authority, shared Core contract, commerce foundation, application shape, consistency, migration, and production readiness.
