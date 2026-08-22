# Akros production product and operating baseline

Research for the Wayfinder decision ticket **Reconstruct the Akros production product and operating baseline**.

All source paths below are relative to the `wayrepo` root. They point into the adjacent evidence archive under `../context`. The archive contains production-derived code and data artifacts, but not the live database or a trustworthy runtime configuration export.

## Evidence grades

| Grade | Meaning in this note |
| --- | --- |
| **Observed deployment fact** | Directly established by Akros entry points, host configuration, or an active Akros-specific extension seam. This still does not prove the archived version is exactly what production serves today. |
| **Documented Akros intent** | Explicitly described by Akros' own implementation guide and backed by a corresponding project template or integration. Strong requirement evidence, but not direct observation of current traffic. |
| **Supported / feature-gated** | Code or templates exist and are selected through settings, domain data, licenses, or runtime conditions. Presence does not prove Akros enabled or used the capability. |
| **Historical / conflicting** | Older scripts, adapters, or migrations remain in the deployment archive alongside newer mechanisms. They are evidence of domain knowledge and migration risk, not automatically the current direction. |
| **Unknown** | The archive lacks the live database, external-system configuration, operational telemetry, or stakeholder evidence needed to resolve it. |

## Executive finding

Akros is a thin customer deployment over the old shared WRShop engine, not a standalone e-commerce application. Both the storefront and CMS entry points bootstrap a sibling `engine/wrshop/run.php`; Akros contributes deployment configuration, storefront and email templates, assets, custom operational scripts, and an Akros-specific ABRA product mapping. The Nginx configuration routes both `www.akros.cz` and `b2b.akros.cz` to the same deployment. These are **observed deployment facts**. Sources: `../context/team-1633516729428-akros-124b361ce570/public_html/index.php`, `../context/team-1633516729428-akros-124b361ce570/public_html/cms/index.php`, and `../context/team-1633516729428-akros-124b361ce570/conf.d/nginx.conf`.

The strongest product description is Akros' own `project/readme.html`. It documents a rich catalog, product detail, basket, and content-administration experience and names the corresponding CMS locations. The checked-in templates substantiate those flows. This is **documented Akros intent**, not proof that every setting remains enabled in production. Source: `../context/team-1633516729428-akros-124b361ce570/project/readme.html`.

ABRA is a first-class business boundary. The newer `AbraApi` path imports catalog and customer data, exports customers and orders, imports order/status and invoice information, and obtains authorized invoice PDFs. However, legacy SOAP/FTP/XML scripts and an `AbraG3` generation coexist with `AbraApi`, so the authoritative current integration cannot be established from code presence alone. Sources: Akros `project/is/`, Akros `project/wrshop/Module/Is/AbraApi/Model/Import/ProductModel.php`, and shared-engine `wrshop/Module/Is/AbraApi/`.

The principal limitation is decisive: the archive has no Akros SQL/database snapshot. Domain settings, license flags, CMS content, active price lists, payment and shipping setup, account population, identifiers, and feature enablement live in data that is not present. The baseline can therefore say what the deployment is designed to do and what it depends on, but a live audit is required before declaring the exact replacement scope.

## Storefront baseline

### Homepage, navigation, content, and discovery

**Documented Akros intent:**

- Header navigation, logo, stock count, search, quick product selection, basket, login, and favorites.
- Product category navigation plus special collections such as action, new, recommended, most wanted, discounted, and in-stock products.
- Fixed banners, a rotating special offer, category/menu/footer/modal banner positions, selected homepage products, news articles, visited products, survey, quick contacts, social links, newsletter, and editable header/footer navigation.
- CMS ownership is explicitly documented: text/system pages, categories, settings, domain configuration, banners, special offers, articles, and surveys.

Primary source: `../context/team-1633516729428-akros-124b361ce570/project/readme.html`, section “Domácí stránka”; implementation templates include `project/templates/content/content.homepage.tpl`, `project/templates/components/menu.top.tpl`, `project/templates/components/menu.left.tpl`, and `project/templates/footer.tpl`.

**Documented Akros intent:** category and search pages provide breadcrumbs, category copy, subcategories, category-specific banners, bestsellers, long-tail parameter filtering, sorting, pagination, and product-list buying. Product cards expose image, availability, preference/state markers, group/variant information, rating, normal and final prices, favorites, and direct purchase where possible. Sources: `project/readme.html`, section “Kategorie, hledání”; `project/templates/content/content.list.tpl`; `project/templates/components/block.products.tpl`; and `project/templates/components/view.product.tpl`.

**Supported / feature-gated:** multiple list views, product comparison, visited products, favorites, ratings, and recommendations depend on settings or object state. The compare action is commented out in `project/templates/content/content.detail.tpl` even though comparison templates remain, so comparison is specifically not safe to call a current Akros requirement.

### Product model and product detail

**Documented Akros intent:** product detail includes:

- Multiple images, state/preference markers, rating, manufacturer and supplier information.
- Short and full descriptions, product code, EAN, warranty, weight, measure unit, age restriction, and store availability.
- Normal and customer price, VAT-inclusive and VAT-exclusive presentation, quantity prices, recycling and copyright fees, extra freight charge, cheapest-shipping information, and an explicit warning when price is not guaranteed.
- Gifts, promotional actions, free shipping, and delivery guarantee.
- Tabs for description, technical description, parameters, video, attachments/downloads, manufacturer, product patron, and discussion/reviews.
- Product groups and variants with parameter filtering, related/accessory products, alternate products, and a `components` relation.
- Product configurations and selectable sets, including image choices, priced options, and bounded free-form dimensions.

Primary sources: `../context/team-1633516729428-akros-124b361ce570/project/readme.html`, section “Detail produktu”; `project/templates/content/content.detail.tpl`; `project/templates/content/detail/block.tabs.tpl`; `project/templates/content/detail/block.products.group.tpl`; `project/templates/content/detail/block.configuration.tpl`; `project/templates/content/detail/form.configuration.set.tpl`; and `project/templates/components/block.prices.full.tpl`.

**Observed Akros-specific integration fact:** `WrShop\Module\Is\AbraApi\Model\Import\ProductModel::beforeR()` encodes business semantics that a replacement must explicitly preserve, revise, or drop:

- ABRA product code length determines base product versus variant.
- The first eight characters of a product code determine product grouping.
- ABRA store-card category identifiers map through local `identifiers` to storefront preference flags and visibility.
- Supplier packing and indivisible quantities become unit volume and minimum order quantity.
- Product codes beginning with `3` receive a special package-dimension record.
- Product, supplier, producer, category, content, visibility, preference, and stock relationships are prepared across configured domains.

Source: `../context/team-1633516729428-akros-124b361ce570/project/wrshop/Module/Is/AbraApi/Model/Import/ProductModel.php`, symbol `ProductModel::beforeR()`.

### Basket and checkout

**Documented Akros intent / backed by templates:**

- AJAX cart additions, minimum-quantity steps, whole-package purchase, configurations/sets, stock and price validation, cart update/removal, and order summary.
- Fast ordering by product code or EAN, plus bulk import from textarea or CSV. The top navigation links to the quick-selection flow.
- Personal or company purchase, invoice and delivery addresses, multiple stored addresses, company lookup through ARES, and VAT validation through VIES.
- Shipping and payment selection based on basket eligibility, per-method prices, free-shipping thresholds, payment discounts, pickup branches, and individual/estimated shipping prices.
- Vouchers, presents, bonus points, discount thresholds, minimum order value, customer purchase-order number, “deliver only complete order,” requested delivery/pickup date, order note, adult-goods confirmation, and legal/marketing consents.
- Printable offer/order PDF behavior is explicitly named in the Akros guide.

Primary sources: `project/readme.html`, section “Košík”; `project/templates/content/content.basket_aio.tpl`; `project/templates/content/content.basket_import.tpl`; `project/templates/components/form.basket.import*.tpl`; and `project/templates/content/basket/block.*.tpl` under the Akros archive.

**Supported / feature-gated:** PPL pickup widget and Packeta/Zásilkovna have dedicated Akros templates; TopTrans and Geis have conditional behavior in the shipping block. Payment is broker-driven, with a PayU form present and a Comgate-specific Akros configuration flag. These prove integration support, not current provider enablement. Sources: `project/templates/content/basket/branch/block.PPLApi.tpl`, `block.zasilkovna.tpl`, `project/templates/content/basket/block.shippings.tpl`, `project/templates/utilities/form.payu.tpl`, and `project/config.php`.

**Unknown:** currently accepted countries, currencies, VAT rules, checkout registration policy, actual carriers, actual payment methods, free-shipping thresholds, minimum order value, and enabled consent programs are all database/settings facts.

## Customer account and B2B baseline

### Customer self-service

**Supported / strongly substantiated by Akros templates:**

- Registration, login, password recovery, profile maintenance, company data, invoice address, and multiple delivery addresses.
- Searchable order history by date, status, and order number; order detail, PDF, shipment/tracking references, invoice links, and repeat order.
- Searchable invoice history and PDF retrieval.
- Claim filing against an order item, product serial number, description and attachments; claim overview, current status, related components, and history.
- Favorites/wishlist and watchdog notifications for price or availability.
- GDPR consent history and optional bonus/discount/account-balance presentation.

Primary sources: `../context/team-1633516729428-akros-124b361ce570/project/templates/content/user/block.orders.tpl`, `block.order_detail.tpl`, `block.invoice_list.tpl`, `block.claim.tpl`, `block.claim_overview.tpl`, `block.claim_log.tpl`, `block.homepage.tpl`, `block.wishlist.tpl`, and `block.watch_dog.tpl`. Lifecycle email templates are under `project/templates/.mail/`.

**Unknown:** which account modules are visible in production and how much historical account data is retained. Auctions and consignment templates also exist, but there is no Akros-specific product documentation proving current use; treat them as merely supported until a runtime or stakeholder audit says otherwise.

### B2B

**Observed deployment fact:** both `www.akros.cz` and `b2b.akros.cz` route to the deployment. Source: `../context/team-1633516729428-akros-124b361ce570/conf.d/nginx.conf`.

**Supported / substantial Akros template evidence:**

- Customer-specific price types and discounts, “my price,” VAT display preference, quantity pricing, minimum quantities, whole-package purchase, and assigned-store availability.
- Parent/subordinate accounts: create, edit, activate/deactivate, impersonate, inspect subordinate orders and baskets, and inherit parent price list, payment, shipping, avatar, and VAT-display choices.
- Parent assignment of permitted stores/groups and per-product purchase limits with maximum, validity, and repeat interval.
- Basket type `7` approval behavior, including approval and return to the subordinate account.
- Trade-oriented quick selection and bulk basket import.

Primary sources: `project/templates/content/user/block.users.tpl`, `block.user_edit.tpl`, `block.user_settings.tpl`, `block.user_baskets.tpl`, `block.user_detail.tpl`, `block.user_stores_preview.tpl`, `project/templates/content/basket/block.submit.tpl`, and `project/templates/content/content.basket_import.tpl`.

**Historical / migration evidence:** `project/import2.php` maps ABRA price-list codes to local `customer.id_price_type`, including local price types 1, 6, and 15. `project/is/client/catalog.php` derives quantity breaks, and `project/is/client/cenik.php` imports assortment discounts keyed by company identifier. These establish real B2B pricing concepts but not necessarily the current algorithm.

**Unknown / product decision:** whether the first new Akros must include both B2C and B2B storefronts at launch; exact account hierarchy and approval usage; price-list/discount precedence; store/group visibility; product limits; and which data ABRA versus the e-commerce back office owns.

## Administration and operating model

### Back office

**Documented Akros intent:** the Akros guide identifies CMS ownership for categories, products, images, descriptions, preferences, parameters, video, attachments, relations, manufacturers, content/system pages, navigation, banners, special offers, homepage product selection, articles, surveys, newsletter, social links, quick contacts, delivery guarantee, and domain settings. Source: `../context/team-1633516729428-akros-124b361ce570/project/readme.html`.

**Observed architectural fact / supported modules:** Akros' CMS delegates to the shared engine. The shared engine contains administration modules for products, categories, parameters and configurations; orders, customers, invoices and claims; payment, shipping, stores, zones, currencies, VAT and price types; promotions, coupons, gifts and discounts; text, banners, special offers, surveys and newsletters; domains, settings, email templates, logs, identifiers, import/export, and IS services. Source directory: `../context/team-1633516729428-engine-a6883ea926b4/wrshop/cms/modules/`.

This module inventory establishes the available old-platform surface, not the licensed or used Akros subset. The target back office should be derived from Akros workflows and system-of-record decisions, not by cloning all 77 legacy CMS modules.

### Deployment and operations

**Observed deployment facts:**

- Nginx terminates TLS for `www.akros.cz` and `b2b.akros.cz`, serves a shared document root, and forwards PHP to an Akros-specific FPM socket.
- PHP/FPM is configured separately under `conf.d/php82-fpm.conf`.
- The project has storefront, CMS, AJAX, and IS-service entry points rather than a single application boundary.
- The archive contains hard-coded production credentials and customer data artifacts. This note intentionally does not reproduce either.

Sources: `../context/team-1633516729428-akros-124b361ce570/conf.d/nginx.conf`, `conf.d/php82-fpm.conf`, `public_html/index.php`, `public_html/cms/index.php`, `public_html/cms/ajaxconnector.php`, and `project/config.php`.

**Historical / concerning artifact:** `public_html/api.php` is an older Heureka endpoint with old include conventions, a hard-coded domain id, raw request/server dumping, and local transfer logging. It is evidence that Heureka order/API behavior existed, but must be classified explicitly as retain, replace, or remove; it should not be reproduced as-is.

**Unknown:** deployment topology beyond this vhost, cron/job orchestration, database version and size, media storage, queues, cache/search services, email provider and deliverability, monitoring, alerting, backups and restore tests, release process, traffic/load, availability targets, incident history, and actual external credentials/endpoints.

## Integration and data flows

### Newer ABRA API path

**Observed shared-engine extension seam:** `WrShop\Module\Is\AbraApi\BaseControl` declares these flows:

- ABRA → shop: product, product stock, product category membership, product prices, parameters and values, product parameters, categories, customers, recent orders/status data, and order invoices.
- Shop → ABRA: order export; customer export is invoked as part of order export.
- User request → ABRA: invoice PDF retrieval, guarded by matching the logged-in shop customer's ABRA firm id.

Sources: `../context/team-1633516729428-engine-a6883ea926b4/wrshop/Module/Is/AbraApi/BaseControl.php`, symbols `import*`, `exportOrder()`, `exportCustomerFromOrder()`, and `getPdfInvoice()`; `Control/OrderControl.php`, symbols `export()`, `checkExport()`, `getInvoice()`, `afterPageOrder()`, and `getIssuedInvoicesIds()`.

External entity names in manager contracts include `StoreCards`, `StoreSubCards`, `StorePrices`, `StoreMenuItems`, `StoreCardMenuItemLinks`, `Eshop_Parameter`, `Eshop_Value`, `Eshop_StoreCard_Parameter`, `Firms`, `receivedorders`, and `issuedinvoices`. Sources: shared-engine `wrshop/Module/Is/AbraApi/Manager/*.php`.

Local tables visible at the boundary include `product`, `price`, `product_store_quantity`, `customer`, `address`, `order_head`, `invoice_order`, and `identifiers`. Sources: the Akros product model and scripts under `project/is/client/`, plus shared-engine `AbraApi/Control/OrderControl.php`.

Order export attempts to avoid duplicates by looking up ABRA orders by local external number, exports no more than ten local candidates per run, creates or matches the ABRA customer/office, and records the external id or error back on `order_head`. ABRA order import maps external status values into local canceled, dispatched, or delivery-wait states without sending customer email in that import path. Invoice synchronization searches recent externally identified orders without a linked invoice, imports invoice metadata, and stores or serves a generated PDF. Source: shared-engine `AbraApi/Control/OrderControl.php`.

### Conflicting generations

**Historical / conflicting:**

- `project/is/client/` contains legacy SOAP, FTP, XML, file-batch, and direct SQL scripts for catalog, categories, products, stock, prices, customers, firms, orders, invoices, and identifier assignment.
- `project/is/client/Abra/Base.php` uses `AbraG3` identifiers, and `project/custom/fetch_office_by.php` calls an `AbraG3` IS-service route.
- `project/custom/recreate.php` calls the newer `AbraApi` manual order-export route.
- `project/isadapter.php` loads both the generic legacy adapters and the older `Abra` classes.
- `project/config.php` defines `SEND_ORDER_TO_IS` as disabled, while shared-engine `AbraApi` declares scheduled order export and manual re-export.

The archive alone cannot establish which jobs were running, which adapter was authoritative per entity, or whether some older scripts were retained only for emergency/manual use.

### Other third parties

**Supported / feature-gated:**

- Shipping: PPL pickup widget, Packeta/Zásilkovna, TopTrans, Geis, and a generic branch-service mechanism.
- Payments: generic payment broker, PayU template, and a Comgate-specific Akros flag.
- Company/tax validation: ARES and VIES.
- Marketing and comparison: Google Analytics/Tag Manager/e-commerce/conversion/remarketing, Facebook/Meta, Heureka, Zboží.cz, Sklik, and related consent controls.
- Structured data: Product, Offer, Review, Rating, BreadcrumbList, and ItemList markup.
- Search/SEO: canonical-query cleanup, rewritten URLs, breadcrumbs, metadata, product availability and price-validity markup.

Primary sources: Akros `project/templates/content/basket/`, `project/templates/utilities/`, `project/templates/header.tpl`, `header.custom.tpl`, `footer.tpl`, `project/templates/components/`, and `project/config.php`.

**Unknown:** actual active providers, feed endpoints, merchant accounts, measurement ids, consent configuration, current SEO index/redirect inventory, and whether the old Heureka API still receives traffic.

## Migration evidence and constraints

### Customer and B2B data are material and dirty

The archive includes several production-derived migration artifacts:

- `project/customer.csv`: 28,398 rows of customer/external identifier mappings.
- `project/emaily.txt`: 21,634 rows connecting historical/external identifiers and email identity.
- `project/report.txt`: 7,306 rows of email comparison or cleanup evidence, including whitespace, invalid placeholder values, and encoding damage.
- `project/test.log`: 23,789 generated customer price-type updates.

These counts establish scale and data-quality risk. The files contain PII and must be handled as controlled migration inputs, never copied into specifications, tickets, fixtures, or logs.

Identity cannot safely be reduced to email. The ABRA integration also reasons about firm, firm office, invoice/delivery address, external id, and contact, while the old scripts include explicit reconciliation/assignment steps. Sources: Akros `project/is/client/Abra/Customer.php`, shared-engine `AbraApi/Control/CustomerControl.php` and `Manager/CustomerManager.php`, and the migration artifacts named above.

### Historical continuity is broader than catalog

A production-equivalent migration may need to preserve or deliberately archive:

- Product/category/content/media and rewritten URLs.
- ABRA identifiers and mappings, product groups/variants, parameters, stores, stock, price types, customer discounts, and minimum/package quantities.
- Customers, firms/offices, contacts, invoice and delivery addresses, consents, parent/subordinate relationships, store/group assignments, and purchase limits.
- Active baskets or approval flows, orders, order status history, invoices and PDFs, shipment references, claims and attachments, favorites and watchdogs.
- CMS content, banners, email templates, legal text, redirect/canonical rules, and third-party settings.

Which of these are launch-critical versus read-only archive or deliberately dropped is not answered by the source tree.

### URL and SEO continuity

Akros templates emit rewritten product/content links, canonical query cleanup, breadcrumbs, and structured commerce data. Search/category pages produce long-tail parameter titles and descriptions; sold-out/visibility behavior in the shared engine was designed to preserve product detail URLs. A cutover that changes URL identity or indexability without a redirect/content plan risks losing an existing acquisition channel. Sources: Akros `project/templates/header.custom.tpl`, `project/templates/content/content.list.tpl`, `project/templates/content/content.detail.tpl`, `project/templates/components/menu.breadcrumbs.tpl`, and shared-engine `wrshop/inc/dict/*/cms.php` documentation for product visibility/sold-out behavior.

### Production engineering gaps

- No Akros behavioral or end-to-end test suite is present. Files named `Test.php` under `project/is/client/Abra/` are operational/debug scripts rather than an application acceptance suite.
- Secrets are embedded in source and require rotation and managed secret storage during any replacement.
- PII-bearing migration artifacts require controlled access, minimization, retention, and deletion rules.
- The integration uses bounded recent-time windows for some status and invoice discovery; a replacement needs explicit backfill, retry, idempotency, reconciliation, and failure visibility rather than copying those windows blindly.
- The archive includes a default hosting `public_html/index.html` alongside the PHP entry point and depends on common Nginx includes not present here, further showing that the archive is insufficient to reproduce exact routing from source alone.

## Runtime and database facts that remain unavailable

The following cannot be resolved from the archive and should be obtained through a read-only production audit or authoritative stakeholder confirmation:

1. Exact live domains, language/currency/country configuration, B2C versus B2B access rules, and traffic distribution.
2. Enabled CMS modules, feature flags, license/version settings, and the actual account navigation for anonymous, retail, trade, subordinate, and administrator actors.
3. Product, category, media, content, customer, order, invoice, claim, consent, basket, identifier, store, stock, and price-list volumes and freshness.
4. Actual checkout rules and active shipping/payment providers, credentials, webhooks, callbacks, reconciliation, refunds, and failure handling.
5. Actual ABRA adapter/job schedule per entity, active API generation, ownership of every synchronized field, latency, backlog, retry behavior, error rate, manual recovery, and reconciliation process.
6. Customer identity rules and the true number of duplicates, orphan mappings, malformed contacts, inactive accounts, subordinate relationships, and conflicting firm offices.
7. Current order status mapping, cancellation/return/refund process, invoice and credit-note lifecycle, fulfillment ownership, shipment tracking, and claim workflow.
8. CMS editor roles, operational handoffs, manual overrides, and which fields staff intentionally edit in the shop after ABRA import.
9. Search index and SEO baseline: indexed URLs, redirects, canonical behavior, feeds, structured-data validity, analytics, conversion tracking, and organic landing pages.
10. Infrastructure topology, databases, object/media storage, caches/search, email, cron/worker orchestration, monitoring, alerting, backups, restore drills, release pipeline, availability, performance, and security posture.

## Decision implications for the Akros destination

This research supports the following decision sequence:

1. **Audit the live Akros feature and data baseline.** Resolve which documented/supported behaviors are actually used and establish volumes and operational dependencies.
2. **Define “complete Akros.”** Decide whether launch includes B2C and B2B, and classify every legacy capability as preserve, improve, archive, or drop.
3. **Set systems of record and domain boundaries.** Decide field and lifecycle ownership between ABRA, shared core/Ontos, e-commerce domains, Akros customization, and the administration/back office.
4. **Choose the surviving ABRA contract.** Specify entity contracts, identity mapping, status semantics, idempotency, retries, reconciliation, backfill, observability, and retirement of older integration generations.
5. **Lock Akros-specific commerce rules.** Decide product grouping/variants, configurations/components, packaging/minimum quantities, price and discount precedence, store access, account hierarchy, approval, checkout and legal rules.
6. **Choose migration and cutover strategy.** Define what moves, what remains read-only, URL/SEO continuity, coexistence and rollback, data cleansing, and acceptance evidence.
7. **Define production acceptance.** Cover security and privacy, secret management, payment and email reliability, observability, backups, performance, accessibility, test seams, and operational ownership.

Akros can force these decisions without requiring the whole future ERP to be designed. The useful shared seam is the business capability or record that both products genuinely need—customer/organization identity, catalog/item identity, pricing foundations, order/invoice references, administration primitives, or integration infrastructure—not wholesale reuse of the old CMS module inventory.
