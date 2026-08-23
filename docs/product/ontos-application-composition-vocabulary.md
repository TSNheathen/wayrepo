# OntOS application-composition vocabulary

Status: confirmed in product/architecture discussion on 2026-08-23.

This note preserves the agreed distinction between a reusable OntOS application shape, its customer-specific configuration, and its runtime environments. It is deployment-topology neutral.

## Agreed terms

### Application Composition

A named, reusable, dependency-closed directed acyclic graph of OntOS Foundational Modules and Business Modules serving a coherent business purpose.

An Application Composition defines required modules, permitted optional modules, and their dependency rules. Core validates the graph and enforces dependency closure during installation, activation, and entrypoint execution. A module can be activated only when its required dependencies are present and active. A later dependency outage does not rewrite or cascade persisted module states; affected entrypoints degrade explicitly while unrelated modules continue operating. **Commerce** is an Application Composition shared by Akros, N1, and later commerce customers.

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

The single OntOS-to-Symmy integration boundary. OntOS integrates with Symmy rather than maintaining direct Connectors for legacy systems such as POHODA, ABRA, or HELIOS.

### Symmy–Provider Integration

A downstream provider-specific integration operated through Symmy, named for the concrete system—for example **Symmy–POHODA Integration**, **Symmy–ABRA Integration**, or **Symmy–HELIOS Integration**.

The N1 target Customer Configuration enables the Symmy Connector and uses a Symmy–POHODA Integration. N1's current legacy direct POHODA integration is migration and behavioral evidence, not the target OntOS integration architecture.

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
- Preserve dependency-closed module activation: Customer Configurations may choose only valid optional modules and cannot bypass the module dependency graph.
- Keep Core's enforcement generic: the Commerce Application Composition owns its versioned DAG, while Core validates and gates it without learning commerce meaning.
- Keep Production/Staging/Development as topology-neutral Environments. Do not encode geography or decide customer isolation versus multi-tenancy here.
- Correct the N1 target integration statement: OntOS uses the Symmy Connector; POHODA-specific behavior is a Symmy–POHODA Integration. The current direct legacy POHODA integration is evidence only.
- When the wider OntOS correction is accepted, incorporate these definitions into the canonical context/glossary and supersede conflicting Wayfinder terminology.
