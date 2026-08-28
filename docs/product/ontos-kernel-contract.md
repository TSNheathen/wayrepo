# OntOS kernel contract

Decision asset for **[Decide the OntOS kernel contract](https://github.com/TSNheathen/wayrepo/issues/10)**.

## Product model

OntOS is the product. Core is its small mandatory kernel; it is not a separately consumed product, a customer runtime, or a forkable foundation. Foundational and Business Modules own business meaning and compose into applications.

**Commerce** is one reusable Application Composition. **Akros** and **N1** are declarative Customer Configurations of that same composition. Customer variation never creates a Core fork, a module fork, or a customer-named copy of a reusable implementation.

Production, Staging, and Development are topology-neutral Environments. Customer isolation, multi-tenancy, geography, and placement remain a separate Deployment Topology decision.

Composition vocabulary: [OntOS application-composition vocabulary](./ontos-application-composition-vocabulary.md).

## Core admission rule

A capability belongs in Core only when it is a business-neutral guarantee required by OntOS modules generally. Similar domain names, shared screens, or use by several modules do not move business meaning into Core.

At roadmap depth, Core owns the common guarantees for:

- trusted identity and organizational context;
- authentication and authorization integration;
- governed reads and state changes;
- module and Application Composition governance;
- duplicate-effect prevention, audit, events, and durable work;
- stable cross-module addressing;
- generic media, evidence, search, and reporting foundations; and
- operational visibility and recovery of those mechanisms.

Business Modules own all domain models, lifecycles, policy, data, user experiences, and business correctness. Commerce, ERP, Ticketing, CRM, property/rental, finance, accounting, product, inventory, documents, and provider-specific meaning remain outside Core.

Shared business reality remains outside Core even when most Application Compositions need it. The exact boundary among Party identity, CRM, and commerce roles is awaiting OntOS architecture confirmation in [TechsioCZ/ontos#95](https://github.com/TechsioCZ/ontos/issues/95).

## Module and composition rules

Business Modules preserve independently deployable seams even when deployment topology co-locates them. They communicate only through published contracts and never acquire ownership of another module's business data or lifecycle.

As confirmed in [TechsioCZ/ontos#92](https://github.com/TechsioCZ/ontos/issues/92), dependency authority is split without duplication. A versioned Module Manifest declares the module's stable identity, release version, kind, and intrinsic required dependency constraints. A versioned Application Composition selects compatible module releases, owns the required set and permitted optional set, and closes those declarations into one acyclic graph. A Customer Configuration may select only options allowed by that composition; neither it nor a Module Manifest may broaden the composition.

Core binds installation and tenant activation to an immutable composition identity and version. Installation rejects the whole candidate catalog on a cycle, missing module, incompatible version, invalid kind, or unsatisfied required edge. Activation refuses a transition unless every transitive requirement is installed, compatible, and active. Every governed tenant entrypoint evaluates the same transitive closure before resolving private behavior.

Foundational Modules use the same independently deployable, one-module-per-MicroVertical delivery and Installed Module Catalog path as Business Modules, distinguished by an explicit manifest kind. They remain modules, not implicit Core capabilities, and communicate through the same public contracts and owner-local state boundaries.

A suspended, incompatible, missing, or temporarily unreachable dependency yields a typed unavailable/degraded result that identifies the affected entrypoint and dependency reason at the appropriate trust boundary. It never silently cascades or rewrites persisted tenant module states. A dependent module may remain persistently `active` while its effective entrypoints are unavailable; unrelated dependency closures remain operable.

This is the accepted target contract, not a claim that the current runtime already implements it. Schema, catalog, transition, entrypoint, Foundational Module, and test follow-through remain tracked in [Define and enforce Application Composition dependency contracts](https://github.com/TechsioCZ/ontos/issues/92). The replacement architecture decision and retirement of modular-monolith guidance are tracked in [TechsioCZ/ontos#93](https://github.com/TechsioCZ/ontos/issues/93).

## Continuity and evidence rules

As confirmed in [TechsioCZ/ontos#97](https://github.com/TechsioCZ/ontos/issues/97), Core owns invocation identity, canonical request-content conflict detection, and prevention of a second committed business effect. A succeeded duplicate is already committed: Core does not rerun the handler or persist and replay arbitrary business response payloads. The owning Business Module returns a stable ResourceRef/receipt or reconstructs a semantically stable response from canonical state when its interface requires repeat delivery. Byte-identical acknowledgement replay is an explicit bounded Business Module or Connector contract. In-flight duplicates and prior failed attempts retain their own lifecycle and retry policy; they are not successful-response replay.

Committed business facts and required external handoffs must survive routine failure and support visible recovery. Shared media, evidence, search, and reporting foundations provide common controls; their business purpose and correctness remain module-owned.

## Symmy and External Business Systems

Symmy is outside Core. It is the preferred, non-exclusive Integration Hub for external invoicing, accounting, ERP, WMS/PIM, and comparable business-system integrations it provides. Those routes cross the Symmy Connector, with provider-specific behavior downstream in named Symmy–Provider Integrations.

Payments and other provider families outside Symmy use owner-local Direct Provider Adapters. OntOS may also supply a direct business-system integration when Symmy lacks the required route. Both paths remain outside Core and preserve the owning module's authority. N1's direct POHODA integration is legacy and migration evidence, not the target implementation.

## Production-commerce gates

Commerce may rely on Core in production only after evidence proves:

- composition rules are enforced rather than documented only;
- customer and legal-entity isolation fails closed;
- identity, authorization, policy, and secrets fail safely;
- a module outage does not take unrelated capabilities down;
- repeated requests and durable work recover without duplicate business effects;
- evidence, media, search, and reporting respect permissions and recovery rules; and
- operators can observe and recover shared mechanisms.

Detailed workflows, schemas, algorithms, deployment mechanics, and acceptance thresholds belong to OntOS implementation specifications and the downstream Wayfinder decisions.

## Rejected or deferred

Rejected from Core:

- customer or module forks;
- Commerce, ERP, Ticketing, CRM, property/rental, or provider-specific business meaning;
- a generic entity store, workflow engine, plugin marketplace, or commerce engine; and
- deployment-provider, geography, or customer-isolation assumptions.

Deferred to downstream decisions:

- the exact Commerce module graph and module splits;
- Storefront and Channel Application boundaries;
- per-customer isolation, shared multi-tenancy, and regional placement;
- commerce workflow consistency;
- migration, coexistence, and cutover; and
- production acceptance thresholds and operating procedures.

## Authority and evidence

Canonical OntOS vocabulary and accepted architecture have highest authority. App-local OntOS documentation governs current implementation behavior. The issues linked above record gaps where the accepted product direction is not yet represented consistently by the runtime or canonical documentation.

Evidence: [OntOS customer and Business Module composition](../research/ontos-customer-module-composition-evidence.md) and [Czech ERP families and the OntOS commerce boundary](../research/czech-erp-families-ontos-commerce-boundary.md).
