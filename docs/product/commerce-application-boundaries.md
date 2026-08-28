# Commerce application boundaries

Decision asset for **[Decide storefront, commerce operations, and external-system application boundaries](https://github.com/TSNheathen/wayrepo/issues/11)**.

Status: confirmed by the product owner/Head of Engineering on 2026-08-28.

OntOS architecture record: [ADR-0017 — Commerce application boundaries](https://github.com/TechsioCZ/ontos/blob/develop/docs/adr/0017-commerce-application-boundaries.md).

## Decision

OntOS supplies one shared **Commerce Application Composition** for B2C and B2B. Akros, N1, and later customers are Customer Configurations of that backend, not separate commerce products or customer-pinned release lines.

Customer-facing **Storefront Applications** are independently deployed outside the standard OntOS Shell deployment. A customer may use one storefront, separate B2C and B2B storefronts, or another channel split. Each storefront has a tenant-bound **Storefront Client** identity and calls OntOS through a storefront-local BFF/proxy and the thin **Commerce Storefront API**.

OntOS owns commerce behavior and business configuration. Storefront Applications own presentation. Staff use a purpose-built **Commerce Operations** application over the same public module contracts; Commerce Operations is not an expansion of Shell/Core and is not a second writer.

## Boundary model

| Boundary | Owns | Must not own |
| --- | --- | --- |
| **External Storefront Application** | Framework, routing, rendering, layout, interaction design, branding, assets, SEO, and storefront-local aggregation | Canonical commerce facts, durable workflows, authorization policy, or provider orchestration |
| **Storefront-local BFF/proxy** | Same-origin browser boundary, server-held Storefront Client credential, request shaping, caching allowed by contract, and frontend-oriented aggregation | Business decisions, canonical state, durable recovery, or a private OntOS backdoor |
| **Commerce Storefront API** | Tenant/channel authentication, authorization, contract translation, bounded read aggregation, and invocation of public module Actions | Canonical business facts, module-private code, long-running workflow ownership, or presentation |
| **Commerce Business Modules** | B2C/B2B domain facts, policy, Actions, reads, events, workflow state, evidence, and public native commerce contracts | Customer presentation or transport-specific provider behavior |
| **Commerce Operations** | Focused staff workflows for orders, approvals, fulfillment, claims, reconciliation, and recovery | Canonical facts, unrestricted record editing, private module access, or generic Shell/Core behavior |
| **Shell/Core** | Existing staff authentication/context, governed entrypoints, catalogs, authorization integration, invocation guarantees, and business-neutral mechanisms | Commerce workflows, Storefront presentation, Portal Account ownership, or provider-specific meaning |
| **Integration Route** | The configured exchange path for one External Business System and fact family | Global authority over unrelated facts or a universal provider gateway |

Every channel edge is thin: authenticate and resolve context, authorize, translate a channel contract, aggregate bounded reads, invoke public Actions, and return typed results. It does not persist a competing commerce model or coordinate durable business work. This keeps a future MCP or UCP **Agentic Shopping Adapter** as a peer over native commerce contracts rather than an adapter layered through a storefront compatibility façade.

## Native contract and Medusa compatibility

The module-owned native Commerce contracts are authoritative. To deliver existing `new-engine` Storefront Applications quickly, the Commerce Storefront API may expose a deliberately temporary **Medusa Store Compatibility Facade** for the subset of Store API shapes those storefront hooks require.

The façade is protocol translation only. It does not introduce a Medusa runtime, Medusa-owned schema, Medusa workflow ownership, copied Medusa source, or permanent requirement that native clients use Medusa vocabulary. Its route inventory, semantic deviations, observability, deprecation criteria, and replacement plan must be explicit. Storefront hooks may later move to the native contracts when delivery time permits.

## Identity and authentication

The staff and commerce-portal authentication realms are separate even though both use BetterAuth:

- **Staff realm:** Shell owns staff BetterAuth accounts, cookies, sessions, tenant selection, Legal Entity context, and the Authenticated Principal Session contract. Commerce Operations uses this realm and the same staff authorization boundary.
- **Commerce portal realm:** OntOS Commerce owns Portal Account registration and customer authentication through a separate BetterAuth instance/schema/configuration, separate cookies and sessions, and separate account lifecycle. A Portal Account is not a Shell staff account.
- **Storefront service identity:** every Storefront Client has its own rotatable tenant-bound credential and service Principal. The credential identifies the calling application, not the browsing customer.
- **Customer identity:** an authenticated portal token/session separately identifies the retail or B2B Principal and its permitted Party/Counterparty context. Anonymous traffic receives only a bounded guest/cart context.

Portal registration belongs inside OntOS Commerce because it must participate in lifecycle events and stable links to Commerce, Party Registry, CRM, support/ticketing, and analytics. Party Registry remains the owner of shared Party/Counterparty identity; Commerce owns the Portal Account and commerce profile; Principal bindings and permissions govern access. Provider correlations remain Connector Registry facts.

No API may infer customer authority from the Storefront Client credential, a selected Tenant, or a Party Relationship. Both application identity and customer/guest context are validated independently on every protected request.

## Customer configuration and module implementations

One Customer Configuration/Tenant may enable multiple Storefront Clients and select B2C/B2B capabilities, channel permissions, assortment, pricing, market facts, B2B policy, and integration routes. These are business configuration owned by OntOS. Storefront layout and branding remain external presentation configuration.

Customer Configuration remains declarative, but its selectable values now include explicit module implementations:

- **Module Contract Identity** names the stable public capability contract.
- **Module Implementation Identity** names one catalogued executable implementation of that contract, for example `standard` or `akros`.
- If two implementations preserve the same public semantics and contract, they may share one Module Contract Identity and must have distinct Module Implementation Identities.
- If behavior requires different public semantics or contracts, it is a distinct module identity—not a hidden variant.
- The catalog records implementation identity, immutable build revision, public-contract hash/version, migration set, owner, and health/readiness.
- Customer Configuration selects only implementations permitted by its Application Composition. Invisible same-identity forks and customer-named copies that bypass the catalog are forbidden.

The normal path remains shared modules plus policy. An implementation alternative is justified only when configuration or an ordinary reusable capability cannot express the required behavior cleanly. Shell/Core does not grow to host an alternative.

## Delivery and compatibility

OntOS advances the shared backend through continuous mainline delivery. A customer cannot deliberately remain on “v1” or pin an independent whole-product release line. This does not remove release engineering:

- every deployed artifact has an immutable build revision and digest;
- public contracts retain compatibility metadata and skew checks;
- database and authorization changes use compatible rollout sequencing;
- canary, rollback, audit, and incident evidence identify exact revisions; and
- a customer-specific module implementation may be deployed independently without becoming a separate OntOS product version.

Module activation and implementation selection are configuration; artifact promotion is an OntOS-controlled operational decision.

## External-system routes

There is no universal integration gateway. For each External Business System and fact family, Customer Configuration selects one explicit **Integration Route**:

1. **One-time Migration** for bounded import and reconciliation during onboarding or replacement;
2. **Symmy Route** through the Symmy Connector when Symmy supplies the required business-system integration; or
3. **Direct Provider Route** through an owner-local Direct Provider Adapter for provider families outside Symmy or a required route Symmy does not supply.

The owning Business Module retains business authority and mapping semantics. Durable workers, outbox delivery, retries, reconciliation, and evidence remain owner-local regardless of route. Symmy is preferred and non-exclusive; it is not “one gateway to rule them all.”

## Consequences and proof obligations

- Storefront Applications can change framework or deployment independently without changing commerce ownership.
- B2C and B2B share one backend capability model while permitting customer-specific channel combinations.
- Shell/Core stays narrow; Commerce Operations and channel edges compose public module contracts.
- Portal customers and staff cannot accidentally share accounts, cookies, sessions, or tenant-selection semantics.
- A temporary Medusa-compatible façade accelerates delivery but creates an explicit retirement obligation.
- Explicit implementation identities make unavoidable customer code visible, testable, deployable, and removable instead of hiding forks.
- Continuous delivery removes customer-controlled release pinning but increases the importance of compatibility proof, canaries, rollback, and exact build evidence.

Production acceptance must prove tenant and client isolation, dual application/customer authentication, anonymous boundaries, B2C/B2B authorization, degraded dependency behavior, implementation selection, contract skew rejection, rollout/rollback, portal-to-Party linkage, and integration-route recovery.

## Deferred decisions

This decision does not select Next.js versus Modern.js for a Storefront Application, define concrete Commerce module contracts, choose provider routes for a customer, set deployment topology, specify workflow consistency, design migration/cutover, or implement MCP/UCP Agentic Shopping. Those remain downstream decisions. The required future-proofing here is only that native Commerce contracts—not the temporary Medusa façade—remain the common channel boundary.

## Superseded wording

This decision supersedes earlier statements that Customer Configuration may never select a customer-specific module implementation, that every Application Composition exposes a customer-selectable version, that Storefront Channel Applications may normally live in the OntOS deployment, or that no compatibility layer of any kind is allowed. The retained invariants are: no Core forks, no invisible module forks, no third-party commerce runtime or derived source, explicit contracts and implementation identities, and one OntOS-controlled forward-moving backend.
