# Akros Rebuild

This context defines the language used while planning Akros on a new architecture. The current deployment supplies evidence; it does not dictate the replacement's implementation or integration topology.

This glossary extends the canonical OntOS vocabulary in `../context/ontos/CONTEXT.md`. OntOS terms such as Party, Counterparty, Principal, Legal Entity, and Evidence Artifact keep their OntOS meanings here; this file defines only Akros-specific refinements. If the two glossaries conflict, OntOS governs.

## Language

**Akros**:
The e-commerce product being delivered as a sibling of OntOS. Akros consumes Core capabilities but is not an OntOS Business Module or a specialization of the OntOS property/rental ERP product.
_Avoid_: OntOS e-shop, OntOS commerce module

**Core**:
The shared, business-domain-neutral runtime and governance foundation consumed by Akros and OntOS. Core owns Strong Foundations and exposes its server-side boundary through CoreSDK; it is not a product, an ERP, or the owner of product-specific business lifecycles.
_Avoid_: OntOS, Core/Ontos, shared ERP

**Party Registry**:
The shared register of Party identity and Counterparty relationships used by Akros and OntOS. It does not own product-specific customer profiles, purchasing authority, property/rental roles, credentials, or managed Legal Entity group structure.
_Avoid_: Customer database, user directory, Organization Registry

**External Business System**:
A live upstream or downstream system that exchanges business facts with Akros, such as an ERP, accounting system, WMS, PIM, CRM, or bespoke service. Its product and system type must be observed rather than assumed.
_Avoid_: ERP, when the actual system or role has not been verified

**System of Record**:
The system authorized to decide a specific business fact or lifecycle transition. Authority is assigned per fact or transition and need not belong to one system globally.
_Avoid_: Master system, source system

**Connector**:
A replaceable adapter that implements an explicit contract between Akros and an External Business System. A legacy connector is evidence of a possible contract, not proof that the connected system is active or should survive.
_Avoid_: Integration, when referring to the adapter implementation itself

**Akros Integration Boundary**:
The provider-neutral governed seam through which Akros exchanges source facts, business handoffs, outcomes, and external identifiers using a direct Connector, the optional Symmy Connector, or an explicit coexistence of both. It carries no business authority of its own.
_Avoid_: Symmy, legacy adapter, universal integration hub

**Connector Registry**:
The Akros-owned record that correlates an Akros identity with identifiers issued by one External Business System, including the provenance and lifecycle needed for dependable exchange. Owning the mapping does not make Akros the issuer of the external identifier.
_Avoid_: Master identifier, shared external ID field

**Integration Hub**:
An External Business System that coordinates business exchanges with multiple third-party systems. It may route or transform facts but is not automatically their System of Record.
_Avoid_: Connector, ERP, System of Record

**Symmy**:
The Integration Hub used by Akros to exchange business facts with multiple third-party systems. Symmy's coordinating role does not by itself give it authority over those facts.
_Avoid_: ERP, Connector, universal System of Record

**Production Deployment Snapshot**:
The Akros package captured approximately five days before this Wayfinder session and confirmed by the operator as live and in use. It proves which code, routes, customizations, and connector seams were deployed at capture time, but not their database-controlled enablement, traffic, schedules, or operator use.
_Avoid_: Historical archive, when referring specifically to the current Akros package

**Deployed Capability**:
Behavior or a connector seam present in the Production Deployment Snapshot whose activation or use has not yet been established from runtime state.
_Avoid_: Active feature, requirement

**Active Behavior**:
Behavior established by current public observation, runtime configuration or telemetry, or explicit operator confirmation. Active Behavior is evidence for the replacement cutline, not an automatic decision to preserve it unchanged.
_Avoid_: Supported feature

**ABRA**:
A named External Business System family represented by several connector generations in the Production Deployment Snapshot. Its actual production role and activity remain facts to verify; it is neither the assumed current ERP nor a predetermined target dependency.

**Production-complete Launch**:
The release at which the replacement can safely take over every accepted Akros launch channel and its required end-to-end business outcomes. It does not imply parity with every capability deployed or historically available in WRShop.
_Avoid_: Feature parity, complete platform

**Launch Capability**:
A capability required at Production-complete Launch because it is active, revenue-critical, operationally necessary, legally required, or explicitly confirmed as part of the product promise.

**Later Capability**:
A useful capability intentionally deferred beyond Production-complete Launch because no launch-critical outcome depends on it.

**Archived Capability**:
Historical behavior or data retained read-only for customer service, audit, accounting, or legal obligations, without preserving its original write workflow.

**Retired Capability**:
A capability deliberately absent from the replacement because it is unused, obsolete, unsafe, or generic legacy breadth with no accepted Akros value.

**B2C Channel**:
The Akros retail selling channel. Visitors may browse and complete a purchase as a guest or as an authenticated Retail Portal Principal.

**Retail Customer**:
A Party buying or considering a purchase through the B2C Channel. A Retail Customer may purchase as a guest or use a Retail Portal Principal for durable portal access.
_Avoid_: Customer Account, consumer user

**Retail Portal Principal**:
A Principal authorized to access a Retail Customer's saved addresses, commerce history, aftercare, favorites, and notifications. It is optional for B2C checkout.
_Avoid_: Customer Account, user account

**B2B Channel**:
The Akros trade selling channel. Public visitors may see neutral product information and request access, but Counterparty-specific assortment, prices, availability, and ordering require an approved Principal acting for that Counterparty.

**Counterparty Buyer**:
A Principal authorized to prepare and submit purchases for a Counterparty within its assigned limits and approval rules.
_Avoid_: Company Buyer, Company User

**Counterparty Approver**:
A Principal authorized to approve or return purchases that require Counterparty approval.
_Avoid_: Company Approver

**Counterparty Access Administrator**:
A Principal authorized to manage which Principals may act for a Counterparty and with which permissions.
_Avoid_: Company Account Administrator, account owner

**Repeat Order**:
A request to construct a new cart from a historical order's still-sellable items and configurations. Current commercial rules apply; historical price, tax, availability, shipping, and payment terms are not reinstated.
_Avoid_: Duplicate order, reorder at original price

**Assisted Support**:
An audited staff capability that exposes the customer's channel and commercial context without silently assuming the customer's identity. Any customer-affecting action remains explicit, attributed to the operator, and permission-checked.
_Avoid_: Impersonation, login as customer

**Customer Archive**:
The authorized read-only experience through which a customer or operator can access retained historical orders, documents, and claims. It is not itself the statutory accounting or tax archive.

## Commerce domain language

**Product**:
A good or service with a stable commercial identity that can be described, classified, related, and made available through one or more Channels. Its current offer conditions are not part of its identity.
_Avoid_: Price, stock item, order line

**Catalog**:
The commerce domain that owns Product identity, variants and configurations, classification, descriptive facts, media references, and Product relationships.
_Avoid_: Assortment, price list, content-management screen

**Assortment**:
The set of Products eligible for visibility or purchase in a specific Channel or for a specific Counterparty under current policy.
_Avoid_: Catalog, inventory, price list

**Pricing**:
The commerce domain that determines applicable prices, discounts, fees, tax inputs, quantity tiers, and commercial quotations for an explicit commercial context.
_Avoid_: Product identity, invoice, accepted Order price

**Inventory**:
The commerce domain that records or represents stock and reservations when Akros owns those lifecycles. Inventory is distinct from the customer-facing delivery promise.
_Avoid_: Availability, assortment

**Availability**:
The current promise that a Product can be sold and delivered under an explicit commercial context. It may depend on Inventory or facts supplied by an External Business System without owning those facts.
_Avoid_: Inventory, assortment, raw stock count

**Cart**:
A prospective set of Product selections and configurations assembled under an explicit commercial context. A Cart is mutable and does not preserve accepted commercial terms as an Order does.
_Avoid_: Order, basket when naming the canonical domain concept

**Checkout**:
The commerce process that coordinates final validation, required customer choices, and submission of a Cart. Checkout does not own source commercial facts or the resulting Order.
_Avoid_: Order creation domain, payment domain

**Order**:
The durable record of an accepted purchase, including the accepted commercial snapshot and its governed lifecycle.
_Avoid_: Cart, invoice, payment

**Payment**:
The commerce domain that represents collection, authorization, settlement, cancellation, refund, and reconciliation outcomes associated with an Order.
_Avoid_: Order status, invoice, payment-provider callback

**Fulfillment**:
The commerce domain that represents preparation, handoff, delivery, tracking, and delivery exceptions for accepted Order quantities.
_Avoid_: Order, shipping-provider adapter

**Aftercare**:
Customer- and operator-facing post-purchase work permitted by the Order, Payment, Fulfillment, and Claim lifecycles. It coordinates those lifecycles without replacing their ownership.
_Avoid_: Unrestricted Order editing, customer service database

**Claim**:
A governed request concerning one or more durable Order lines, with its own evidence, communication, status, deadlines, and resolution history.
_Avoid_: Order note, generic support ticket

**Akros Commerce Policy**:
The named Akros-owned rules that specialize shared commerce behavior for its B2C and B2B Channels, Counterparty purchasing, quantities and packages, markets, and legal obligations.
_Avoid_: Core policy, provider-specific mapping, generic settings

**Storefront**:
The customer-facing composition and presentation of Akros commerce journeys, including Channel rendering, URLs, and SEO. It consumes commerce decisions but does not own canonical commerce facts.
_Avoid_: Commerce domain, System of Record

**Commerce Operations**:
The staff-facing composition of permissioned commerce workflows and Assisted Support. It invokes domain-owned behavior and does not provide an unrestricted alternative mutation surface.
_Avoid_: Admin, Back Office, direct database editor
