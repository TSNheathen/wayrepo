# Akros Rebuild

This context defines the language used while planning Akros on a new architecture. The current deployment supplies evidence; it does not dictate the replacement's implementation or integration topology.

This glossary extends the canonical OntOS vocabulary in `../context/ontos/CONTEXT.md`. OntOS terms such as Party, Counterparty, Principal, Legal Entity, and Evidence Artifact keep their OntOS meanings here; this file defines only Akros-specific refinements. If the two glossaries conflict, OntOS governs.

## Language

**External Business System**:
A live upstream or downstream system that exchanges business facts with Akros, such as an ERP, accounting system, WMS, PIM, CRM, or bespoke service. Its product and system type must be observed rather than assumed.
_Avoid_: ERP, when the actual system or role has not been verified

**System of Record**:
The system authorized to decide a specific business fact or lifecycle transition. Authority is assigned per fact or transition and need not belong to one system globally.
_Avoid_: Master system, source system

**Connector**:
A replaceable adapter that implements an explicit contract between Akros and an External Business System. A legacy connector is evidence of a possible contract, not proof that the connected system is active or should survive.
_Avoid_: Integration, when referring to the adapter implementation itself

**Production Deployment Snapshot**:
The Akros package captured approximately five days before this Wayfinder session and confirmed by the operator as live and in use. It proves which code, routes, customizations, and connector seams were deployed at capture time, but not their database-controlled enablement, traffic, schedules, or operator use.
_Avoid_: Historical archive, when referring specifically to the current Akros package

**Deployed Capability**:
Behavior or a connector seam present in the Production Deployment Snapshot whose activation or use has not yet been established from runtime state.
_Avoid_: Active feature, requirement

**Active Behavior**:
Behavior established by current public observation, runtime configuration or telemetry, or explicit operator confirmation. Active Behavior is evidence for the replacement cutline, not an automatic decision to preserve it unchanged.
_Avoid_: Supported feature

**ABRA**:
A named External Business System family represented by several connector generations in the Production Deployment Snapshot. Its actual production role and activity remain facts to verify; it is neither the assumed current ERP nor a predetermined target dependency.

**Production-complete Launch**:
The release at which the replacement can safely take over every accepted Akros launch channel and its required end-to-end business outcomes. It does not imply parity with every capability deployed or historically available in WRShop.
_Avoid_: Feature parity, complete platform

**Launch Capability**:
A capability required at Production-complete Launch because it is active, revenue-critical, operationally necessary, legally required, or explicitly confirmed as part of the product promise.

**Later Capability**:
A useful capability intentionally deferred beyond Production-complete Launch because no launch-critical outcome depends on it.

**Archived Capability**:
Historical behavior or data retained read-only for customer service, audit, accounting, or legal obligations, without preserving its original write workflow.

**Retired Capability**:
A capability deliberately absent from the replacement because it is unused, obsolete, unsafe, or generic legacy breadth with no accepted Akros value.

**B2C Channel**:
The Akros retail selling channel. Visitors may browse and complete a purchase as a guest or as an authenticated Retail Portal Principal.

**Retail Customer**:
A Party buying or considering a purchase through the B2C Channel. A Retail Customer may purchase as a guest or use a Retail Portal Principal for durable portal access.
_Avoid_: Customer Account, consumer user

**Retail Portal Principal**:
A Principal authorized to access a Retail Customer's saved addresses, commerce history, aftercare, favorites, and notifications. It is optional for B2C checkout.
_Avoid_: Customer Account, user account

**B2B Channel**:
The Akros trade selling channel. Public visitors may see neutral product information and request access, but Counterparty-specific assortment, prices, availability, and ordering require an approved Principal acting for that Counterparty.

**Counterparty Buyer**:
A Principal authorized to prepare and submit purchases for a Counterparty within its assigned limits and approval rules.
_Avoid_: Company Buyer, Company User

**Counterparty Approver**:
A Principal authorized to approve or return purchases that require Counterparty approval.
_Avoid_: Company Approver

**Counterparty Access Administrator**:
A Principal authorized to manage which Principals may act for a Counterparty and with which permissions.
_Avoid_: Company Account Administrator, account owner

**Repeat Order**:
A request to construct a new cart from a historical order's still-sellable items and configurations. Current commercial rules apply; historical price, tax, availability, shipping, and payment terms are not reinstated.
_Avoid_: Duplicate order, reorder at original price

**Assisted Support**:
An audited staff capability that exposes the customer's channel and commercial context without silently assuming the customer's identity. Any customer-affecting action remains explicit, attributed to the operator, and permission-checked.
_Avoid_: Impersonation, login as customer

**Customer Archive**:
The authorized read-only experience through which a customer or operator can access retained historical orders, documents, and claims. It is not itself the statutory accounting or tax archive.
