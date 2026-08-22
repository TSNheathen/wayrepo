# Legacy commerce invariants and lessons for Akros

## Research question

Which domain rules, state transitions, operational capabilities, integration patterns, and failure lessons from the legacy WRShop core remain relevant to a production Akros replacement? This note separates durable commerce knowledge from generic unused breadth, Akros-specific policy, obsolete architecture, and speculative feature-catalog classifications. It also identifies behavior that should be characterized before replacement.

## Evidence and confidence

The evidence base has two different strengths:

1. `../context/team-1633516729428-engine-a6883ea926b4` is primary source code for the shared legacy engine snapshot. It proves that behavior or an extension surface existed in that snapshot, but not that Akros enabled or used it.
2. `../context/stare_jadro_featury/output` is a useful secondary synthesis of that source. Its own README says the 43 handoffs are business interpretations of static code rather than runtime proof, and that every feature includes explicit hypotheses. Its `Must Have`, `Nice to Have`, and `Unknown / Verify` buckets describe a proposed B2B product portfolio, not the Akros launch scope. [`../context/stare_jadro_featury/output/README_PRODUCT_CATALOG.md:5-27,31-36,97-110`]

Claims below are therefore marked implicitly as:

- **Durable** when the behavior is a commerce invariant or an operational requirement supported by both code structure and feature analysis.
- **Akros-candidate** when Akros or its ABRA integration appears directly, but production intent still requires confirmation against the Akros repository, data, configuration, or stakeholders.
- **Hypothesis** when only generic engine breadth or the static feature catalog supports it.
- **Reject** when the evidence is an implementation mechanism or failure mode that should not define the new architecture.

## Durable commerce knowledge

### Commercial context is explicit and multi-dimensional

A commercial decision is not made for a product in isolation. The legacy behavior repeatedly resolves it using some combination of domain, language, currency, country/tax zone, customer or customer group, price type, quantity, store, and current time. In particular:

- Product and category publication are domain-specific. A product can be visible in one domain and hidden in another; category visibility controls navigation in that domain. [`../context/stare_jadro_featury/output/01-catalog-core.md:29-38`]
- A price is identified by product, domain, price type, currency, and quantity threshold. VAT is country- and domain-aware, while currencies carry rounding and precision rules. [`../context/stare_jadro_featury/output/07-pricing-tax-and-availability.md:26-34`]
- A customer account can carry B2B status, domain, preferred price/payment/shipping behavior, group rules, parent/partner relationships, balances, and multiple typed addresses. [`../context/stare_jadro_featury/output/11-customer-accounts-addresses-groups-and-contracts.md:16-23,31-38`]
- The basket constructor itself obtains domain, language, currency, account overrides, shipping, payment, store, and balance context before calculating the basket. [`../context/team-1633516729428-engine-a6883ea926b4/wrshop/Control/BasketControl.php:123-163,167-206`]

**Direction constraint:** the replacement needs one explicit, inspectable commercial context passed into catalog visibility, pricing, basket validation, checkout, and integrations. Hidden ambient context would reproduce the legacy coupling.

### Catalog lifecycle protects commerce history

The legacy catalog treats products, categories, producers, suppliers, stores, parameters, parameter groups, product groups, packages, prices, availability, media, and external identifiers as related operational master data. Product publication requires at least a title, unique business code, and category; missing main category can fall back to the first assigned category. Products referenced by orders are retired rather than physically erased, preserving historical commerce. [`../context/stare_jadro_featury/output/01-catalog-core.md:16-21,29-45`]

Parameters are reusable multilingual definitions with values and units, linked through groups to categories and products rather than embedded as arbitrary product fields. [`../context/stare_jadro_featury/output/02-product-parameters-and-groups.md:1-4,13-37`]

**Direction constraint:** replacement and migration must distinguish mutable sell-side catalog state from immutable historical order facts. External identifiers and source mappings must be first-class, because migration and ERP synchronization depend on stable identity across systems.

### Basket and checkout are a policy boundary, not CRUD

The durable purchase-path behavior includes:

- A basket persists for customer/session and domain.
- Repeated additions merge only when product and configuration identity match.
- Quantity obeys minimum quantity, unit-volume increments, stock, sell-out, and buyability rules; zero removes a line.
- Shipping and payment are filtered by basket total, weight, stock state, destination, and compatibility with each other.
- Missing/invisible/out-of-stock/priceless products, minimum-order violations, saldo rules, and deployments requiring registration can block checkout.
- A successful checkout creates order head, item, shipping, payment, and extras records, then clears a non-permanent basket. [`../context/stare_jadro_featury/output/08-basket-and-checkout-core.md:12-18,25-40,42-47`]

The 5,514-line `BasketControl` also contains discounting, price resolution, OSS, shipping eligibility, package limits, balance validation, default/permanent baskets, and order creation. That breadth is evidence that these policies interact; it is not evidence they belong in one module. [`../context/team-1633516729428-engine-a6883ea926b4/wrshop/Control/BasketControl.php`, symbols `createOrder`, `validateBasket`, `fetchPrices`, `runShipping`, `runPayment`, `runDiscounts`, `runOss`, `checkPackages`]

**Direction constraint:** define checkout as an externally testable orchestration seam with explicit policy inputs and an order result. It should coordinate pricing, availability, delivery, payment, customer-credit, tax, and inventory policies without owning all of them.

### Orders are durable operational records with guarded transitions

The legacy back office treats an order as more than checkout output:

- Localized order statuses are configurable and may trigger customer email.
- Operators create invoices from orders and add internal or customer-facing notes and attachments.
- Internal notes suppress requested email delivery.
- Claims have configurable statuses, due days, history, notification behavior, and guarded deletion.
- Saldo validation can warn or block and can force cash-only payment; domain minimum order becomes the fallback constraint. [`../context/stare_jadro_featury/output/10-order-management-invoicing-claims-and-saldo.md:13-20,29-47`]

Payment callbacks also represent state transitions: confirmed payment marks order and items paid; waiting, cancel, and reverse map differently; reverse clears the paid flag; callbacks require a resolvable transaction/order link and duplicate paid processing should not create a second paid state. [`../context/stare_jadro_featury/output/37-payment-gateway-framework.md:13-19,27-42`]

**Direction constraint:** order, payment, invoice, fulfillment, customer communication, and claim transitions need explicit state machines or commands, authorization, audit history, and idempotency. Historical financial records and system-owned states must not be casually deletable.

### B2B assortment and pricing rules are potentially product-defining

The legacy engine supports behavior that may be essential to Akros rather than optional platform polish:

- A customer-specific whitelist overrides the domain default; allow-lists and deny-lists can apply through products, categories, producers, and normalized category ancestry. The same effective visibility is intended across browse, search, navigation, breadcrumbs, and direct access. [`../context/stare_jadro_featury/output/31-whitelist-and-personalized-visibility.md:1-7,24-35`]
- Direct discount winner precedence is customer item, customer category, product, category, then producer; category discounts can inherit to descendants; quantity pricing selects the highest reached threshold. [`../context/stare_jadro_featury/output/22-promotions-engine-baseline.md:24-33`]
- Coupons carry domain, validity, minimum basket value, usage limit, stacking, customer, category, and shipping restrictions. [`../context/stare_jadro_featury/output/23-coupon-engine.md:24-35`]
- Bulk basket import matches supplier/business code, EAN, or title against the visible sellable assortment, handles ambiguity explicitly, and can partially succeed with warnings. [`../context/stare_jadro_featury/output/34-basket-import.md:24-38`]

These rules should not automatically enter Akros scope, but the replacement cannot safely simplify them until production usage and negotiated customer behavior are checked.

### Administration and operational recovery are part of the product

A complete production shop needs operators to manage catalog, prices, orders, accounts, content, localization, tax zones, bank accounts, admin identities and rights, imports/exports, feeds, and integration failures. The old core also includes catalog completeness checks for missing content/media/parameters. [`../context/stare_jadro_featury/output/26-import-export-and-feed-management.md:16-21,29-45`; `../context/stare_jadro_featury/output/27-datafill-and-catalog-qa.md:1-7`; `../context/stare_jadro_featury/output/29-settings-rights-localization-and-master-data.md:14-24,34-59`]

Some recovery tools are dangerously low-level, but the underlying need is durable: operators need observable jobs, failure diagnostics, retries, route/search/feed rebuilds where derived state exists, and safe correction workflows.

### Integration work is an asynchronous, auditable domain

The generic ERP/IS layer supports configured import/export for products, categories, prices, stock, parameters, customers, orders, invoices, attachments, and mappings across multiple transports. Its strongest reusable lessons are batch processing, continuation state, staging external sources, partial success, failure logs, and cleanup only after successful completion. [`../context/stare_jadro_featury/output/40-erp-and-is-connector-framework.md:13-29,32-45`]

The legacy deployment runs integration work every two minutes through `index.php -fisservice`, and warns that a MariaDB pricing function and its definer must survive database import. Those are operational coupling warnings, not architecture to preserve. [`../context/team-1633516729428-engine-a6883ea926b4/readme.txt:7-14`]

**Direction constraint:** integration jobs need explicit ownership, checkpoints, idempotency, audit and retry semantics. Provider payloads and credentials belong behind adapters; application/web bootstrap and database-installed business functions should not be the job boundary.

## Akros coupling that must be resolved deliberately

### ABRA appears to be a concrete Akros boundary

The only direct Akros application reference in the engine code is an example ABRA endpoint containing `/AKROS/PDMIssuedDocs`. [`../context/team-1633516729428-engine-a6883ea926b4/wrshop/Module/Is/AbraApi/Control/OrderControl.php:190-206`]

The surrounding ABRA adapter is substantial. `AbraApi\BaseControl` declares imports for products, stock, categories, prices, parameters, customers, orders and invoices, plus order/customer export. It recalculates store totals around stock import and performs work in batches. [`../context/team-1633516729428-engine-a6883ea926b4/wrshop/Module/Is/AbraApi/BaseControl.php:19-51,77-132,153-187`]

This is strong evidence that an ABRA integration boundary matters to Akros. It is not enough to decide which system is authoritative for every entity or which flows remain live today.

### Akros-like policy leaked into the shared ABRA mapping

`AbraApi\Model\Import\ProductModel::beforeR()` embeds rules that must be classified one by one:

- ABRA IDs map to product, producer, supplier, category, VAT and other internal identities.
- SKU length determines `base_product`; the first eight SKU characters determine parent/group identity.
- `X_IndivisibleQuantity` determines both minimum quantity and unit volume.
- ABRA category mappings drive merchandising preferences and visibility.
- SKU prefix `3` marks fixed prices and fabricates dimensions `1 × 1 × 100`.
- Missing guarantee length defaults to `720`. [`../context/team-1633516729428-engine-a6883ea926b4/wrshop/Module/Is/AbraApi/Model/Import/ProductModel.php:12-46,54-68,77-110`]

`AbraApi\Model\Export\OrderModel::beforeR()` also embeds concrete operational policy:

- An order without a main store is not exported.
- Constant symbol `0000008000`, creator `SUPER00000`, `X_Zprava_Eobchod`, ABRA document queue/division mappings, external customer-order code, complete-order-only behavior, payment/shipping mappings, store-card IDs and VAT IDs shape the payload.
- Zero-priced rows are omitted. [`../context/team-1633516729428-engine-a6883ea926b4/wrshop/Module/Is/AbraApi/Model/Export/OrderModel.php:77-153`]

These rules are **Akros-candidates**, not shared-commerce rules. Each must be confirmed against current Akros configuration, ABRA schema/data, and operator expectations. Valid rules should live in an Akros/ABRA policy adapter, expressed with business names rather than magic prefixes, codes, and field names.

### The concrete override surface is outside this engine snapshot

The loader searches `PROJECT_DIR/wrapp` and `PROJECT_DIR` as class sources and explicitly supports overridden classes; templates are also loaded from project directories elsewhere in the engine. [`../context/team-1633516729428-engine-a6883ea926b4/wrshop/Core/Loader.php:28-80`]

Therefore this engine cannot establish complete Akros behavior by itself. The Akros sibling repository, its configuration/version data, database shape, integration logs, feeds, templates and actual storefront flows are required to distinguish:

- shared legacy behavior Akros uses,
- Akros overrides and policy,
- generic dormant capability,
- stale or abandoned code.

## Generic capability hypotheses, not Akros requirements

The feature catalog inventories a broad reusable B2B suite. The following are real code surfaces but remain hypotheses for a production Akros replacement until Akros evidence confirms them:

- configurable-product trees and package types;
- wishlists, product repositories, watchdog alerts, ratings/forum, surveys and newsletters;
- add-on services, coupons, flash promotions, auctions and free gifts;
- internal admin messaging, proposal builder and client-center/intranet workflows;
- marketplace APIs for Mall and Heureka Kosik;
- dozens of payment gateway, carrier, ERP and storefront-script adapters;
- multi-domain operation beyond the specific domains Akros needs.

The catalog itself places 12 features in `Unknown / Verify`, including shipping orchestration, ERP/IS connector framework, marketplace APIs and several internal tools, and says runtime configuration, database rows and a credible client use case must be verified before product commitment. [`../context/stare_jadro_featury/output/README_PRODUCT_CATALOG.md:97-110`]

Conversely, its `Must Have` list should not bypass Akros discovery. For example 2FA, personalized visibility, multidomain navigation and feed management are sensible platform capabilities, but their exact Akros launch requirement and operational shape must still come from Akros evidence. [`../context/stare_jadro_featury/output/README_PRODUCT_CATALOG.md:38-63`]

## Architecture to reject

The following mechanisms are historical evidence, not foundations to carry forward:

### Global mutable runtime

`WrShop\Core\Engine` holds request, user, basket, session, route, controls, managers and configuration, exposes a mutable singleton, creates the request from globals, and performs application boot/service dispatch. [`../context/team-1633516729428-engine-a6883ea926b4/wrshop/Core/Engine.php:93-150,152-205`]

Reject ambient singleton context and service-location. Dependencies and commercial context need explicit inputs so rules are independently testable.

### Filesystem shadowing and client inheritance

The custom autoloader searches legacy paths, project override paths, engine paths, libraries and `src`, with case rewrites and hard-coded fallbacks. [`../context/team-1633516729428-engine-a6883ea926b4/wrshop/Core/Loader.php:28-120`]

Reject same-name class shadowing as the customization model. Use explicit modules, policies and provider adapters with stable contracts.

### God classes and coupled policy pipelines

Core behavior is concentrated in very large files: `BasketControl.php` has 5,514 lines; legacy `product.php` 4,581; `orders.php` 3,572; `product_list.php` 3,250; `page.php` 3,161; product and customer CMS modules each approach 3,000. This makes policy precedence, side effects and seams difficult to discover.

Reject controller/module ownership of whole business processes. Preserve end-to-end orchestration while separating pricing, catalog, inventory, checkout, order, payment, fulfillment and integration policy behind explicit seams.

### Mixed generations without a completed migration boundary

Legacy models/managers and raw SQL coexist with a narrow Doctrine entity layer. Orders appear as `WrShop\Model\OrderModel`, `Order2Model`, and `WrShop\Entity\OrderAggregate\Order`. The Symfony route layer is mostly legacy test/admin routes, including duplicate route names. [`../context/team-1633516729428-engine-a6883ea926b4/config/routes.php:3-15`]

Reject architecture-by-gradual-framework-overlay without a bounded replacement path. New work should deliver end-to-end slices through deliberate domain seams.

### Configuration entropy and magic policy

The snapshot has hundreds of version/config keys, timestamped configuration copies, deployment constants, numeric balance/status types, SKU-prefix rules and hard-coded ERP values. The basket even contains an empty `newBasket` branch. [`../context/team-1633516729428-engine-a6883ea926b4/wrshop/Control/BasketControl.php:132-164`]

Reject a universal flag matrix. Feature/module decisions and client policies should have typed names, ownership, validation and a constrained extension surface.

### Database and web-entry coupling

Pricing depends on the MariaDB `price_single` function, referenced by listings and marketplace code, while integration jobs enter through the main web application. [`../context/team-1633516729428-engine-a6883ea926b4/readme.txt:7-14`; `../context/team-1633516729428-engine-a6883ea926b4/wrshop/migration/price_single.php:7-31`]

Reject hidden database-installed business behavior and the web front controller as the integration scheduler contract.

### Missing executable characterization safety net

No first-party PHPUnit configuration or coherent application test suite is evident outside vendored packages and ad hoc scripts. The modernized test controller uses development-only routes and debug termination rather than automated behavior assertions. [`../context/team-1633516729428-engine-a6883ea926b4/src/Controller/Legacy/TestController.php`; `../context/team-1633516729428-engine-a6883ea926b4/config/routes.php:6-15`]

Reject migration by code resemblance. Legacy rules need behavior-level fixtures and production examples before simplification.

## Behaviors requiring characterization before replacement

Characterization should use the highest observable seam available: given representative Akros catalog/account/integration inputs, observe storefront eligibility and price, basket/checkout outcome, persisted order snapshot, outbound ABRA payload, and inbound status/invoice result. The priority order is:

1. **ABRA authority and mappings:** source of truth per entity; import/export direction; identifier lifecycle; deletion/deactivation; checkpoints; partial failure; retry and duplicate behavior.
2. **Product identity and grouping:** eight-character grouping, SKU-prefix behavior, variants/base products, category mappings, guarantee defaults, indivisible quantities, fixed prices and package dimensions.
3. **Effective availability:** which stores count, supplier/store availability, reservations, sell-out/backorder behavior, delivery date and storefront labels.
4. **Price resolution:** price-type selection by domain/customer, quantity tiers, VAT/OSS/currency rounding, fixed/action/normal prices, direct discount precedence and zero/missing-price behavior. The MariaDB `price_single` function and basket calculations are likely separate sources of truth today.
5. **Effective assortment:** domain visibility, Akros customer/group restrictions, whitelist inheritance and consistency across listing, search and direct access.
6. **Basket line identity and quantity normalization:** product configuration, duplicate merges, unit volume, minimums, stock limits, persistent/permanent baskets and bulk imports.
7. **Checkout gates:** registration, address/company validation, minimum order, saldo/credit, complete-order-only, shipping/payment compatibility, pickup point, packages and stock allocation.
8. **Order snapshot and lifecycle:** exact copied fields, numbering, initial status, notes, emails, payment callbacks, cancellation/reverse, operator transitions and audit history.
9. **ABRA order export:** main-store requirement, zero-priced rows, constant/document/division mappings, customer/contact identity, payment/shipping mapping, retry/idempotency and what marks an order processed.
10. **Invoice and status return:** matching keys, authorization, files/PDFs, status translation, late or duplicate updates and operator recovery.
11. **Operational administration:** which screens/jobs Akros operators actually use to correct catalog, prices, customers, orders, feeds and failed integrations.
12. **Production non-functional behavior:** job volumes and duration, stale-data tolerance, cache/search/feed rebuilds, observability, security/2FA/rights, backup and recovery.

Generic features should be characterized only after Akros evidence places them on the destination path.

## Resolution

The durable legacy contribution is a set of commerce invariants and operational cases, not a reusable architecture. A production Akros replacement must explicitly model commercial context, catalog lifecycle, pricing/availability, basket and checkout policy, durable order/payment/fulfillment transitions, back-office recovery, and auditable integration work. ABRA is a concrete Akros boundary and the old adapter contains likely Akros policy, but its magic mappings must be validated and isolated rather than promoted into shared core.

The generic 43-feature catalog is useful as a completeness checklist and fog generator. It must not define Akros scope without evidence from the Akros implementation, configuration, data, integrations and operators. The old singleton, project class shadowing, god classes, flag matrix, mixed persistence generations, database business functions and web-entry cron are architecture to reject.
