# `new-engine` current business evidence

Roadmap evidence for the Akros rebuild, inspected at `TechsioCZ/new-engine` commit `0db98293608f3f615318ff61f88c3d086e753de1` (`2026-08-22`, `fix(storefront): rebase absolute Payload media URLs onto the public CMS base`).

## Authority and evidence boundary

This repository is additional first-party evidence, not the authority for Wayfinder terminology or architecture. Canonical OntOS terminology and accepted OntOS rules govern. In particular, repository terms such as `company`, `customer`, `admin`, and `ERP` must be translated into the canonical **Party**, **Counterparty**, **Principal**, **Legal Entity**, **Commerce Operations**, **External Business System**, **System of Record**, and **Connector** vocabulary before they influence the roadmap.

The repository proves implemented code, intended deployment composition, and tested capability breadth at the inspected commit. It does **not** prove current production enablement, traffic, accountable business ownership, the identity of systems behind Symmy, or authority over any business fact. Local evidence paths below are relative to the `wayrepo` root.

## What the repository contains today

The concrete product is a Medusa-based commerce platform built around the **Herbatika** storefront. The intended deployed stack names Medusa, Payload, and Herbatika, while explicitly calling **N1** a selectable reference storefront excluded from automatic CI scope (`../../context/new-engine/README.md:82-89`). The root guidance likewise calls the demo storefronts reference implementations (`../../context/new-engine/AGENTS.md:10-18`). No Akros-named application or Akros reference appears in the tracked repository, so this is evidence for reusable commerce and delivery patterns—not proof that Akros already exists in `new-engine`.

Herbatika is concrete across four markets: Slovakia, Czechia, Hungary, and Romania, with named live/preview host contracts for each (`../../context/new-engine/apps/herbatika/tests/market-readiness/live-hostname-readiness.mjs:5-31`). It implements customer-facing catalog, search, account, product-list, Cart, Checkout, Order confirmation, content, review, and Claim journeys; representative code shows market-aware product discovery (`../../context/new-engine/apps/herbatika/src/pages/~sf/[market]/products/index.tsx:28-76`), Checkout routing (`../../context/new-engine/apps/herbatika/src/pages/~sf/[market]/checkout/index.tsx:1-16`), and account Orders, lists, and settings (`../../context/new-engine/apps/herbatika/src/pages/~sf/[market]/account/section/[section].tsx:14-68`). It also recognizes retail and wholesale registration paths (`../../context/new-engine/apps/herbatika/src/lib/auth/auth-form-validators.ts:25-47,64-65,149-166`). These are Herbatika business choices; they do not define Akros Channel or Counterparty policy.

## Roadmap-relevant capabilities

| Capability area | What the repository proves | Roadmap interpretation |
| --- | --- | --- |
| Commerce foundation | The backend has implemented extensions around Brand, measurements, product attributes/content, product lists, reviews, Claims, search, storefront text, company/employee handling, quotes, approvals, Orders, and multi-market URL/publication behavior (`../../context/new-engine/apps/medusa-be/src/config/modules.ts:214-305`). | Strong evidence that these are real commerce needs and candidate reusable e-commerce capabilities. It does not promote them to Core or shared business foundations; OntOS V1 must still demonstrate genuinely shared meaning. |
| Storefront reuse | The shared storefront-data package supports auth, Catalog, categories, Cart, Checkout, customers, Orders, product lists, products, collections, and regions (`../../context/new-engine/libs/storefront-data/README.md:149-188`). Its own rule keeps storefront-specific policy local until reused (`../../context/new-engine/libs/storefront-data/README.md:5-29,190-196`). | Confirms a useful separation between reusable commerce behavior and Storefront-owned composition. The code package boundary is evidence, not a mandated Wayfinder module boundary. |
| Market and content reuse | A separate CMS owns localized pages, articles, navigation groupings, hero content, and media concerns (`../../context/new-engine/apps/payload/README.md:1-14,126-129`), while shared i18n resolves markets and backend-managed messages but leaves market definitions and keys to each application (`../../context/new-engine/libs/storefront-i18n/README.md:1-5`). | Confirms the roadmap distinction between Content, Storefront, and reusable market support. Herbatika's four-market design does not establish Akros's launch markets. |
| B2B commerce | Implemented capability includes wholesale registration, companies and employees, customer groups, approvals, quotes, price lists, and saved product lists (`../../context/new-engine/apps/herbatika/src/lib/auth/auth-form-validators.ts:30-47,64-65`; `../../context/new-engine/apps/medusa-be/src/config/modules.ts:251-280`). | Confirms that B2B is more than a second presentation theme. The repository's `company` and `customer` models must not replace OntOS Party/Counterparty/Principal/Legal Entity distinctions. |
| Payment and Fulfillment | Payment integration includes cash on delivery, optional QR, and GoPay, Stripe, and Comgate providers; Fulfillment integration includes PPL, Packeta, and GLS (`../../context/new-engine/apps/medusa-be/src/config/modules.ts:29-48,83-109,138-191`). | Confirms provider replacement seams and multi-provider operations as roadmap concerns. It does not prove which providers Akros uses or which system owns payment, shipment, or financial facts. |
| Commerce Operations | A dedicated order dashboard is an internal Medusa extension, not a separate product (`../../context/new-engine/apps/medusa-order-dashboard-plugin/README.md:1-6`). It supports operational queues, filters, Order/payment/Fulfillment visibility, status changes, fulfillment creation, carrier labels, and expedition PDFs (`../../context/new-engine/apps/medusa-order-dashboard-plugin/src/admin/routes/order-dashboard/i18n.ts:1-24,25-77,79-125,176-205`). | Confirms **Commerce Operations** as a substantial business capability. It remains workflow composition over domain-owned behavior, in accordance with OntOS authority; “Admin” is only the implementation surface. |

## Symmy and other external-system evidence

The repository registers `medusa-symmy-plugin` in the commerce backend (`../../context/new-engine/apps/medusa-be/src/config/plugins.ts:5-23`). Under canonical vocabulary, this package is the **commerce-side Symmy Connector**; it is not Symmy itself. Its implemented exchanges cover at least products and Retail Customers (`../../context/new-engine/apps/medusa-symmy-plugin/src/api/api/symmy/v1/products/batch/route.ts:9-36`; `../../context/new-engine/apps/medusa-symmy-plugin/src/api/api/symmy/v1/customers/batch/route.ts:9-36`), customer groups and price lists (`../../context/new-engine/apps/medusa-symmy-plugin/src/api/api/symmy/v1/customer-groups/batch/route.ts:12-36`; `../../context/new-engine/apps/medusa-symmy-plugin/src/api/api/symmy/v1/price-lists/batch-upsert/route.ts:9-36`), stock, invoices, and shipment tracking (`../../context/new-engine/apps/medusa-symmy-plugin/src/api/api/symmy/v1/inventory/stock/batch/route.ts:5-29`; `../../context/new-engine/apps/medusa-symmy-plugin/src/api/api/symmy/v1/invoices/batch/route.ts:11-35`; `../../context/new-engine/apps/medusa-symmy-plugin/src/api/api/symmy/v1/tracking/batch/route.ts:11-35`). Completion/failure notification back to configured recipients is also implemented (`../../context/new-engine/apps/medusa-symmy-plugin/src/modules/webhook-config/service.ts:10-45`).

This materially strengthens the earlier evidence that Symmy is a broad Integration Hub rather than a product-only importer. It still does not name the multiple third parties behind Symmy, prove which exchanges are currently active for Akros, or make Symmy the System of Record.

Other concrete Connector/provider families are:

- Payload CMS for published Content (`../../context/new-engine/apps/medusa-be/src/modules/payload/README.md:1-5,80-99`).
- GoPay, Stripe, Comgate, QR, and cash-on-delivery payment paths (`../../context/new-engine/apps/medusa-be/src/modules/payment-paykit/README.md:1-18`; `../../context/new-engine/apps/medusa-be/src/config/modules.ts:29-48`).
- PPL, Packeta, and GLS Fulfillment paths (`../../context/new-engine/apps/medusa-be/src/config/modules.ts:83-109,138-191`).
- Google, Meta, Heureka, and Leadhub analytics/marketing adapters (`../../context/new-engine/libs/analytics/package.json:15-30`).

These prove replaceable integration needs, not Akros activity or business authority.

## Maturity and risk signal

This is a substantial, actively evolving implementation rather than a throwaway prototype. The repository has dedicated CI for affected projects and Herbatika URL/ingress behavior (`../../context/new-engine/.github/workflows/ci.yml:44-86`), plus backend unit, module-integration, and HTTP end-to-end suites (`../../context/new-engine/.github/workflows/medusa-be-tests-after-ci.yml:109-187`). Herbatika also has explicit four-market readiness checks covering hosts, sitemap, pages, SEO, currency, static content, and legal evidence (`../../context/new-engine/apps/herbatika/tests/market-readiness/gate.node-test.mjs:575-600`).

The principal roadmap risks are:

- **Product-fit risk:** the concrete merchant is Herbatika, not Akros. Reuse must be validated against Akros B2C/B2B outcomes and OntOS rules rather than assumed from technical similarity.
- **Authority risk:** the broad Symmy Connector shows exchange breadth but not fact ownership, override policy, or the third parties behind the Integration Hub.
- **Model-translation risk:** existing company/customer/admin vocabulary can collapse OntOS distinctions if copied directly.
- **Scope risk:** the codebase already spans Catalog enrichment, B2B, Content, search, payment, Fulfillment, Aftercare, Claims, SEO/URLs, and Commerce Operations. Akros needs an explicit launch cutline instead of inheriting all present breadth.
- **Documentation drift:** the Symmy README describes only product import (`../../context/new-engine/apps/medusa-symmy-plugin/README.md:1-11`), while current code covers several more business fact families. Roadmap decisions should rely on corroborated code and current operational confirmation, not a single package description.
- **Readiness-transfer risk:** CI and Herbatika release evidence demonstrate engineering maturity for this repository, but do not establish Akros production readiness, migration safety, live scale, or OntOS governance compliance.

## Effect on earlier Wayfinder assumptions

**Confirmed:**

- Symmy is appropriately treated as an Integration Hub, and the Medusa package as its commerce-side Connector; the Connector's breadth is now supported by current first-party code rather than only a static report.
- Catalog, Pricing, Inventory/Availability inputs, Retail Customer and B2B relationships, Orders, invoices, tracking, payment, Fulfillment, Content, and Commerce Operations all have concrete implementation evidence.
- Storefront, Content, reusable commerce behavior, Commerce Operations, Connectors, and infrastructure are meaningfully different ownership concerns.
- B2B needs distinct organization/relationship, approval, quotation, price-list, and purchasing capabilities; OntOS terminology must supply their canonical business interpretation.

**Corrected or constrained:**

- `new-engine` must not be described as the Akros replacement already in progress. It is a Herbatika-led commerce implementation with reference storefronts and reusable packages.
- Presence in this repository does not establish that a capability belongs in Core, that a provider is active for Akros, or that a fact should be owned by commerce rather than an External Business System.
- The earlier static `new-engine` audit remains historical evidence, but this current first-party snapshot supersedes it for repository capability breadth. Neither source proves live Akros activity.
- New provider evidence (notably GLS, GoPay, Stripe, and QR) expands the candidate landscape; it does not contradict legacy Akros candidates such as PayU or establish retain/replace decisions.

## Remaining Akros unknowns

Before using this evidence for authority or sequencing decisions, Wayfinder still needs business-level answers to:

1. Is Akros expected to adopt this commerce foundation, selectively reuse it, or only learn from it?
2. Which B2C and B2B outcomes from the implemented Herbatika/N1 breadth are in the Akros launch cutline?
3. Which third-party systems sit behind Symmy, which fact families move through it, and in which broad direction?
4. Which system is authoritative for Product, assortment, price, stock/availability, Party/Counterparty facts, Order status, payment, Fulfillment, invoice/credit documents, and Claims—and where are local overrides intentional?
5. Which payment, carrier, Content, search, communications, analytics, comparison-shopping, and regulatory-verification systems are active for Akros?
6. What open Orders, payments, shipments, invoices, Claims, approvals, identities, URL/SEO obligations, and retained history must survive migration?
7. Which capabilities have the same business meaning in OntOS V1 and therefore merit a shared business foundation? Similar implementation shapes in this repository are not sufficient.

These unknowns belong in the roadmap-level data-authority and migration decisions. Endpoint, schema, event, workflow, deployment, and provider-contract design remain deferred.
