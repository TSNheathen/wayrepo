# Akros production product cutline

Decision asset for **[Define the production-complete Akros product cutline](https://github.com/TSNheathen/wayrepo/issues/6)**.

This ledger preserves all four dispositions. `Later` is a deliberate pickup list, not an implicit launch commitment. `Retired` records consciously rejected legacy breadth so it can be referenced or reconsidered without silently returning to scope.

## Product promise

- Production-complete means the replacement can safely take over every accepted Akros launch channel and required end-to-end business outcome; it does not mean WRShop feature parity.
- Launch both the Czech B2C and B2B channels in Czech and CZK. Preserve another current market only if production evidence establishes material use; market expansion is later.
- Preserve business outcomes and SEO continuity, with targeted improvement of risky or confusing journeys. A broad visual redesign and speculative platform breadth are later.
- B2C supports anonymous browsing, guest checkout, and optional authenticated access through a Retail Portal Principal.
- B2B protects Counterparty-specific assortment, prices, availability, and ordering behind an approved Principal. The Counterparty is the commercial customer; buyer, approver, and access-administrator authority is assigned per Counterparty.
- B2C and B2B share product identity and core content while allowing channel-specific visibility, merchandising, prices, quantity/package rules, availability, and purchasing rules.

## Classification rule

- **Launch**: active, revenue-critical, operationally necessary, legally required, or explicitly confirmed as part of the product promise.
- **Later**: useful but intentionally deferred because Production-complete Launch does not depend on it.
- **Archive/read-only**: retained for customer service, audit, accounting, or legal obligations without preserving the original write workflow.
- **Retire**: unused, obsolete, unsafe, or generic legacy breadth with no accepted Akros value.

## Launch

### Catalog, discovery, and content

- Category navigation, search, parameter filtering, sorting, product lists, and product detail.
- Media, documents, technical parameters, manufacturer, code/EAN, channel visibility, related/accessory/component relationships, and essential homepage/content merchandising.
- Every product shape required to sell a current Akros product correctly: base products, variants/groups, minimum and package quantities, units, technical parameters, components, and configuration/set or bounded-dimension choices where applicable.
- Editable legal/service pages, navigation, homepage/category merchandising, contact and delivery information, and transactional email.
- Canonical URLs, redirects, sitemap, robots policy, structured product data, and every currently consumed product/comparison feed.

### Commercial promise

- Display the applicable channel/company price, VAT treatment, quantity tier, fees, discount, availability, and delivery expectation.
- Revalidate commercial facts before submission and snapshot accepted price/tax facts into the durable order.
- Permit backorders only under an explicit product/customer rule.
- Ordinary promotional prices, negotiated B2B prices/discounts, quantity pricing, and any voucher or free-shipping rule confirmed as currently material.

### Buying journeys

- B2C: discover, configure/select, cart, guest or authenticated checkout, eligible shipping/payment, consent, and order confirmation.
- B2B: authenticate, see company terms, quick search or bulk entry, build a cart under quantity/package rules, add purchase-order/delivery instructions, submit directly or for approval, and receive confirmation.
- Both journeys preserve selected configuration identity and create a durable order snapshot.
- Printable offers and advanced purchasing controls only where confirmed as operationally required.

### Identity and aftercare

- Retail Portal Principal enrollment, sign-in, recovery, profile, invoice and delivery addresses, searchable order/invoice history, order and document detail, repeat ordering, claim submission/history, and consent history.
- Principal access for a Counterparty, Counterparty permission management, and subordinate-basket review/approval where enabled.
- Durable order status, shipment/tracking, invoice and credit documents, plus customer requests for lifecycle-permitted cancellation, correction, return, or refund. Operators perform irreversible financial or operational transitions.
- Claims against an order line, including product/serial identity, explanation, attachments, guarded operator processing, communication, status, and history.
- Repeat order creates a new cart from still-sellable items/configurations, reports changed or unavailable lines, and applies current commercial rules rather than recreating historical terms.

### Migration

- Migrate active Retail Customer identities and Principal bindings, approved Counterparty relationships/permissions, required addresses/consents, open orders, unresolved payments/shipments, active approval baskets, open claims, and external mappings required to continue them.
- Require password reset or secure Principal reactivation where legacy credentials cannot be migrated safely.

### Administration and operations

- Catalog and Content Operators manage locally owned categories, content, media, relations, merchandising, visibility, SEO metadata, navigation, and legal/service pages.
- Customer and Counterparty Operators approve B2B access, manage Counterparty relationships and purchasing controls, resolve access problems, and perform permitted customer-data corrections.
- Order and Aftercare Operators investigate orders, payments, shipments, and documents; perform permitted corrections/cancellations; process returns, refunds, and claims; and resend communications.
- Commerce Operations Administrators manage channel rules, provider-neutral payment/shipping availability, operational configuration, feed and Connector health, reconciliation, and guarded retries.
- Externally owned facts expose their source and freshness and cannot be silently overridden.
- Replace unrestricted customer impersonation with audited assisted support. Customer-context views and any cart/order action are permission-checked, explicit, attributed to the operator, and auditable.
- Every state-changing operator action is permissioned and audited. High-risk overrides, cancellations after handoff, refunds, credit documents, company-authority changes, and Connector replays require a reason and preview their downstream consequences. Dual approval applies only where an accepted policy requires it.
- Launch only named, typed, validated, owned, and audited Akros policies and provider-neutral configuration. Do not reproduce generic settings breadth or undocumented feature flags.

### External outcomes and resilience

- Appropriate catalog, assortment, price, tax, stock, and availability facts reach each channel; required customer/company facts and confirmed orders reach the responsible External Business Systems.
- Required order status, shipment/tracking, invoice, and credit-document outcomes return to Akros.
- Payments support authorization/result, reconciliation, cancellation/refund outcomes, and failure recovery. Shipping supports eligibility, price, pickup/address choice, fulfillment result, tracking, and exception recovery.
- Transactional email, active feeds, consent-controlled measurement, and legally required document access remain operational.
- A confirmed order and its accepted commercial snapshot remain durable when an External Business System is unavailable. Asynchronous handoffs are idempotent, visible, retryable, and reconcilable; synchronous dependencies fail clearly before confirmation.
- Operators can identify stale data, backlogs, failures, ownership, and safe recovery actions.

### Production acceptance

- Every Launch Capability has executable happy-path, business-rejection, external-failure, retry/recovery, permission, and audit scenarios that pass with representative synthetic data in a production-like environment.
- Launch-blocking journeys are B2C guest/authenticated purchase; B2B direct/approval purchase; complex configured/package-quantity purchase; payment success/failure/reconciliation/cancellation/refund; shipping/pickup/fulfillment/tracking/exception; exactly-once-in-business-terms order handoff; Principal access recovery/order/invoice/repeat-order/claim aftercare; catalog/content publication and channel visibility; guarded operator correction/retry/audit; and historical archive access.
- Initial service targets are at least 99.9% monthly storefront/checkout availability excluding announced maintenance, zero lost or business-duplicated confirmed orders, a 15-minute recovery point objective, a two-hour recovery time objective, tested backup restoration and External Business System reconciliation, and capacity at twice evidenced peak traffic/order throughput. Stricter evidenced or contractual commitments supersede these floors.
- Alerts cover checkout failures, stale commercial data, failed handoffs, payment/shipping failures, feed failures, and growing backlogs.
- All launch customer and staff-critical journeys meet WCAG 2.2 AA; key public page types achieve good Core Web Vitals at the 75th percentile; mobile checkout and supported modern browsers are verified.
- Security acceptance includes a threat model, authorization and privacy/consent tests, dependency and secret scanning, independent penetration testing, no unresolved critical/high findings, and no sensitive data in logs, analytics, research artifacts, or operational errors.
- Migration acceptance requires 100% reconciliation of open operational records and required mappings; count/financial-total reconciliation for retained history; sampled semantic verification of products, configurations, prices, taxes, customers, documents, and URLs; and no unexplained duplicate, orphaned, or unmapped launch-critical records.
- Every materially indexed or externally linked URL class has a target, redirect, or intentional retirement. Archive access and retention controls pass before legacy shutdown.
- Named owners sign off product, Akros operations, catalog/content, B2B sales, finance/tax, customer service/claims, privacy/legal, security, accessibility, SEO/acquisition, infrastructure/recovery, and every active External Business System.
- Go-live requires a rehearsed cutover, explicit rollback criteria, support rota, monitoring, runbooks, and a time-bounded hypercare period.

### Legal, privacy, tax, and accessibility

- B2C acceptance covers timely pre-contract disclosures, full price/charges, an unambiguous payment-obligation action, no preselected paid extras, durable confirmation, current terms/policies, and current Czech dispute-resolution information. B2B uses a separately approved contract policy.
- Consumer price reductions carry the required price history and promotion classification. Personalized pricing and customer reviews remain absent unless their conditional disclosures and verification controls are deliberately introduced.
- Covered consumer offers carry required manufacturer/responsible-person identity, product identification, warnings, and Czech-language safety information.
- Withdrawal/returns/refunds and complaints are complete workflows with required information/forms, durable acknowledgements, product-specific exceptions, records, timers, communications, alerts/escalation, and auditable extensions. Statutory defect rights remain distinct from commercial warranties.
- Privacy acceptance includes a purpose/legal-basis inventory, collection-time notices, data-subject request handling, processor/transfer inventory, breach ownership, and record-class retention/deletion.
- Nonessential cookies and tracking remain disabled before valid consent; accept, reject, and withdrawal are equally accessible. Required acknowledgements, marketing, comparison programmes, and cookie purposes retain separate evidence. Opt-out suppression applies across all marketing senders.
- Consumer e-commerce accessibility is a Launch Capability. WCAG 2.2 AA is the engineering floor, while a separate legal/executive review determines statutory applicability and sign-off. Checkout, authentication, payments, documents, forms, support, and influential third-party components are in scope.
- Restricted goods are prohibited by default and the generic legacy 18+ checkbox is retired. A named Product/Legal owner may admit a category only with category-specific warning, eligibility, verification, delivery, privacy, refusal, and audit rules.
- Finance owns document triggers/content/corrections, System of Record, reconciliation, and statutory preservation. The customer-facing archive is an authorized access view, not the statutory accounting/tax archive. Retention is record-class based with integrity, readability, legal hold, expiry, and evidenced deletion.
- Legal re-checks current law immediately before launch, including the recorded September 2026 Czech/EU watch items.

## Later

- Additional countries, languages, and currencies.
- Broad visual redesign and speculative platform capabilities.
- Personalized recommendations, recently viewed products, favorites, availability/price watchdogs, and ratings where not currently business-critical.
- Unused generic product configurability.
- Unconfirmed vouchers, gifts, bonus points, loyalty balances, elaborate discount thresholds, and personalized promotions.
- Newsletter, editorial/news content, and marketing automation unless confirmed active.
- Printable offers and advanced purchasing controls where not operationally required at launch.
- Authenticated retail favorites, watchdogs, loyalty presentation, and other unconfirmed engagement features.

## Archive/read-only

- Historical orders, invoices/credit documents, and claims for the legally and operationally required period.
- Historical records remain available for customer service, audit, accounting, and legal use without recreating obsolete write workflows.
- Exact retention periods require an accountable legal/data owner and cannot be inferred from WRShop.

## Retire

- Product comparison unless current use is explicitly confirmed.
- Public product discussions/forums, surveys, and send-to-friend unless current use is explicitly confirmed.
- Generic sale-of-the-day and promotion mechanisms with no Akros owner or demonstrated use.
- Obsolete tracking tags, abandoned feeds, unsafe Heureka endpoints, and duplicate legacy content mechanisms.
- Legacy product configurability that no current Akros product requires.
- Anonymous carts, abandoned ordinary carts, favorites, watchdogs, and obsolete engagement data unless runtime evidence proves material business value.
- Unrestricted customer impersonation.
- Arbitrary key/value settings, duplicate configuration paths, and behavior controlled by undocumented flags.
- The generic legacy 18+ checkbox unless an accepted restricted-goods category receives category-specific controls.
- The obsolete EU Online Dispute Resolution platform link.

## Decisions still open

No product-cutline decisions remain open. Provider selection, Connector contracts, field-level Systems of Record, detailed migration mechanics, and implementation architecture belong to later Wayfinder tickets.
