# Akros commerce foundation

Decision asset for **[Choose the commerce foundation for production Akros](https://github.com/TSNheathen/wayrepo/issues/9)**.

## Decision — corrected by the OntOS kernel decision

Production Akros will use the clean **Commerce Application Composition inside OntOS**. OntOS is the product; Core is its small internal kernel. Reusable commerce behavior belongs to OntOS Foundational and Business Modules. Akros and N1 are declarative Customer Configurations of the same dependency-closed composition, never sibling products or owners of forked modules.

The foundation contains no Medusa runtime, package, schema, API, workflow, Admin component, copied source, compatibility layer, or transitional deployment. Medusa and other commerce engines may be read only as behavioral evidence and sources of independent acceptance scenarios. Renaming or selectively copying their code is not an acceptable route because it remains derived architecture and source.

The no-Medusa constraint is binding. If it conflicts with delivery timing, the roadmap removes non-launch scope or reforecasts delivery rather than silently introducing an engine dependency.

## Composition shape

The Commerce Application Composition uses the accepted OntOS architecture:

- CoreSDK-governed Actions and reads;
- explicit business ownership and one writer per canonical aggregate;
- BetterAuth, Principal binding, authorization, and business policy separation;
- tenant and Legal Entity isolation;
- audit, Evidence Artifacts, domain events, and durable outbox work;
- module-owned canonical data rather than a generic entity store; and
- independently deployable MicroVertical seams with contract-only communication; and
- Storefront and Commerce Operations as composition surfaces over module-owned behavior.

Commerce Business Modules cover the e-commerce domains required by the accepted cutline: Catalog, Assortment, Pricing, Inventory, Availability, Cart, Checkout, Order, Payment, Fulfillment, Aftercare, and Claim. Akros-specific Content and Commerce Policy are declarative Customer Configuration over those modules. Shared business reality remains in Foundational or Business Modules rather than expanding Core.

MicroVerticals may be co-located but must remain independently deployable. The architecture does not introduce synchronous dual writes, cross-module transactions, private implementation imports, a generic plugin marketplace, or a universal commerce framework.

## Scope and behavioral authority

The native implementation reproduces required business outcomes, not framework breadth or WRShop feature parity. It includes current Akros product shapes, commercial context, package and quantity rules, B2B purchasing and approval, durable Orders, payments and refunds, Fulfillment, Claims, integration recovery, and production Commerce Operations only where the Production-complete Launch requires them.

When evidence conflicts, precedence is:

1. accepted OntOS terminology and architectural decisions;
2. accepted Akros Wayfinder decisions;
3. current Akros behavior required by the production cutline;
4. legacy WRShop behavior as migration and acceptance evidence;
5. Medusa and `new-engine` behavior as secondary inspiration and edge-case evidence; and
6. other commerce engines as general reference only.

A behavior from Medusa or another engine enters scope only when the Akros cutline requires its outcome. An Akros behavior that conflicts with accepted OntOS architecture is surfaced explicitly rather than copied silently.

## Commerce Operations

Commerce Operations is purpose-built over the same module-owned Actions used by every caller. It provides focused Catalog, Counterparty, Order, Payment, Fulfillment, Claim, Content, and Connector workflows without becoming a generic administration framework or a direct database editor.

Every state change is permissioned, policy-checked, attributed, and audited. Existing WRShop and Medusa operator surfaces are behavioral references only; neither supplies target runtime or UI code.

## Proof gates

The foundation is not considered production-commerce capable until evidence proves:

- Core-level business-effect duplicate prevention plus module-owned reconstruction of repeat responses;
- durable asynchronous work with crash recovery;
- fail-closed production identity, authorization, policy, and secrets;
- tenant and Legal Entity isolation;
- permissioned audit and Evidence Artifact handling;
- one writer for each canonical aggregate;
- recovery and reconciliation for external failures; and
- a representative end-to-end Akros commerce slice through Storefront and Commerce Operations.

Later acceptance must cover every launch-critical happy path, business rejection, external failure, retry/recovery, permission, audit, migration, and rollback scenario in the accepted product cutline.

## Migration and reversibility

WRShop remains the production commerce writer until the native replacement is accepted. There is no Medusa transition. Migration uses explicit, reconcilable imports and projections rather than synchronous dual writes. Historical records may remain in the Customer Archive where the cutline permits it.

The dedicated migration decision determines treatment of open Orders, payments, Fulfillments, Claims, Principal bindings, documents, mappings, coexistence, cutover, rollback, and legacy shutdown. Owned domain contracts and exportable canonical data preserve architectural reversibility; runtime compatibility with WRShop is not required.

## Alternatives rejected

The evidence assessment recommended a staged Medusa-derived route on delivery-risk grounds, but that route violates the confirmed architectural constraint and is rejected. The same constraint rejects permanent Medusa, a renamed or internalized derivative, and a transitional Medusa runtime.

Another established headless engine is rejected because it replaces the prohibited dependency with a different engine dependency, lacks comparable Akros evidence, and still requires rebuilding Akros-specific behavior and Commerce Operations. A generic engine evaluation may inform behavior but cannot become the foundation without reopening this decision.

A clean native composition has the largest implementation and proof burden. It is nevertheless selected deliberately because OntOS is the product architecture and Medusa-derived runtime or source is prohibited. Scope is controlled through the Akros cutline and reusable module DAG rather than a customer fork or general commerce engine.

Research evidence: [Akros commerce-foundation options](../research/akros-commerce-foundation-options.md).

## Downstream decisions

- [Decide the shared Ontos kernel contract](https://github.com/TSNheathen/wayrepo/issues/10) determines the exact shared Core contract and hardening ownership.
- [Decide storefront, commerce operations, and external-system application boundaries](https://github.com/TSNheathen/wayrepo/issues/11) determines physical application composition.
- [Define critical commerce workflow and consistency guarantees](https://github.com/TSNheathen/wayrepo/issues/12) determines lifecycle state machines and cross-domain consistency.
- [Choose the Akros migration, coexistence, and cutover strategy](https://github.com/TSNheathen/wayrepo/issues/13) determines transition mechanics.
- [Set production readiness and acceptance gates](https://github.com/TSNheathen/wayrepo/issues/14) turns the proof obligations into launch gates.
- [Sequence the path into end-to-end Akros milestones](https://github.com/TSNheathen/wayrepo/issues/15) orders delivery without changing this foundation choice.
