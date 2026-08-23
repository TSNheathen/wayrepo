# Czech ERP families and the OntOS commerce boundary

Research for **[Decide the shared Ontos kernel contract](https://github.com/TSNheathen/wayrepo/issues/10)**. This note uses current official vendor documentation only and stays at business-roadmap depth; it does not select provider-specific schemas, endpoints, or Connector implementations. Sources were checked on 2026-08-23.

## Executive finding

HELIOS, ABRA, and POHODA can each perform some combination of accounting, finance, purchasing, warehouse/inventory, pricing, customer, order, and reporting work for an e-commerce operator. They do not establish one uniform “ERP topology.” The vendor families contain different products and editions, and their official e-commerce material shows materially different operating models: the external system may receive only periodic accounting documents, may own broad operational facts synchronized with the shop, or may sit directly underneath the shop.

OntOS therefore needs reusable **Business Modules**, not a single global “ERP mode” and not customer forks. For each customer and each business fact or lifecycle transition, the product must be able to declare one of these arrangements:

- the OntOS Business Module is the System of Record and supplies the complete back-office capability;
- an External Business System is the System of Record while the OntOS Business Module supplies the governed local projection, commerce-facing behavior, operational visibility, and Connector boundary; or
- authority is intentionally split by fact or transition—for example, OntOS owns the accepted commerce Order while an external accounting system owns the statutory invoice.

This is why one customer can manage products, translations, warehouses, prices, purchasing, and commerce operations in OntOS while another uses HELIOS, ABRA, POHODA, or another system for some of the same operational areas. The OntOS back office remains the coherent operator experience; using it does not imply that OntOS owns every fact shown there.

## Official product evidence

| Family | Roles the products can play for an e-commerce customer | Credible integration surfaces | Roadmap implication |
| --- | --- | --- | --- |
| **HELIOS** | HELIOS is a product family rather than one technical system. The official portfolio positions Easy, iNuvio, and Nephrite for different company scales. iNuvio covers economics/accounting, warehousing, trade, CRM, manufacturing, and transport; Nephrite similarly covers finance/economics, commercial warehouse, CRM, production, projects, workflow, and reporting ([HELIOS portfolio segmentation](https://www.helios.eu/heliosbi), [iNuvio capabilities](https://www.helios.eu/helios-inuvio), [Nephrite capabilities](https://www.helios.eu/helios-nephrite)). | Official material describes an iNuvio REST API/eServer used by a customer-built communication layer and says the API remains under active expansion; Nephrite material describes REST API support for B2B/B2C e-shop and third-party integrations ([iNuvio REST integration example](https://www.helios.eu/novinky/vyzkumny-ustav-vcelarsky-bez-moderniho-erp-systemu-uz-to-dal-neslo), [iNuvio 2026 development direction](https://www.helios.eu/novinky/bezpecnost-rychlost-a-mobilita-hlavni-cile-rozvoje-helios-inuvio), [Nephrite REST/e-shop integration](https://www.helios.eu/novinky/novinky-v-helios-nephrite)). An older official, explicitly product-specific HELIOS Orange connector also demonstrates bidirectional product, order, payment/status, B2B/B2C, and multi-shop exchange; it is evidence of possible arrangements, not a universal current HELIOS contract ([HELIOS Orange E-shop Connector](https://www.helios.eu/files/agend-list-e-shop-konektor.pdf)). | “HELIOS” alone is insufficient for planning. Product, version, licensed modules, implementation partner, exposed interface, and actual authority must be discovered for each customer. No shared HELIOS schema or topology should enter OntOS business contracts. |
| **ABRA** | ABRA also names materially different products. ABRA Gen is a broad ERP that may cover management, CRM, purchasing, warehouse, manufacturing, sales, finance/accounting, payroll, and reporting; ABRA Flexi covers accounting, finance, contacts, trade, warehouses, price lists, and e-shop exchange ([ABRA Gen roles](https://www.abra.eu/erp-system-abra-gen/), [ABRA Flexi roles and editions](https://www.abra.eu/flexi/)). | ABRA Gen exposes an installed HTTP REST API using JSON and OpenAPI-described business objects ([ABRA Gen Web API tutorial](https://help.abra.eu/cs/26.1/G3/Content/TutorialAPI/TutorialAPI_basic_info.htm), [ABRA Gen OpenAPI documentation](https://help.abra.eu/cs/26.0/G3/Content/Part53_API/OpenAPI.htm)). ABRA Flexi has a separate REST API with documentation for records, incremental updates, batch and transaction processing. Its official e-shop guide covers catalog/prices/stock, orders, realization, and invoicing ([ABRA Flexi REST documentation](https://podpora.flexibee.eu/en/collections/2592813-rest-api-documentation), [ABRA Flexi e-shop integration guide](https://podpora.flexibee.eu/cs/articles/3638593-jak-napojit-e-shop-na-abra-flexi-pres-rest-api)). Most importantly, the vendor explicitly presents three valid architectures: a periodic accounting-only import, broad near-real-time synchronization, or a shop built directly on Flexi ([ABRA Flexi integration patterns](https://podpora.flexibee.eu/en/articles/7036282-connecting-any-e-shop-to-abra-flexi)). | “ABRA” must never imply ABRA Gen, ABRA Flexi, the legacy Akros Connector generation, or global external authority. The official alternatives prove that ownership and cadence must be assigned per capability, even for customers using the same vendor family. |
| **POHODA** | POHODA is an economic/accounting system available in functionally different variants. Depending on the edition it can cover accounting or tax records, invoicing, orders, finance, warehouses, sales, payroll, assets, and other agendas ([POHODA product](https://www.stormware.cz/pohoda/), [POHODA variants](https://www.stormware.cz/pohoda/varianty/)). Its official e-commerce material supports offering goods from warehouse records, receiving orders, extending the customer directory, maintaining product descriptions/categories, and connecting multiple e-shops to one POHODA installation ([POHODA e-commerce](https://www.stormware.cz/pohoda/e-shopy-homebanking/)). | POHODA documents three distinct surfaces: a user-triggered “general internet shop” exchange, automatable XML file import/export mediated by an external application, and POHODA mServer, which processes authenticated HTTP POST requests carrying POHODA XML. The mServer is primarily intended for a local network/PC ([official connection options](https://www.stormware.cz/podpora/faq/pohoda/?id=1160), [mServer developer documentation](https://www.stormware.cz/pohoda/xml/mserver/provyvojare/), [POHODA XML API](https://www.stormware.cz/pohoda/xml/)). | OntOS cannot assume a remotely reachable JSON/REST service, immediate synchronization, or the same agenda coverage across POHODA customers. The downstream Symmy–POHODA Integration may need to operate near a customer installation or use bounded file exchange without weakening the OntOS-to-Symmy contract. |

## Consequences for OntOS Business Modules

### One capability model, variable authority

The evidence supports a modular product in which the same business capability can run under different authority arrangements per customer. The reusable module owns the OntOS meaning and guarantees. In the confirmed target, OntOS crosses one Symmy Connector boundary and provider-specific translation is a downstream Symmy–Provider Integration. This applies at least to:

- Product/Catalog facts, categories, variants, attributes, media metadata, and translations;
- pricing inputs, customer-specific terms, and accepted commercial snapshots;
- warehouse structure, physical stock, reservations, replenishment, and customer-facing Availability;
- Counterparties, contacts, addresses, and commercial account data;
- purchasing and supplier operations;
- accepted Orders and later processing outcomes;
- invoices, credit documents, payments, and statutory accounting outcomes; and
- operational reporting, reconciliation, and recovery.

The authority switch cannot be module-wide by default. A customer may use external accounting while OntOS owns Product and Inventory, or use an external warehouse and price list while OntOS owns commerce Orders and customer-facing content. Product translation is a particularly clear example: it can be managed natively in an OntOS Product/Catalog or Content capability even when stock and accounting remain external.

### Full OntOS back office does not mean universal OntOS authority

Operators should not have to leave OntOS merely because one capability is externally authoritative. The relevant Business Module still needs to expose:

- the local, permissioned representation required by storefront and back-office workflows;
- provenance and freshness of externally supplied facts;
- pending handoffs, errors, retries, backfills, and reconciliation outcomes;
- guarded corrections or requests that respect the declared System of Record; and
- a clear distinction between an OntOS decision and an externally reported outcome.

Conversely, customers without an external ERP need the same Business Modules to provide complete native management for the capabilities they adopt. That is a reason to keep storage/inventory, products, translations, purchasing, and commerce behavior in reusable OntOS Business Modules. It is not a reason to move those meanings into Core.

### Customer and deployment topology remain separate decisions

The vendor evidence does not decide whether OntOS runs one isolated deployment per customer, a multi-tenant deployment, or a mixture. It also does not decide whether storefronts and Module Federation frontends share a process or release unit with the back office. Those are deployment decisions constrained by isolation and reliability requirements, not by the brand of an External Business System.

The architecture should preserve both likely customer shapes:

1. **Externally backed customer:** OntOS supplies commerce and back-office modules while one or more external systems retain selected accounting, inventory, pricing, purchasing, or document authority.
2. **OntOS-managed customer:** the same modules provide the operational Systems of Record inside OntOS, with only the unavoidable or deliberately retained outside systems connected at their boundaries.

This is an inference from the documented vendor variability, not a claim that any named product is active for Akros or N1.

## Assumptions OntOS must reject

- A vendor family name identifies the installed product, edition, version, licensed modules, or interface.
- “ERP” means one system owns Product, price, stock, customer, Order, invoice, and payment facts together.
- An external system is automatically authoritative merely because it can import, export, or display a fact.
- Every integration is synchronous, near-real-time, cloud-hosted, directly reachable, or REST/JSON based.
- One customer has exactly one External Business System, one shop, one company, or one direction of synchronization.
- Provider record identifiers, status codes, category trees, price lists, or warehouse structures are canonical OntOS concepts.
- Using OntOS as the full back-office experience requires replacing a customer's statutory accounting system.
- A customer without an external ERP should receive a fork or a different product rather than a different composition and authority configuration of the same OntOS Business Modules.

## Decision-ready conclusion

Treat HELIOS, ABRA, POHODA, and similar products as replaceable **External Business Systems** reached through the Symmy Connector and named downstream integrations such as Symmy–POHODA Integration. Build reusable OntOS Business Modules for the business capabilities needed by Akros, N1, and later customers, and make authority explicit per fact and lifecycle transition. N1's current direct POHODA integration remains legacy evidence, not the target boundary. Keep Core limited to the non-business mechanisms those modules share. Leave physical tenant/customer deployment topology to a separate decision, because the ERP evidence neither requires nor rules out isolated or multi-tenant OntOS deployments.
