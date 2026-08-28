# OntOS application-composition vocabulary

Status: confirmed in product/architecture discussion on 2026-08-23.

This note preserves the agreed distinction between a reusable OntOS application shape, its customer-specific configuration, and its runtime environments. It is deployment-topology neutral.

## Agreed terms

### Application Composition

A named, reusable, versioned, dependency-closed directed acyclic graph of OntOS Foundational Modules and Business Modules serving a coherent business purpose.

As confirmed in [TechsioCZ/ontos#92](https://github.com/TechsioCZ/ontos/issues/92), each versioned Module Manifest declares its module identity, release version, explicit Foundational-or-Business kind, and intrinsic required dependency constraints. The Application Composition selects compatible releases, owns the required set and permitted optional set, and closes those declarations into one DAG. Its identity and version govern installation and Customer Configuration activation; a Customer Configuration cannot broaden the graph.

Core validates the contract generically at installation, activation, and every governed tenant entrypoint. A cycle, missing module, incompatible release, invalid kind, or unsatisfied dependency rejects installation or activation. A suspended or temporarily unreachable dependency returns a typed unavailable/degraded outcome for affected entrypoints without rewriting persisted module states; unrelated closures remain operable. Foundational Modules use the same independently deployable catalog and entrypoint path as Business Modules rather than becoming implicit Core capabilities.

This is the accepted logical target, not a statement that the current OntOS runtime already implements it. [Define and enforce Application Composition dependency contracts](https://github.com/TechsioCZ/ontos/issues/92) tracks the schema and enforcement follow-through.

### Customer Configuration

A customer-specific declarative configuration of an Application Composition.

A Customer Configuration may select permitted optional modules and define customer policies, settings, branding, locales, Connectors, and integration participation. It must not fork Core, change shared module contracts, or create customer-specific copies of module implementations. A genuinely necessary variation should become a reusable module capability or an explicitly governed extension rather than an implicit customer fork.

**Akros** and **N1** are Customer Configurations of the same Commerce Application Composition.

### Environment

A lifecycle context in which a Customer Configuration operates, such as **Production**, **Staging**, or **Development**.

Environment identity does not imply a geographic region, data-residency boundary, isolated deployment, or shared multi-tenant deployment.

### Deployment Topology

The physical mapping of Customer Configurations, Environments, Tenants, Application Composition modules, data stores, workers, and channel applications onto running infrastructure.

Deployment Topology determines matters such as customer isolation versus shared multi-tenancy, infrastructure placement, and any regional or residency constraints. Those choices remain undecided and do not change the logical vocabulary above.

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
- Record Commerce as one reusable Application Composition; Akros and N1 are Customer Configurations of it, not separate products, compositions, module families, or Core forks.
- Preserve dependency-closed composition as the target: Customer Configurations may choose only valid optional modules and cannot bypass the versioned module graph. Until enforcement lands, documentation and planning must identify this as a production gate rather than implemented behavior.
- Keep Core's enforcement generic: Commerce owns the business meaning of its graph, while Core must validate it without learning commerce meaning.
- Keep Production/Staging/Development as topology-neutral Environments. Do not encode geography or decide customer isolation versus multi-tenancy here.
- Prefer the Symmy Connector for the business-system integrations Symmy supplies; keep Symmy–Provider Integrations downstream and use owner-local Direct Provider Adapters for provider families outside Symmy or missing Symmy routes.
- Track unresolved OntOS implementation and boundary questions through the linked architect-review issues rather than presenting them as already implemented.
