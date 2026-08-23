# OntOS application-composition vocabulary

Status: confirmed in product/architecture discussion on 2026-08-23.

This note preserves the agreed distinction between a reusable OntOS application shape, its customer-specific configuration, and its runtime environments. It is deployment-topology neutral.

## Agreed terms

### Application Composition

A named, reusable, dependency-closed directed acyclic graph of OntOS Foundational Modules and Business Modules serving a coherent business purpose.

An Application Composition defines required modules, permitted optional modules, and their dependency rules. Core validates those rules generically. A dependency failure may make affected capabilities unavailable but must not disable unrelated modules or silently rewrite module state. **Commerce** is an Application Composition shared by Akros, N1, and later commerce customers.

This is the accepted logical target, not a statement that the current OntOS runtime already implements it. The versioned contract, Foundational Module representation, and enforcement points await architect confirmation in [TechsioCZ/ontos#92](https://github.com/TechsioCZ/ontos/issues/92).

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

For the POHODA, ABRA, HELIOS, and comparable business-system routes decided by this roadmap, the OntOS-to-Symmy integration boundary. OntOS does not preserve direct legacy Connectors for those systems.

### Symmy–Provider Integration

A downstream provider-specific integration operated through Symmy, named for the concrete system—for example **Symmy–POHODA Integration**, **Symmy–ABRA Integration**, or **Symmy–HELIOS Integration**.

The N1 target Customer Configuration enables the Symmy Connector and uses a Symmy–POHODA Integration. N1's current legacy direct POHODA integration is migration and behavioral evidence, not the target OntOS integration architecture.

Whether every other provider family, including payment and carrier providers, must cross this seam is an OntOS-owned decision tracked in [TechsioCZ/ontos#96](https://github.com/TechsioCZ/ontos/issues/96).

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
- Preserve dependency-closed composition as the target: Customer Configurations may choose only valid optional modules and cannot bypass the module graph once enforcement is implemented.
- Keep Core's enforcement generic: Commerce owns the business meaning of its graph, while Core must validate it without learning commerce meaning.
- Keep Production/Staging/Development as topology-neutral Environments. Do not encode geography or decide customer isolation versus multi-tenancy here.
- Correct the N1 target integration statement: OntOS uses the Symmy Connector; POHODA-specific behavior is a Symmy–POHODA Integration. The current direct legacy POHODA integration is evidence only.
- Track unresolved OntOS implementation and boundary questions through the linked architect-review issues rather than presenting them as already implemented.
