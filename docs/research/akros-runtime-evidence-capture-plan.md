# Akros runtime evidence capture plan

Research for the Wayfinder decision ticket **Capture Akros runtime evidence missing from the archive**.

All source paths are relative to the `wayrepo` root. Evidence is limited to the adjacent archive under `../context`, the existing [Akros production baseline](./akros-production-baseline.md), and a safe read-only check of Akros' public HTTP surface on 2026-08-22 at approximately 15:00 UTC. No authenticated production, database, observability, provider-console, analytics, or operator-session access was available for this research.

## Scope boundary

This plan supports **rebuilding Akros on the new architecture**. The old WRShop deployment is evidence for product behavior, domain and integration contracts, migration shape, operator acceptance, and URL/SEO continuity. Repairing, refactoring, securing, or reproducing the old PHP application and its infrastructure is outside this ticket.

The archive distinguishes three evidence classes:

1. **Safe structural evidence:** static host and entry-point configuration, rewrite shapes, templates, source-level entity/flow declarations, and aggregate file metadata. For example, the archive establishes two named Akros hosts on one document root, distinct storefront/CMS/AJAX entry points, and sitemap, robots, feed, rewritten HTML, and catch-all routes (`../context/team-1633516729428-akros-124b361ce570/conf.d/nginx.conf:1-23`; `../context/team-1633516729428-akros-124b361ce570/public_html/index.php:1-3`; `../context/team-1633516729428-akros-124b361ce570/public_html/cms/index.php:1-4`; `../context/team-1633516729428-akros-124b361ce570/public_html/cms/ajaxconnector.php:1-6`; `../context/team-1633516729428-akros-124b361ce570/public_html/.htaccess:74-92`).
2. **Sensitive evidence that may be counted but not quoted:** the deployment configuration embeds credentials, while customer mapping and reconciliation artifacts contain PII. The existing baseline records only safe aggregate counts—28,398 customer mappings, 21,634 historical/email mappings, 7,306 cleanup rows, and 23,789 generated price-type updates—and explicitly prohibits copying their rows into planning artifacts (`docs/research/akros-production-baseline.md:133-140,193-204`).
3. **Capability evidence, not enablement evidence:** source and templates show what the deployment can do, but live database settings, licenses, data, schedules, and telemetry determine what it actually does. The existing baseline makes that distinction explicit (`docs/research/akros-production-baseline.md:7-15,19-25`).

### Evidence inventory

| Already safely available | Still absent |
| --- | --- |
| Static host, entry-point, route, and feature/contract shapes from the archive | Authenticated domain/channel/module/role configuration and feature/license export |
| Source-declared ABRA entity directions, windows, batches, and manual recovery seam | Active ABRA generation, scheduler definitions, run outcomes, backlog, ownership, and reconciliation evidence |
| Aggregate counts—but no rows—from four PII-bearing migration artifacts | Current sanitized aggregate volumes, relationship shapes, history ranges, and data-quality distributions |
| Dated public B2C/B2B HTTP availability, robots route families, and a small sitemap snapshot | Traffic/adoption, active checkout/payment/shipping rules, operator workflows, full redirect/index/analytics baseline, and external-interface activity |

The absent inventory is not recoverable by deeper source reading: the relevant state lives in the database, scheduler, telemetry, provider systems, analytics/search tooling, and staff practice (`docs/research/akros-production-baseline.md:230-243`).

## Finding

The archive and public surface are sufficient to seed a production evidence request, but insufficient to declare the replacement acceptance boundary. The archive contains no trustworthy live database or runtime-configuration export (`docs/research/akros-production-baseline.md:5,23-25`). Its own ignore rules exclude runtime-bearing directories such as logs, local configuration, data, migration state, caches, transport, backups, certificates, and sessions (`../context/team-1633516729428-akros-124b361ce570/.gitignore:7-31`).

The missing evidence is not merely infrastructure detail. It includes the live domain/channel/module matrix, actual payment and shipping rules, authoritative ABRA paths and schedules, aggregate data volumes and shapes, operator workflows, URL/SEO inventory, and active externally consumed interfaces. These facts determine what the new architecture must preserve, change, archive, or retire. The full gap is corroborated by the existing baseline (`docs/research/akros-production-baseline.md:230-243`).

The ABRA source is a particularly useful seed, but cannot substitute for a runtime capture. It declares imports for products, stock, category membership, prices, parameters, categories, recent orders, customers, and invoices; order export; and customer export as part of order handling (`../context/team-1633516729428-engine-a6883ea926b4/wrshop/Module/Is/AbraApi/BaseControl.php:27-54,99-220,228-232`). It also contains numeric cadence-like declarations, a one-day recent-order window, explicit product/customer page sizes, a ten-order export batch, duplicate lookup, a 31-day invoice-search window, and manual re-export. Those are implementation clues, not proof of the active scheduler or production outcomes (`../context/team-1633516729428-engine-a6883ea926b4/wrshop/Module/Is/AbraApi/BaseControl.php:27-54,115-120,179-196`; `../context/team-1633516729428-engine-a6883ea926b4/wrshop/Module/Is/AbraApi/Control/OrderControl.php:65-97,168-186,280-290`; `../context/team-1633516729428-akros-124b361ce570/project/custom/recreate.php:1-5`). Coexisting `AbraApi`, `AbraG3`, and legacy adapters make active-path confirmation mandatory (`docs/research/akros-production-baseline.md:164-174`).

## Safe public-runtime evidence

A read-only public HTTP check on 2026-08-22 at approximately 15:00 UTC adds a narrow layer of current evidence:

- [The public Akros storefront](https://www.akros.cz/) and [the Akros B2B storefront](https://b2b.akros.cz/) both returned HTTP 200 through Nginx/PHP-backed sessions. They presented distinct page titles and each identified `/homepage` as its `og:url`. This confirms that separate B2C- and B2B-facing surfaces are publicly reachable; it does **not** establish traffic, customer adoption, enabled checkout rules, data ownership, or backend separation.
- [The public robots policy](https://www.akros.cz/robots.txt) pointed crawlers to [the live sitemap](https://www.akros.cz/sitemap) and exposed route families for checkout, authentication, claims, partner registration, subordinate-customer/order overview, quick selection, wishlist, comparison, and basket import. Robots entries prove route awareness, not that every feature is enabled, usable, or in replacement scope.
- The observed [live sitemap](https://www.akros.cz/sitemap) exposed a sitemap/index snapshot with 21 product-page entries plus text-page entries. That is safe evidence of public URL shapes and currently advertised crawl targets, not a catalog-size or indexed-page count.

No session cookie, tracking identifier, query string, response header carrying identifiers, or customer-specific page content was retained. This public snapshot narrows the domain/URL capture but does not resolve any authenticated runtime gaps listed below.

## Production-safe capture protocol

The capture should be executed by an authorized production operator or data owner. It is deliberately read-only and produces a sanitized evidence bundle, not a production clone.

### Preconditions and provenance

- [ ] Name a business owner, Akros operator, database owner, ABRA owner, payment owner, shipping owner, and SEO/analytics owner for the capture.
- [ ] Record capture timestamp in UTC, environment, source system, source version/revision when knowable, query or report version, timezone, aggregation window, and operator for every output.
- [ ] Prefer a read-only replica or approved point-in-time export. If production must be queried, use a dedicated read-only account, bounded statements, timeouts, and off-peak execution.
- [ ] Record table/report names and field names, but never connection strings, credentials, tokens, secret names/paths, private keys, cookies, session identifiers, allow-list addresses, or raw request headers.
- [ ] Use schema-only samples: field name, type, nullable flag, semantic description, enum/range, source of truth, and representative **synthetic** value. Do not include real rows or payloads.
- [ ] Aggregate by stable non-personal dimensions. Suppress cells below 5, round dates to month where exact time is unnecessary, bucket amounts and ages, and omit intersections that could identify a person or company.
- [ ] Do not pseudonymize row-level subjects for this bundle; hashes remain linkable. Export counts and distributions instead.
- [ ] Store the bundle in the project's controlled evidence location with named access, encryption, retention date, and deletion owner.

### Runtime safety

- [ ] Perform no `INSERT`, `UPDATE`, `DELETE`, DDL, reindex, cache flush, queue manipulation, job trigger, retry, replay, re-export, payment action, refund, shipment action, email, webhook, callback, or invoice-PDF fetch.
- [ ] Do not invoke the manual ABRA order route, old integration/debug endpoints, payment returns, carrier callbacks, or marketplace APIs. Their existence is contract evidence only; the archived manual route can initiate work (`../context/team-1633516729428-akros-124b361ce570/project/custom/recreate.php:1-5`).
- [ ] Do not take full database, filesystem, log, network-trace, or provider payload dumps for this ticket.
- [ ] Derive telemetry aggregates in place. Export only counts, percentages, duration buckets, status/error categories, oldest-backlog age, and redacted route templates.
- [ ] For screenshots or walkthroughs, use a prepared synthetic account/order where possible. Otherwise redact names, emails, phones, addresses, company numbers, order/invoice numbers, notes, attachments, and identifiers before the image leaves the controlled session.
- [ ] Never capture PAN, CVV, payment tokens, gateway transaction identifiers, bank details, ABRA credentials, invoice PDFs, claim attachments, free-text order/claim notes, search terms, or raw URLs with query strings.

## Capture and output matrix

Each section below is required. “Output” means a section or table inside the one controlled evidence bundle; it does not authorize the full underlying export.

### 1. Enabled domains, channels, and modules

The archive proves configured host candidates, including B2C/B2B-shaped hosts, but not the live access rules or enabled feature set (`../context/team-1633516729428-akros-124b361ce570/conf.d/nginx.conf:1-8`; `docs/research/akros-production-baseline.md:103-119,230-236`).

- [ ] List each live public/admin hostname and channel purpose: B2C, B2B, CMS, service/API, feed, or redirect-only.
- [ ] For each storefront channel, record locale, currency, sell-to countries, tax display, anonymous/authenticated access, registration policy, customer segment eligibility, price-list selection, store/warehouse visibility, and checkout availability.
- [ ] Export enabled/disabled state for licensed modules, feature flags, account navigation, CMS modules, third-party modules, and domain-scoped settings. Export semantic values only when non-sensitive; otherwise record `configured: yes/no`, owner, and purpose.
- [ ] Record role-to-capability matrices for anonymous, retail customer, company buyer, subordinate buyer, approver, customer-service operator, catalog/content editor, order operator, and administrator.
- [ ] Compare active modules with the documented CMS/product surface; do not infer enablement from the shared engine's 77-module inventory (`docs/research/akros-production-baseline.md:121-129`).

**Output:** `domain-channel-module matrix` with one row per domain/channel/module and provenance per row.

**Acceptance:** every live host and actor lands on an explicit replacement channel; every discovered module is classified `preserve`, `improve`, `archive/read-only`, or `retire`, with an owner and evidence source.

### 2. Payments and shipping

Templates support PPL pickup, Packeta/Zásilkovna, TopTrans, Geis, PayU, and a Comgate-specific setting, but code presence does not show which providers or rules are active (`docs/research/akros-production-baseline.md:69-84,176-189`).

- [ ] List active payment and shipping methods by domain, country, currency, customer segment, basket/order type, and fulfillment mode.
- [ ] Record provider family, customer-visible label, eligibility predicate, price/discount calculation, free-shipping/minimum-order threshold, branch/pickup requirement, requested-date behavior, and fallback/manual method.
- [ ] Record callback/webhook/return route **templates** with query strings removed; event types; owning system; auth category; idempotency key semantics; timeout/retry policy; and reconciliation owner/schedule.
- [ ] Aggregate the last 12 complete months by method and terminal state: attempts/orders, success, failure category, cancellation, refund/credit, shipment created, delivered, returned, and unresolved. Suppress low-volume cells.
- [ ] Record operator exception workflows: retry/reconcile payment, change method before capture, refund/credit, change carrier, correct pickup point, recreate shipment, and handle provider outage. Observe only; trigger nothing.

**Output:** `payment-shipping rules and outcomes matrix`, plus schema-only callback contracts.

**Acceptance:** each method has deterministic replacement eligibility/pricing/status rules, callback ownership, reconciliation behavior, migration disposition, and aggregate evidence of actual use. No payment or shipment identifier or payload is exported.

### 3. ABRA synchronization paths and schedules

Source-level flows include catalog, stock, category membership, prices, parameters, customers, orders/statuses, invoices, customer/order export, and authorized invoice access (`docs/research/akros-production-baseline.md:148-162`). Customer matching reasons about firm, office, address, and contact rather than email alone (`../context/team-1633516729428-engine-a6883ea926b4/wrshop/Module/Is/AbraApi/Control/CustomerControl.php:31-113`).

- [ ] Identify the **active implementation generation and entry point per entity**: `AbraApi`, `AbraG3`, another legacy path, manual-only, or inactive. Do not record endpoint credentials.
- [ ] For each entity/operation, record direction, trigger type, schedule and timezone, incremental watermark/window, filters, source and destination schema names, field ownership, transformation/business rules, batch/page limit, dependency ordering, and expected latency.
- [ ] Capture enabled scheduler/orchestrator definitions and last-change metadata as sanitized configuration—not executable commands. Reconcile these with the source's cadence-like numbers rather than treating those numbers as schedules.
- [ ] Aggregate 30- and 90-day runs by operation: attempted, succeeded, partially succeeded, failed by category, duration buckets, records read/written/skipped, retry count, oldest backlog age, and last successful completion.
- [ ] Count identifier mappings by service/entity and report one-to-zero, one-to-one, and collision/orphan distributions; export no identifiers.
- [ ] Record order-export duplicate detection, customer/office matching, status mapping, invoice-link/PDF behavior, retry/replay, backfill, reconciliation, manual recovery, and alert ownership. The archived path limits automatic candidates to ten and writes external/error state locally; current production behavior must be observed rather than assumed (`../context/team-1633516729428-engine-a6883ea926b4/wrshop/Module/Is/AbraApi/Control/OrderControl.php:19-62,65-97,139-186,245-290`).
- [ ] Produce schema-only contracts with synthetic examples for every retained entity and callback. Do not export customer, address, order, invoice, or ABRA payload samples.

**Output:** `ABRA entity-contract matrix`, `schedule/run aggregates`, `mapping-health aggregates`, and `operator recovery walkthroughs`.

**Acceptance:** every required entity has one active path, explicit source-of-truth/field ownership, schedule, freshness target, idempotency and retry semantics, backfill/reconciliation method, migration disposition, and evidence owner. Conflicting generations are resolved as a replacement decision, not repaired in place.

### 4. Representative aggregate data volumes and shapes

The source tree cannot supply current volumes, and the PII-bearing files establish only customer-migration scale and quality risk (`docs/research/akros-production-baseline.md:191-216,230-240`). Capture current counts and schema characteristics without exporting rows.

- [ ] **Catalog/content:** base products, variants/groups, components/configurations, visible/hidden/discontinued items, categories and depth, parameters/values, product-category/parameter/relation cardinalities, manufacturers/suppliers, content/system pages, banners, news, attachments, and media counts/bytes by type.
- [ ] **Pricing/inventory:** price types/lists, currencies, VAT rates, quantity tiers, discounts, supplier packs/minimum quantities, stores/warehouses, stock-state buckets, zero/negative stock, and freshness buckets.
- [ ] **Customer/B2B:** retail customers, firms, offices, contacts, invoice/delivery addresses, active/inactive accounts, parent/subordinate relationships, approval users/baskets, store/group assignments, purchase limits, price-type assignments, consents, and duplicate/orphan/conflict counts by rule.
- [ ] **Commerce/history:** active/abandoned/approval baskets, orders by month/channel/status, order rows per order buckets, payments, shipments, invoices/credit notes, claims/status history, favorites, watchdogs, and retained history date ranges.
- [ ] **Integration/migration:** external mappings by system/entity, unmapped records, collisions, stale-watermark buckets, queue/backlog counts, and oldest age.
- [ ] For each aggregate/domain object, capture schema-only shape: field names/types/nullability, enum values and counts, relationship cardinality buckets, null percentages, earliest/latest month, and authoritative owner.
- [ ] Suppress cells below 5; use month/time and amount/count buckets; do not export free text, identifiers, row-level hashes, or rare cross-dimension combinations.

**Output:** `aggregate inventory and schema catalog`, with source timestamp and query/report version for every measure.

**Acceptance:** architects can size migration and retention, identify high-cardinality and dirty relationships, and generate synthetic representative fixtures without any real subject or transaction row.

### 5. Operator workflows

The old CMS surface indicates possible operational areas, while the actual roles, handoffs, overrides, and post-ABRA edits are absent (`docs/research/akros-production-baseline.md:121-129,230-243`).

- [ ] Conduct read-only, role-authentic walkthroughs for catalog/content publication, domain merchandising, product visibility, media/attachment handling, and SEO metadata.
- [ ] Walk through price/discount/stock investigation and document which fields can be overridden locally versus owned by ABRA.
- [ ] Walk through company/customer reconciliation, firm/office/contact correction, parent/subordinate account administration, purchase limits, basket approval/return, and impersonation controls.
- [ ] Walk through order search/detail, status intervention, ABRA export/re-export decision, invoice retrieval, shipment/tracking exception, cancellation/refund/credit handling, and claim processing.
- [ ] Walk through payment/carrier reconciliation, failed ABRA run/backlog recovery, feed/marketplace failure, and content/URL correction.
- [ ] For every workflow record: actor/role, trigger, preconditions, ordered steps, decisions, systems touched, data read/written conceptually, handoff, exception/rollback, evidence viewed, frequency bucket, and required replacement outcome.
- [ ] Use synthetic or fully redacted screenshots. Do not click actions that mutate state or contact an external system.

**Output:** `operator workflow catalog` and role matrix, linked to replacement acceptance scenarios.

**Acceptance:** each recurring or business-critical workflow has an owner, happy path, exception path, system-of-record boundary, frequency/criticality, and `preserve/improve/archive/retire` decision.

### 6. Live URL and SEO continuity

The deployment supports sitemap/robots/feed routes and rewritten product/content URLs (`../context/team-1633516729428-akros-124b361ce570/public_html/.htaccess:74-92`). Templates emit canonical cleanup, breadcrumbs, long-tail category metadata, and structured commerce data; continuity risk therefore extends beyond a simple route list (`docs/research/akros-production-baseline.md:176-189,218-220`).

- [ ] Inventory all live host variants and their redirect target, protocol, status code, canonical host, locale/channel, and robots policy.
- [ ] Export sitemap indexes and URL counts by public route/template type. Public path exports may be retained only in the controlled SEO evidence area; remove query strings, fragments, session data, and internal preview/admin URLs.
- [ ] For a stratified public sample and every high-value route template, record response status, redirect chain, canonical, robots, hreflang, title/description presence, structured-data types/validation status, availability/indexability, and rendered-content requirement.
- [ ] Export redirect and rewritten-URL rules, including product/category/content identity and sold-out/hidden behavior, as source/target **path templates** plus aggregate counts.
- [ ] Obtain aggregate Search Console/analytics evidence by host and page template for at least 12 complete months: indexed/excluded counts, clicks/impressions/landing sessions/conversions in buckets, not raw user/search-query data. Keep public top landing URLs only if approved.
- [ ] Inventory active product/marketplace feeds by provider, route template, item count, freshness, validation state, and consumer owner.

**Output:** `host/URL/redirect matrix`, `SEO aggregate baseline`, `structured-data matrix`, and `feed inventory`.

**Acceptance:** every materially indexed or externally linked public URL class has a target route, redirect/canonical policy, metadata/schema acceptance case, and pre/post-cutover comparison measure.

### 7. Externally consumed interfaces

Candidate interfaces include ABRA, payment and carrier integrations, ARES/VIES, marketplace/comparison feeds and APIs, analytics/marketing tags, and invoice retrieval. Presence in source is only a discovery list; actual activity must be proven by sanitized configuration plus aggregate call evidence (`docs/research/akros-production-baseline.md:146-189`).

- [ ] Inventory each inbound/outbound interface with producer, consumer, business purpose, owner, direction, protocol, route/topic/file **template**, auth category, trigger/schedule, payload schema version, source of truth, and data classification.
- [ ] Confirm active/inactive state using deployment configuration and aggregate calls, not source presence. Record last-seen month, 30/90-day call count, status/error categories, latency buckets, retry/idempotency/reconciliation, and support contact role.
- [ ] Include public storefront/service APIs, ABRA, payment returns/webhooks, carrier/pickup APIs, ARES/VIES, Heureka/Zboží and other feeds/marketplaces, analytics/conversion/consent tags, email delivery, invoice access, and any B2B machine consumer discovered at runtime.
- [ ] Record callback and writer ownership during coexistence/cutover, compatibility requirement, consumer migration method, version/deprecation constraint, and rollback behavior.
- [ ] Export schemas only, with synthetic examples. Remove credentials, tokens, signatures, IPs, query strings, partner/customer identifiers, payloads, and free text.

**Output:** `external-interface registry` and schema-only contract appendix.

**Acceptance:** every interface with observed traffic has an accountable owner, replacement contract, data classification, operational semantics, consumer transition plan, and cutover/rollback acceptance test.

## Bundle-level acceptance criteria

The ticket's evidence capture is complete only when:

- [ ] Every requested area above has a provenance-complete output or an explicit `unavailable`, named owner, reason, and follow-up date.
- [ ] Structural capability is never presented as live enablement; every live claim has a timestamped runtime source or operator confirmation.
- [ ] Counts reconcile across important boundaries: enabled domains/channels, orders versus payment/shipping outcomes, shop versus ABRA identifiers/statuses/invoices, and sitemap versus indexed/redirected URL classes. Differences are explained, not silently normalized.
- [ ] Low counts are suppressed and the bundle contains no secrets, credentials, PII, PCI data, row-level hashes, raw payloads, raw logs, full dumps, private files, or mutation instructions.
- [ ] Every retained behavior/interface/workflow maps to a replacement requirement and acceptance scenario; everything else is explicitly `archive/read-only` or `retire`.
- [ ] A second authorized reviewer signs off the privacy/secret scan and the relevant business/system owners sign off their matrices.

## Decision value

This capture turns the archived capability inventory into an evidence-backed replacement boundary. It will let the Wayfinder map decide the live B2C/B2B scope, systems and fields of record, surviving ABRA contracts, payment/shipping semantics, migration/retention sizing, operator acceptance surface, URL/SEO continuity, and externally coordinated cutover without expanding into legacy-platform remediation. Those are the decision gaps already identified by the baseline (`docs/research/akros-production-baseline.md:245-257`).
