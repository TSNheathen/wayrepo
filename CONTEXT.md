# Akros Rebuild

This context defines the language used while planning Akros on a new architecture. The current deployment supplies evidence; it does not dictate the replacement's implementation or integration topology.

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
