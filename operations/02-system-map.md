# O2. System Map

The system map is your mental model of how the code actually runs. It is not a class diagram. It is a trace of the path data takes from entry point to final state.

Building a system map forces you to understand what the code does, not just what it contains. This is where architectural issues surface: a class that appears clean in isolation but is part of a problematic flow.

## Entry points

Every Salesforce codebase has entry points where external actors reach the code. Identify all of them:

| Entry point type | What to look for |
|---|---|
| `@AuraEnabled` methods | Public Aura-enabled controller methods |
| `@RestResource` methods | HTTP REST endpoints |
| Trigger handlers | Any logic executed by a trigger |
| Scheduled jobs | `Schedulable` interface implementations |
| Platform Event subscribers | `EventBus.subscribe()` or `Trigger` on Platform Event |
| Flow invocations | Invocable methods called by flows |
| Outbound messaging | HTTP callouts triggered by workflow rules |
| Inbound email handlers | `Messaging.InboundEmailHandler` |

For each entry point, trace the path:

```text
Entry point → first handler → service layer → data access → async/callout → result
```

## The one-paragraph summary

After tracing the paths, write one paragraph that describes the runtime flow of the most critical business process in the codebase.

Example:

> This codebase handles purchase order approval. The `PurchaseOrderController` receives approval requests via REST, calls `PurchaseOrderService.validate()` to check budget availability, writes the approval record via `PurchaseOrderSelector`, and enqueues `ApprovalQueueable` to notify the external ERP system. The ERP callback is handled by `ErpCallbackController`, which writes the response back through `PurchaseOrderService.updateFromErp()`.

That paragraph should be enough for a new developer to understand what the system does without reading a single class.

## What to flag in the system map

**Long chains.** A path with more than 4 hops from entry to data write has too many failure points. Trace and simplify.

**Mixed responsibilities.** A class that handles both UI entry and data access is doing too much. Flag it for the Architecture lens.

**Silent async.** Any entry point that enqueues async work without an immediate observable result. The caller does not know when or if it completes.

**Shared state.** Static variables that carry data across method calls within a transaction. These are hidden coupling.

**Circular dependencies.** Class A calls B calls C calls A. This is a deployment and testing hazard.

## System map format

```md
## Architecture Summary

Entry point → orchestration → data access → business logic → async/callout → UI/result.

[One paragraph describing the main runtime flow]

## Entry Points

| Entry Point | Type | Purpose | Risk |
|---|---|---|---|
| PurchaseOrderController | REST | Approval submission | No auth on POST |
| ApprovalQueueable | Queueable | ERP notification | No retry |
```

## What this chapter covered

- How to trace entry point paths through the codebase
- How to write the one-paragraph runtime summary
- What to flag in the system map (long chains, mixed responsibilities, silent async, shared state, circular deps)
- The system map output format

## References

- [Apex Enterprise Patterns: Selector Layer](https://developer.salesforce.com/docs/atlas.en-us.apexcode.meta/apexcode/apex_application_framework.htm)
- [Apex Trigger Frameworks](https://developer.salesforce.com/docs/atlas.en-us.apexcode.meta/apexcode/apex_triggers.htm)