# Ontos, ERP, and prior commerce attempts against production Akros

> **Historical evidence notice (superseded 2026-08-23):** This assessment predates the accepted OntOS product/composition model and the current independently deployable MicroVertical architecture. Its modular-monolith and separately shared-kernel framing is not current. Use the [OntOS kernel contract](../product/ontos-kernel-contract.md) for the accepted roadmap direction and the linked OntOS architect-review issues for implementation gaps.

Research for the Wayfinder ticket **Assess Ontos, ERP overlap, and prior commerce attempts against Akros**.

## Question

Against the production-Akros destination, what is evidenced about Ontos's intent, implemented maturity, reusable kernel concepts, unresolved risks, and missing commerce capabilities; which ERP capabilities genuinely overlap without blocking Akros; and what do the Medusa/new-engine and other historical attempts establish or leave undecided?

## Executive finding

Ontos is best evidenced as a **partially implemented application kernel with one real Ticketing vertical**, not as a commerce engine and not yet as a production-hardened shared platform. Its strongest concepts—governed Actions and reads, operation identity/context, authorization and policy separation, transactional evidence/outbox, module state, media, and explicit module-owned data—are credible candidates for a shared foundation. They should survive only where Akros proves their value and after the production gaps described below are closed.

The property/rental ERP plan supplies useful domain and delivery evidence, but its historical destination differs from the current Akros-first destination. Genuine overlap is concentrated in cross-cutting runtime guarantees, staff-facing operations, documents, parties, billing/accounting handoff, reporting, and integration execution. Akros does not need the property/rental ERP to be designed or completed before commerce can ship.

Ontos has no implementation of the core commerce domains required by Akros. The historical Medusa/new-engine work therefore remains important as a commerce behavior inventory and migration-risk analysis. It does **not** settle the commerce foundation: different reports recommend an existing headless engine plus sidecar, an in-repo Medusa fork, and a Medusa-derived compatibility runtime around an extracted Ontos kernel. The latest hybrid report is the most recent synthesis, but it is still a historical agent analysis of a repository that is not present in this evidence set, not an accepted architecture decision.

The resulting direction is:

1. Treat Ontos as a candidate source of kernel contracts, not as proof that commerce should be rebuilt natively on Ontos.
2. Decide the commerce runtime against the reconstructed production-Akros acceptance matrix and hard scenarios.
3. Share ERP foundations only at demonstrated seams; keep commerce domain behavior and Akros integration mappings independently deliverable.
4. Preserve characterization, single-writer, outbox, reconciliation, and incremental-cutover lessons regardless of the chosen engine.

## Evidence classes and provenance

The source material spans different generations, so this report assigns evidentiary weight by provenance rather than apparent completeness.

| Evidence class | Sources | Weight in this assessment |
| --- | --- | --- |
| Current implementation | `../context/ontos/app/` at Git commit `1bb6685`; current source, tests, package topology | Strongest evidence of what Ontos actually implements now |
| Recorded ADR | `../context/ontos/adr/` | Binding only where status is `Accepted`; `Accepted for current planning` is provisional; `Proposed` remains unresolved |
| Current architecture/product documentation | Numbered Ontos architecture pack and `CONTEXT.md`, mostly June 2026 | Strong evidence of intent at that time, not proof of implementation or present product priority |
| Historical implementation attempts | `../context/ontos/mvp/`, `../context/ontos/mvp2/` and their handoffs | Useful proof/lesson evidence; explicitly experimental or disposable |
| Historical Medusa/new-engine analyses | `../context/e-commerce_new-engine_dump/`, dated 18 August 2026 | High-value risk and option analysis, but secondary evidence extracted from an unavailable session/repository snapshot |
| Existing Akros and old-core source | `../context/team-1633516729428-akros-124b361ce570/` and `../context/team-1633516729428-engine-a6883ea926b4/` | Primary evidence of existing behavior; this note samples ERP/integration seams rather than attempting the complete Akros inventory |
| Old-core feature reports | `../context/stare_jadro_featury/output/` | Useful synthesized inventory, but claims marked as hypotheses remain hypotheses and should be checked against source/current usage |

The Medusa hybrid report explicitly says it examined Ontos at commit `1bb66855...`, which matches the available Ontos `main`. It also explicitly describes itself as a faithful historical extraction rather than a new audit (`../context/e-commerce_new-engine_dump/extracted-medusa-ontos-hybrid-strategy.md:297-304`).

## What Ontos was trying to build

The architecture pack frames Ontos as a delivery-bound property/rental ERP that could later grow into a temporal company-ontology system. V0 was corrected to mean preparation; V1 was the mandatory end-of-2026 ERP delivery (`../context/ontos/CONTEXT.md:3-18`; `../context/ontos/16_JUNE_2026_V0_PREP_AND_V1_DELIVERY_HANDOFF.md:6-26`).

Its intended application shape is:

- a TypeScript modular monolith rather than distributed MicroVertical services;
- a Shell that composes jointly deployable, full-stack MicroVertical business modules;
- Core as a non-business kernel used by Shell and every module;
- Postgres as canonical operational state;
- registered Actions for state changes, with events/outbox for post-commit effects;
- explicit module-owned domain tables rather than a generic entity/EAV store;
- `ResourceRef` values for cross-module addressability;
- BetterAuth authentication, SpiceDB relationship authorization, and a separate business Policy Layer;
- optional Neo4j and other replayable projections.

Evidence: `../context/ontos/03_ARCHITECTURE_OVERVIEW.md:1-39`, `../context/ontos/05_MICROVERTICALS.md:5-45`, and `../context/ontos/06_CORE_KERNEL.md:1-51`.

The most important scope rule is that Core owns cross-cutting runtime guarantees, while business modules own business meaning. The Core document explicitly warns against generalizing a concept merely because two modules appear similar; a shared abstraction should follow a second proven use case (`../context/ontos/06_CORE_KERNEL.md:29-33`). That rule is directly compatible with an Akros-first path.

### Accepted or provisionally accepted decisions

- **Postgres canonical; Neo4j optional projection** is `Accepted`. Operational behavior must not depend on Neo4j (`../context/ontos/adr/0004-postgres-canonical-neo4j-projection.md`).
- **BetterAuth + SpiceDB + Policy Layer separation** is `Accepted` (`../context/ontos/adr/0005-betterauth-spicedb-policy-layer.md`).
- **Authenticated Principal Session** is `Accepted`: a raw BetterAuth session is insufficient without active Principal binding and Tenant (`../context/ontos/adr/0014-authenticated-principal-session.md`).
- **Explicit domain tables plus ResourceRef** is `Accepted for current planning`, not unconditionally accepted (`../context/ontos/adr/0006-explicit-domain-tables-plus-resource-ref.md`).
- **No product AI in V0** is also `Accepted for current planning` (`../context/ontos/adr/0007-no-product-ai-in-v0.md`).

### Still-proposed load-bearing choices

The following remain `Proposed`: unified MicroVertical slices, modular monolith, Action-driven writes with evented effects, module activation states, Postgres outbox/idempotent workers, separation of business and authorization graphs, internal dogfooding, and broadcast outbox deliveries. See ADRs 0001-0003, 0008-0011, and 0013 under `../context/ontos/adr/`.

These proposals are coherent and several now have implementation support, but their ADR status matters: they were not formally locked for a production-Akros architecture.

## Implemented maturity

The current `app/` is materially beyond a paper design:

- `packages/core-runtime` implements CoreSDK, operation context, module state, auth, SpiceDB authorization, policy, evidence tables, media, and outbox contracts.
- `packages/outbox-worker` implements delivery materialization, pending claims, attempts, retry/dead state, and a callable worker tick.
- `verticals/ticketing` contains 60 Action implementation files and substantial domain/API/UI tests.
- Ticketing proves optimistic revisions, tenant-scoped persistence, governed Actions/reads, Core references, person directory, and media use across a real vertical.

The current architecture contract says public backend work should enter through CoreSDK; BFF adapters must not own transactions, call handlers/SpiceDB directly, or write Core evidence (`../context/ontos/app/packages/core-runtime/docs/action-ecosystem.md:1-33`). This is credible implementation evidence for a reusable boundary.

However, maturity stops well short of production commerce:

- `ticketing` is the only installed module, hard-coded in `../context/ontos/app/packages/shared-contracts/src/module-state.ts:13-15` and Shell entrypoints.
- The documented Ontos Module Manifest/Runtime Registration architecture is not fully present in the current app; module installation and Shell wiring are static.
- Current runtime documentation explicitly leaves generic exact-response idempotency replay, concrete business Actions, access-management Actions, external queues, and full manifest generation out of scope (`../context/ontos/app/packages/core-runtime/docs/action-ecosystem.md:80-87`).
- An already-successful idempotent Action returns `OperationIdempotencyReplayUnavailable` rather than the original response (`../context/ontos/app/packages/core-runtime/src/core-sdk.ts:711-735`). The hybrid analysis correctly identifies this as unsuitable for checkout, capture, refund, and fulfillment creation (`../context/e-commerce_new-engine_dump/extracted-medusa-ontos-hybrid-strategy.md:102-111`).
- The outbox runtime claims only `pending` deliveries and records `claimed_at`, but exposes no lease expiry or reclaim path for a process that dies while a delivery is `processing`; it is a tick function rather than an independently deployed continuous worker (`../context/ontos/app/packages/outbox-worker/src/runtime.ts:115-177,389-420`).
- BetterAuth and gateway-token code retain local fallback secrets rather than failing closed without production configuration (`../context/ontos/app/packages/core-runtime/src/auth/config.ts:11-12`; `../context/ontos/app/packages/core-runtime/src/vertical-gateway-token.ts:49-50`).
- No catalog, cart, checkout, order, payment, inventory, fulfillment, tax, customer-commerce, storefront, or Commerce Admin module exists.

### What the earlier attempts prove

- `mvp/` proved Shell/MicroVertical composition and tenant/auth/context/authz gates. Its Day 3 summary explicitly says it wrote no canonical business, Action, audit, event, outbox, property, or billing rows; live database/browser proof was blocked in that environment (`../context/ontos/21_DAY_3_IMPLEMENTATION_SUMMARY.md:1-5,73-121`).
- `mvp2/` was explicitly a fresh experiment, not a migration of `mvp/`. It specified CoreSDK as the sole public read/write boundary and defined the success/denial/failure/idempotency transaction semantics to prove (`../context/ontos/22_MVP2_CORESDK_IMPLEMENTATION_REQUIREMENTS.md:3-15,24-44,113-126`).
- `app/` subsequently demonstrates that many of those kernel concepts can support a non-trivial Ticketing vertical. It does not demonstrate their suitability for high-risk commerce invariants.

The June readiness report remains useful as a warning against confusing architectural coherence with delivery readiness. It found missing acceptance criteria, unsettled ADRs, unenumerated module functions, incomplete tenant/idempotency constraints, unresolved Party ownership, and undefined accounting handoff (`../context/ontos/15_PRE_DEVELOPMENT_VALIDATION_REPORT.md:16-23,46-231`). Some kernel issues have since been implemented, but the report's product-decision gaps were not resolved for Akros.

## ERP scope and genuine overlap with commerce/admin

The historical ERP plan is broad: property/unit structure, rental, pricing/billing/payment basics, accounting handoff, costs, documents, facility work, reporting, roles, administration, integrations, and migration. It deliberately rejects implementing statutory accounting (`../context/ontos/01_CONTEXT_AND_CONSTRAINTS.md:5-17`; `../context/ontos/11_V0_SCOPE_AND_MODULES.md:11-63`).

The plan originally put the e-shop outside the Ontos runtime and deferred e-shop connector work to 2027 (`../context/ontos/03_ARCHITECTURE_OVERVIEW.md:15-19,41-43`; `../context/ontos/12_ROADMAP.md:47-49`). The new Akros-first destination therefore changes a foundational product assumption; the old boundary cannot be silently treated as current intent.

### Overlap that is genuinely reusable

| Capability | Why it overlaps | Boundary needed to avoid blocking Akros |
| --- | --- | --- |
| Staff identity, Tenant/Legal Entity context, authorization and policy | Commerce Admin and ERP both need controlled staff operations and audit attribution | Storefront customer accounts and sessions need a deliberate contract; do not assume they are tenant-scoped staff Principals |
| CoreSDK Action/read envelope | Both products benefit from consistent authz, policy, idempotency, audit and transaction semantics | Commerce workflows may require stronger replay, locking, compensation and reconciliation than Ticketing |
| Audit, evidence, events and outbox | Orders, payments, exports, document generation and ERP sync all need durable effects | Production hardening is an Akros prerequisite; it cannot wait for ERP feature work |
| Media/documents | Product assets, order attachments, invoices, claims and ERP documents share storage/access needs | Commerce presentation assets and compliance evidence have different lifecycle/policy needs |
| Party/counterparty and addresses | Buyers, companies, suppliers and accounting counterparties overlap semantically | Ownership of Commerce Customer, B2B Company, contact, login identity and ERP Party is unresolved |
| Billing/accounting handoff | Order invoices, receivables, accounting export and evidence span commerce and ERP | Commerce Order/Payment state is not statutory accounting; decide which system owns each invoice and payment fact |
| Tasks, approvals and operational work | Claims, fulfillment exceptions, catalog QA and integration failures can use staff workflows | Generic Ticketing must not substitute for purpose-built Order/Claim/Fulfillment Admin behavior |
| Search/reporting foundations | Both products need permission-aware discovery and operational exports | Domain-specific indexes and financial correctness stay with owning modules |
| Import/export job runtime | Akros and ERP both need batching, retry, failure visibility and reconciliation | Connector mappings and record authority remain integration-specific and independently deployable |

The old core's ERP/IS connector report describes this last seam as a reusable e-commerce integration capability covering product, category, customer, order, invoice, stock, price, parameter and related data; it requires batch continuation, partial success and logs while explicitly excluding replacement of the ERP itself (`../context/stare_jadro_featury/output/40-erp-and-is-connector-framework.md:1-45`).

Akros source provides primary corroboration. Its Abra client exposes operations for catalog, prices, customers, categories, orders, invoices, relations and stock (`../context/team-1633516729428-akros-124b361ce570/project/is/client/client.php:28-198`). Its configuration points to the Akros Abra web-service endpoint and staged import/export directories (`../context/team-1633516729428-akros-124b361ce570/project/is/client/Abra/config.php:3-23`). This establishes an Akros-critical integration surface, but not the future direction or source-of-truth rules for each entity.

### ERP work that need not gate Akros

Property/rental registries, lease and reservation behavior, facility-specific operations, property ownership/management assignments, property reporting, full ERP finance planning, and unrelated future manufacturing/ontology work are not dependencies merely because they were planned on the same kernel. Akros should consume a shared capability only when the Akros slice needs it and the interface can remain stable without finishing its ERP consumers.

## Missing commerce capability and production risk

The August hybrid assessment found no Ontos implementation of catalog, variants, pricing, price lists, promotions, cart/checkout, order lifecycle, payments/refunds, inventory reservation, fulfillment, tax, Commerce Admin, or durable compensated multi-step workflows (`../context/e-commerce_new-engine_dump/extracted-medusa-ontos-hybrid-strategy.md:41-59`). Current source inspection confirms that remains true at the referenced commit.

The broader Medusa exit assessment is valuable because it identifies what a framework replacement can accidentally omit:

- commerce state machines and workflow retry/locking/compensation;
- duplicate and out-of-order payment webhooks, external idempotency and refund correctness;
- historical totals, tax snapshots and the graph of order/payment/fulfillment records;
- inventory reservations tied to a cart/order rather than only aggregate quantities;
- pricing, volume discounts, regional/currency context and stable rounding;
- ERP synchronization, partial batch failure, replay and stuck-job recovery;
- the operator Admin shell and core product/order/customer screens;
- customer/admin auth, password/session migration and API keys;
- stable storefront DTO/response contracts;
- characterization and end-to-end tests for checkout, payment, refund, concurrency, fulfillment, ERP retry and cutover rollback.

Evidence: `../context/e-commerce_new-engine_dump/medusa-exit-assessment.md:45-232`.

Those findings were produced against a `new-engine` repository not included here. Their counts and named plugins should not be promoted into Akros scope without corroboration. The failure modes and proof scenarios remain strong general commerce evidence.

## Conflicting commerce proposals and their weight

### Existing headless engine plus sidecar

The Medusa exit assessment recommends a finished headless commerce engine for standard catalog/cart/order behavior plus a custom sidecar for B2B companies, approvals, quotes, ERP integration, carrier orchestration and custom operations/Admin (`../context/e-commerce_new-engine_dump/medusa-exit-assessment.md:268-282,351-364`).

**Weight:** a reasoned option analysis, not a selected vendor or proven Akros fit. It usefully warns that a custom commerce core is a separate multi-year product program.

### In-repo Medusa source fork

The fork plan recommends internalizing exact Medusa source without behavioral redesign, preserving DB/API/package compatibility first, then renaming and changing domains only after parity (`../context/e-commerce_new-engine_dump/medusa-in-repo-fork-plan.md:93-149,150-290,480-491`).

**Weight:** a detailed brownfield migration plan contingent on the unavailable `new-engine` dependency graph and on source ownership being a strategic goal. It creates an ongoing platform-vendor maintenance obligation.

### Ontos kernel plus Medusa-derived commerce compatibility

The later hybrid synthesis recommends Ontos as the target kernel while Medusa remains the source of commerce implementation, compatibility runtime and behavioral oracle. It explicitly recommends extracting only the kernel—not the whole Ontos workspace, Ticketing, or Module Federation Shell—and keeping Medusa the sole commerce writer initially (`../context/e-commerce_new-engine_dump/extracted-medusa-ontos-hybrid-strategy.md:3-11,61-84,139-195`).

**Weight:** the most recent synthesis and the only proposal that assessed the current Ontos commit, but still a historical report rather than an ADR, prototype result, or production decision. It does not establish that the unavailable Medusa-based system matches Akros's actual required capability set.

### What the historical work does establish

Regardless of the selected engine:

1. Characterize production behavior and hard workflows before replacement.
2. Put provider-neutral contracts between storefront consumers and the backend.
3. Keep one writer per aggregate; never nest independent Ontos and commerce transactions or synchronously dual-write.
4. Commit canonical state and outbox together; project or synchronize after commit.
5. Give payment and carrier callbacks exactly one owner during transition.
6. Move low-risk/read domains before pricing, cart, inventory, order, payment/refund and fulfillment writes.
7. Consider allowing old carts/orders to finish on the old writer and retaining history read-only rather than forcing a risky complete migration.
8. Do not combine source internalization with redesign of critical commerce invariants.

Evidence: `../context/e-commerce_new-engine_dump/medusa-exit-assessment.md:236-331` and `../context/e-commerce_new-engine_dump/extracted-medusa-ontos-hybrid-strategy.md:139-212`.

## Decision gaps exposed for the Wayfinder map

This research narrows, but does not answer, the following product/architecture decisions:

1. **Production-Akros acceptance boundary:** Which existing Akros and old-core capabilities are must-preserve, improve, migrate, archive or deliberately drop?
2. **Commerce foundation:** Does Medusa-derived hybrid, another headless engine plus sidecar, or native Ontos commerce best satisfy that boundary and the hardest proof scenarios?
3. **Kernel adoption:** Which Ontos contracts are retained, what production hardening is prerequisite, and should they be extracted without the present Shell/Ticketing application?
4. **Domain ownership and sources of truth:** Who owns Product/Catalog, Price, Inventory, Customer/Party/Company, Order, Invoice, Payment and Fulfillment across commerce, the smaller ERP and Abra?
5. **Application topology:** Is Commerce Admin a vendor dashboard, a shared Ontos Shell, an Akros operations application, or a combination with explicit boundaries?
6. **Identity topology:** How do staff Principals, B2B companies/buyers and retail customer accounts relate without forcing storefront auth into the historical tenant-scoped staff model?
7. **Integration contract:** Which current Abra flows are required at launch, with what direction, authority, failure/replay semantics, monitoring and reconciliation?
8. **Migration/cutover:** Which data moves, which remains archived, how writers change by domain, and how financial/order consistency is reconciled?
9. **ERP cutline:** Which shared foundations or admin capabilities are dependencies for Akros, and which property/rental/finance work remains parallel or out of scope?
10. **ADR reconciliation:** Which proposed Ontos ADRs are accepted, revised or rejected for the Akros-first destination?

## Resolution gist

Ontos provides evidence for a candidate shared kernel, not for a complete or already-decided commerce architecture. ERP overlap is real at runtime, staff operations, document/billing/reporting and integration seams, but should be consumed incrementally so property/rental ERP scope cannot gate Akros. The commerce foundation, domain ownership, Admin/identity topology, and cutover remain decisions. Historical Medusa work supplies strong behavioral and migration guardrails, while its competing target architectures remain proposals that must be judged against the production-Akros acceptance matrix.
