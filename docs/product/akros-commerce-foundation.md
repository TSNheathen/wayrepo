# Akros commerce foundation

Decision asset for **[Choose the commerce foundation for production Akros](https://github.com/TSNheathen/wayrepo/issues/9)**.

## Decision — corrected by the OntOS kernel decision

Production Akros will use the clean **Commerce Application Composition inside OntOS**. OntOS is the product; Core is its small internal kernel. Reusable commerce behavior belongs to OntOS Foundational and Business Modules. Akros and N1 are declarative Customer Configurations of the same continuously delivered dependency-closed composition, never sibling products or owners of hidden module forks.

The foundation contains no third-party commerce-engine runtime, package, schema, workflow, administration component, copied source, or transitional engine deployment. Existing engines may be read only as behavioral evidence and sources of independent acceptance scenarios. Renaming or selectively copying their code is not an acceptable route because it remains derived architecture and source.

The thin Commerce Storefront API may temporarily translate the limited Medusa Store API shapes required by existing `new-engine` storefront hooks. That protocol façade owns no business state or workflow, and the native module-owned Commerce contracts remain authoritative. This explicit delivery bridge does not weaken the prohibition on a Medusa runtime, schema, copied source, or engine-owned architecture.

The no-third-party-engine constraint is binding. If it conflicts with delivery timing, the roadmap removes non-launch scope or reforecasts delivery rather than silently introducing an engine dependency.

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

The dependency-closed composition is an accepted target rule, not a claim about current runtime completeness. [OntOS #92: Define and enforce Application Composition dependency contracts](https://github.com/TechsioCZ/ontos/issues/92) confirms that versioned public contracts declare intrinsic required dependency constraints, while the continuously delivered Commerce Application Composition selects compatible implementations and owns its required and permitted-optional graph. Core must preserve transitive closure during installation, tenant activation, and every governed entrypoint. Foundational Modules use the same independently deployable catalog path as Business Modules under an explicit module kind. A missing, incompatible, suspended, or unreachable dependency degrades only affected entrypoints and never cascades persisted state.

[OntOS #93: Record the independently deployable MicroVertical architecture and retire stale modular-monolith guidance](https://github.com/TechsioCZ/ontos/issues/93) confirms that every Foundational or Business Module retains one independently deployable MicroVertical seam. Co-location never permits private imports, shared repositories, cross-module database access, shared business transactions, or Shell/Core ownership of executable registration. Current repository mechanisms enforce substantial contract, catalog, gateway, data, and worker boundaries; production still requires independent placement, version-skew, failure-isolation, rollout, rollback, recovery, and operational proof.

## Scope and behavioral authority

The native implementation reproduces required business outcomes, not framework breadth or WRShop feature parity. It includes current Akros product shapes, commercial context, package and quantity rules, B2B purchasing and approval, durable Orders, payments and refunds, Fulfillment, Claims, integration recovery, and production Commerce Operations only where the Production-complete Launch requires them.

When evidence conflicts, precedence is:

1. accepted OntOS terminology and architectural decisions;
2. accepted Akros Wayfinder decisions;
3. current Akros behavior required by the production cutline;
4. legacy WRShop behavior as migration and acceptance evidence;
5. historical commerce-engine behavior as secondary inspiration and edge-case evidence; and
6. other commerce engines as general reference only.

A behavior from another engine enters scope only when the Akros cutline requires its outcome. An Akros behavior that conflicts with accepted OntOS architecture is surfaced explicitly rather than copied silently.

## Commerce Operations

Commerce Operations is a separately bounded, purpose-built staff application over the same public module-owned Actions used by every caller. It provides focused Catalog, Counterparty, Order, approval, Payment, Fulfillment, Claim, Content, reconciliation, and recovery workflows without becoming a generic administration framework, Shell/Core capability, canonical fact owner, or direct database editor.

Every state change is permissioned, policy-checked, attributed, and audited. Existing operator surfaces are behavioral references only; none supplies target runtime or UI code.

## Proof gates

The foundation is not considered production-commerce capable until evidence proves:

- Core-level business-effect duplicate prevention plus module-owned reconstruction of repeat responses;
- durable asynchronous work with crash recovery;
- fail-closed production identity, authorization, policy, and secrets;
- tenant and Legal Entity isolation;
- versioned composition compatibility and transitive dependency closure at installation, activation, and entrypoint execution;
- typed affected-entrypoint degradation without persisted-state cascades or unrelated-module outage;
- permissioned audit and Evidence Artifact handling;
- one writer for each canonical aggregate;
- recovery and reconciliation for external failures; and
- a representative end-to-end Akros commerce slice through Storefront and Commerce Operations.

Later acceptance must cover every launch-critical happy path, business rejection, external failure, retry/recovery, permission, audit, migration, and rollback scenario in the accepted product cutline.

## Migration and reversibility

WRShop remains the production commerce writer until the native replacement is accepted. There is no transitional third-party commerce runtime. Migration uses explicit, reconcilable imports and projections rather than synchronous dual writes. Historical records may remain in the Customer Archive where the cutline permits it.

The dedicated migration decision determines treatment of open Orders, payments, Fulfillments, Claims, Principal bindings, documents, mappings, coexistence, cutover, rollback, and legacy shutdown. Owned domain contracts and exportable canonical data preserve architectural reversibility; runtime compatibility with WRShop is not required.

## Alternatives rejected

The historical evidence assessment recommended a staged engine-derived route on delivery-risk grounds, but that route violates the confirmed architectural constraint and is rejected. The same constraint rejects a permanent engine dependency, a renamed or internalized derivative, and a transitional engine runtime.

Another established headless engine is rejected because it replaces the prohibited dependency with a different engine dependency, lacks comparable Akros evidence, and still requires rebuilding Akros-specific behavior and Commerce Operations. A generic engine evaluation may inform behavior but cannot become the foundation without reopening this decision.

A clean native composition has the largest implementation and proof burden. It is nevertheless selected deliberately because OntOS is the product architecture and engine-derived runtime or source is prohibited. Scope is controlled through the Akros cutline and reusable module graph rather than a customer fork or general commerce engine.

Research evidence: [Akros commerce-foundation options](../research/akros-commerce-foundation-options.md).

## Downstream decisions

- [Decide the OntOS kernel contract](https://github.com/TSNheathen/wayrepo/issues/10) determines the Core boundary and hardening ownership.
- [Commerce application boundaries](./commerce-application-boundaries.md) resolves Storefront, Commerce Operations, portal identity, module-implementation, delivery, and external-route boundaries.
- [Define critical commerce workflow and consistency guarantees](https://github.com/TSNheathen/wayrepo/issues/12) determines lifecycle state machines and cross-domain consistency.
- [Choose the Akros migration, coexistence, and cutover strategy](https://github.com/TSNheathen/wayrepo/issues/13) determines transition mechanics.
- [Set production readiness and acceptance gates](https://github.com/TSNheathen/wayrepo/issues/14) turns the proof obligations into launch gates.
- [Sequence the path into end-to-end Akros milestones](https://github.com/TSNheathen/wayrepo/issues/15) orders delivery without changing this foundation choice.
