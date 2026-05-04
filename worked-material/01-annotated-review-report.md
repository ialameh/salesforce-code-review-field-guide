# WM1. Annotated Review Report

This chapter shows the full 9-stage review pipeline applied to a fictional but realistic Salesforce codebase. The codebase is a simplified order management system. The review demonstrates how findings are extracted, how lenses are applied, and how the final report is structured.

The codebase has intentional defects across multiple lenses. The review identifies them and produces a prioritized fix list.

## The codebase

The review scope includes:

- `OrderController.cls` — AuraEnabled REST controller for order management
- `OrderService.cls` — Business logic for order processing
- `OrderTrigger.trigger` — Trigger on Order__c sObject
- `OrderLineItemTrigger.trigger` — Trigger on OrderLineItem__c sObject
- `OrderBatch.cls` — Batch job for order completion processing
- `ErpCalloutQueueable.cls` — Queueable for ERP integration
- `OrderServiceTest.cls` — Test class for order service

Total: 7 files, approximately 1,800 lines of Apex.

## Stage 1: Intake and Inventory

```md
## Inventory Summary

| Type | Count | Notes |
|---|---:|---|
| Apex classes | 4 | OrderController, OrderService, OrderBatch, ErpCalloutQueueable |
| Apex triggers | 2 | OrderTrigger, OrderLineItemTrigger |
| Test classes | 1 | OrderServiceTest (low coverage) |
| Batch classes | 1 | OrderBatch |
| Queueable classes | 1 | ErpCalloutQueueable |
| AuraEnabled methods | 3 | All in OrderController |
| Async components | 2 | OrderBatch, ErpCalloutQueueable |
| Integration points | 1 | ERP callout via Named Credential |
```

Notable: Only 1 test class for 4 production classes. Coverage will be a significant finding.

## Stage 2: System Map

```text
Entry point → OrderController (AuraEnabled, 3 methods)
  → OrderService (business logic)
    → OrderTrigger (after insert/update)
      → ErpCalloutQueueable (enqueued for each order)
    → OrderBatch (scheduled completion job)
      → ERP callout via Named Credential
  → OrderLineItemTrigger (before insert/update on line items)
```

Main flow: `createOrder()` → validates → writes Order__c → enqueues ERP notification.

## Stage 3: Evidence Extraction

Selected findings (abbreviated):

**Finding 1**
- File: `OrderController.cls`
- Class: `OrderController`
- Method: `createOrder()`
- Line: 34
- Pattern: `insert orderRecord;` — DML inside `for` loop iterating over line items
- Governor limit at risk: DML statements
- Severity: Critical
- Certainty: Confirmed

**Finding 2**
- File: `OrderController.cls`
- Class: `OrderController`
- Method: `createOrder()`
- Line: 22
- Pattern: No `with sharing` on class, no FLS check before DML
- Risk: System context execution, any user can write to any field
- Severity: High
- Certainty: Confirmed

**Finding 3**
- File: `ErpCalloutQueueable.cls`
- Class: `ErpCalloutQueueable`
- Method: `execute()`
- Line: 41
- Pattern: No idempotency guard. If job retried, ERP receives duplicate notification.
- Severity: High
- Certainty: Confirmed

## Stage 4: Lens Findings

(abbreviated — full findings in the full report)

**Governor Limits:** 3 Critical findings, 1 High finding
**Security:** 2 High findings, 1 Medium finding
**Bulkification:** 2 Critical findings
**Async:** 2 High findings
**Test Quality:** 1 Critical (no negative tests), 2 High (fake assertions)

## Stage 5: Failure Simulation

```md
| Scenario | Expected Behaviour | Actual Risk | Severity | Fix |
|---|---|---|---|---|
| 200 line items per order | DML limit hit at ~150 | 150 DML limit exceeded | Critical | Move DML outside loop |
| ERP callout timeout | Retry, idempotency guard | No guard, duplicate ERP notification | High | Add idempotency key |
| Concurrent order creation | Both orders processed | No active-run guard, both trigger ERP | High | Add active-run guard on trigger |
| Empty order (no line items) | Query returns empty, handle gracefully | No empty check, potential null access | Medium | Add null/empty check |
```

## Stage 6: Cross-Lens Contradiction Check

```md
| Tension | Resolution | Decision |
|---|---|---|
| Architecture calls OrderController a clean entry point. Security flags it as unsafe without sharing. | The class is clean as an entry point but unsafe as a security boundary. The fix requires both: keep the architecture, add with sharing. | Add `with sharing` to OrderController. Security resolved, architecture preserved. |
```

## Stage 7: Risk Ranking

```md
| Rank | Severity | Item | Effort | Impact |
|---|---|---|---|---|
| 1 | Critical | DML inside for loop in OrderController.createOrder() | S | Governor limit failure at normal volume |
| 2 | Critical | OrderService has no test coverage for negative path | M | No protection against invalid orders |
| 3 | High | ErpCalloutQueueable has no idempotency guard | S | Duplicate ERP notifications on retry |
| 4 | High | OrderController missing with sharing | S | System context execution, FLS bypass |
| 5 | High | OrderController missing FLS check before DML | S | User can write fields they cannot access |
```

## Stage 8: Final Report

(Produced following the format in Chapter R1)

## Stage 9: Self-Check

```md
**Surprised by:** The OrderService had more business logic than expected. I initially planned to review it as a thin passthrough but it does actual validation and calculation. This may have caused me to under-review the batch class.

**Skipped or treated lightly:** The LWC lens was not applied. There are no LWCs in scope but the REST endpoints are called from a React client that I did not review. This is a gap.

**Would say differently face-to-face:** I would tell the team that the OrderService business logic is well-structured but the OrderController security is a significant risk that should be addressed before any production deployment.

**Do not fully understand:** The ERP notification payload format. I reviewed the callout structure but not the payload schema. This is where a SOQL injection might hide.

**Style issues treated as production risks:** No.

**Reluctant to deploy:** Yes. The DML-in-loop finding alone means this code will fail at normal order volumes. It is not deployable in current state.
```

## What this chapter covered

- Full 9-stage review applied to a realistic codebase
- Inventory, system map, evidence extraction, and lens findings
- Failure simulation table and cross-lens contradiction resolution
- Prioritized fix list and self-check output

## References

- [Apex Governor Limits](https://developer.salesforce.com/docs/atlas.en-us.apexcode.meta/apexcode/apex_limits.htm)
- [Apex Batch Best Practices](https://developer.salesforce.com/docs/atlas.en-us.apexcode.meta/apexcode/apex_batch.htm)
- [Security Review Requirements](https://developer.salesforce.com/docs/atlas.en-us.securityGuide.meta/securityguide/sec_code_package_security_review.htm)