# OntOS application-composition vocabulary

Status: confirmed on 2026-08-23 and amended by the Commerce application-boundary decision on 2026-08-28.

This note preserves the agreed distinction between a reusable OntOS application shape, its customer-specific configuration, and its runtime environments. It is deployment-topology neutral.

## Agreed terms

### Application Composition

A named, reusable, continuously delivered, dependency-closed directed acyclic graph of OntOS Foundational Modules and Business Modules serving a coherent business purpose.

As confirmed in [TechsioCZ/ontos#92](https://github.com/TechsioCZ/ontos/issues/92), each Module Manifest declares stable contract identity, compatibility metadata, explicit Foundational-or-Business kind, and intrinsic required dependency constraints. The Application Composition selects compatible implementations, owns the required set and permitted optional set, and closes those declarations into one DAG. Immutable build revisions and contract versions govern installation, skew detection, audit, and rollback, but customers do not pin a whole-product composition version or remain on a separate release line.

Core validates the contract generically at installation, activation, and every governed tenant entrypoint. A cycle, missing module, incompatible release, invalid kind, or unsatisfied dependency rejects installation or activation. A suspended or temporarily unreachable dependency returns a typed unavailable/degraded outcome for affected entrypoints without rewriting persisted module states; unrelated closures remain operable. Foundational Modules use the same independently deployable catalog and entrypoint path as Business Modules rather than becoming implicit Core capabilities.

This is the accepted logical target, not a statement that the current OntOS runtime already implements it. [Define and enforce Application Composition dependency contracts](https://github.com/TechsioCZ/ontos/issues/92) records the resolved decision and the schema and enforcement requirements that pass to downstream specifications and implementation tickets.

### Customer Configuration

A customer-specific declarative configuration of an Application Composition.

A Customer Configuration may select permitted optional modules, explicit Module Implementation Identities, and define customer policies, settings, locales, Storefront Clients, Connectors, and integration participation. It must not fork Core, change a shared Module Contract Identity in place, or hide customer code behind an existing implementation identity. Prefer shared capability and policy; when a genuine implementation alternative is necessary, catalogue and operate it explicitly. Different public semantics require a distinct module identity.

**Akros** and **N1** are Customer Configurations of the same Commerce Application Composition.

### Environment

A lifecycle context in which a Customer Configuration operates, such as **Production**, **Staging**, or **Development**.

Environment identity does not imply a geographic region, data-residency boundary, isolated deployment, or shared multi-tenant deployment.

### Deployment Topology

The physical mapping of Customer Configurations, Environments, Tenants, Application Composition modules, data stores, workers, and channel applications onto running infrastructure.

Deployment Topology determines matters such as customer isolation versus shared multi-tenancy, infrastructure placement, and any regional or residency constraints. Storefront Applications remain independently deployed outside the standard OntOS Shell deployment regardless of those still-open backend placement choices.

### Module Contract Identity and Module Implementation Identity

The **Module Contract Identity** names stable public capability semantics. A **Module Implementation Identity** names one explicit catalogued implementation of that contract. Multiple implementations may share a contract identity only while public semantics remain compatible; otherwise they are separate modules. The catalog records implementation identity, build revision, contract hash/version, migrations, owner, and health.

Customer Configuration selects a permitted implementation declaratively. This is not a customer-selectable product version: OntOS controls continuous mainline promotion, while immutable artifacts and compatibility metadata remain mandatory for canary, rollback, and audit.

### Storefront Application and Storefront Client

A **Storefront Application** is an independently deployed customer-facing application outside the standard OntOS Shell deployment. It owns presentation and uses a storefront-local BFF/proxy. Each Storefront Application authenticates as its own tenant-bound **Storefront Client** and presents a separate customer or guest context to the thin Commerce Storefront API.

### MicroVertical delivery seam

Every OntOS Foundational or Business Module is one independently deployable MicroVertical. Co-location is permitted only as a Deployment Topology choice and never permits private imports, shared repositories, cross-module database access, or shared business transactions. Executable registrations, migrations, handlers, workers, Policies, and repositories remain owner-local; Shell/Core consumes allowlisted serialized contracts and invokes governed entrypoints. Synchronous communication uses published typed clients and asynchronous communication uses published Outbox contracts.

The same authentication, authorization, typed-failure, compatibility, and dependency-closure boundary applies when co-located or network-separated. "Modular monolith" and "jointly deployable application" are historical OntOS terms, superseded by [OntOS ADR-0016](https://github.com/TechsioCZ/ontos/blob/develop/docs/adr/0016-independently-deployable-microverticals.md) and the accepted resolution of [OntOS #93: Record the independently deployable MicroVertical architecture and retire stale modular-monolith guidance](https://github.com/TechsioCZ/ontos/issues/93).

## Integration terminology

### Symmy Connector

The OntOS-to-Symmy boundary used when the preferred, non-exclusive Symmy Integration Hub supplies an external invoicing, accounting, ERP, WMS/PIM, or comparable business-system route. Owning modules exchange provider-neutral business facts through this boundary.

### Symmy–Provider Integration

A downstream provider-specific integration operated through Symmy, named for the concrete system—for example **Symmy–POHODA Integration**, **Symmy–ABRA Integration**, or **Symmy–HELIOS Integration**.

The N1 target Customer Configuration enables the Symmy Connector and prefers a Symmy–POHODA Integration when it satisfies the required contract. N1's current legacy direct POHODA integration is migration and behavioral evidence, not the target implementation.

### Direct Provider Adapter

An owner-local external adapter used for payments and other provider families intentionally outside Symmy, or when Symmy does not supply the required business-system integration. It obeys the owning module's public contract and does not acquire authority over business facts.

## Example

```text
OntOS
└── Commerce Application Composition
    ├── Akros Customer Configuration
    │   ├── Production Environment
    │   └── Staging Environment
    └── N1 Customer Configuration
        ├── Symmy Connector
        │   └── Symmy–POHODA Integration
        ├── Production Environment
        └── Staging Environment
```

## Handoff to the main Wayfinder conversation

- Replace the proposed **OntOS Solution** term with the two-level model: **Application Composition** plus **Customer Configuration**.
- Record Commerce as one reusable Application Composition; Akros and N1 are Customer Configurations of it, not separate products, compositions, release lines, or Core forks.
- Preserve dependency-closed composition as the target: Customer Configurations may choose only permitted modules and explicit implementations and cannot bypass contract compatibility. Until enforcement lands, documentation and planning must identify this as a production gate rather than implemented behavior.
- Keep Core's enforcement generic: Commerce owns the business meaning of its graph, while Core must validate it without learning commerce meaning.
- Keep Production/Staging/Development as topology-neutral Environments. Do not encode geography or decide customer isolation versus multi-tenancy here.
- Preserve the independently deployable MicroVertical seam in every topology; co-location cannot broaden access or move executable ownership into Shell/Core.
- Keep Storefront Applications outside the standard OntOS deployment, with one tenant-bound Storefront Client per frontend and a thin Commerce Storefront API over native module contracts.
- Prefer the Symmy Connector for the business-system integrations Symmy supplies; keep Symmy–Provider Integrations downstream and use owner-local Direct Provider Adapters for provider families outside Symmy or missing Symmy routes.
- Track unresolved OntOS implementation and boundary questions through the linked architect-review issues rather than presenting them as already implemented.
