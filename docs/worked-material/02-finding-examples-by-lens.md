# WM2. Finding Examples by Lens

A curated set of finding examples organized by lens. Each example shows the specific code, the risk, the severity, the certainty, and the recommended fix. Use these as reference when writing your own findings.

## Architecture Findings

### Example 1: God Class

**File:** `AccountService.cls`
**Class:** `AccountService`
**Lines:** 1-487

**Pattern:** Single class contains 487 lines across 14 public methods spanning 4 distinct responsibilities: account CRUD, contact management, opportunity tracking, and reporting metrics.

**Risk:** The class does too much. Any change to contact management logic risks breaking opportunity logic. Testing requires the entire class, not individual methods. New developers cannot understand the scope quickly.

**Severity:** High
**Certainty:** Confirmed

**Recommended Fix:** Split into `AccountService`, `ContactManagementService`, `OpportunityTrackingService`, `ReportingService`. Extract shared utilities into a shared module. Target each service being testable in isolation.

---

### Example 2: Trigger Doing Business Logic

**File:** `OrderTrigger.trigger`
**Lines:** 15-43

**Pattern:** Trigger handler code calculates total order amount and writes it to a parent field directly, instead of delegating to a service method.

```apex
// Inside OrderTrigger.trigger
for (Order__c order : triggerNew) {
    Decimal total = 0;
    for (OrderLineItem__c item : [SELECT Amount__c FROM OrderLineItem__c WHERE OrderId__c = :order.Id]) {
        total += item.Amount__c;
    }
    order.TotalAmount__c = total;
}
```

**Risk:** Logic embedded in trigger cannot be tested without a trigger test context. Logic cannot be reused from non-trigger entry points (REST, Queueable). The nested SOQL is a governor limit risk.

**Severity:** High
**Certainty:** Confirmed

**Recommended Fix:** Move the total calculation to `OrderService.calculateTotal(orderId)` and call it from both the trigger handler and any other entry points. The trigger handler should delegate, not compute.

---

## Governor Limits Findings

### Example 1: SOQL in Trigger Loop

**File:** `ProjectTrigger.trigger`
**Method:** `handleAfterInsert()`
**Lines:** 28-37

**Pattern:**
```apex
for (Project__c proj : triggerNew) {
    List<Resource__c> resources = [
        SELECT Id, Name FROM Resource__c
        WHERE ProjectId__c = :proj.Id
    ];
    for (Resource__c res : resources) {
        res.ProjectName__c = proj.Name;
    }
    update resources;
}
```

**Risk:** At 100 projects in triggerNew, this executes 100 SOQL queries and up to 100 DML statements. Governor limit: 100 queries. Limit will be exceeded around 100 records.

**Severity:** Critical
**Certainty:** Confirmed

**Recommended Fix:** Collect all project IDs and query all related resources in a single query outside the loop. Update all resources in a single DML statement.

---

### Example 2: HTTP Callout in Loop

**File:** `OrderBatch.cls`
**Method:** `execute()`
**Lines:** 58-71

**Pattern:**
```apex
for (Order__c order : scope) {
    HttpRequest req = new HttpRequest();
    req.setEndpoint('callout:ErpApi/notify');
    req.setMethod('POST');
    req.setBody(JSON.serialize(order));
    Http http = new Http();
    HttpResponse res = http.send(req); // Synchronous callout inside batch
}
```

**Risk:** Synchronous HTTP callouts inside a batch job are not supported by Salesforce. This will throw `System.CalloutException`. Even if async callouts were supported, one callout per order means 200 callouts for a scope of 200, exceeding the 100-callout limit.

**Severity:** Critical
**Certainty:** Confirmed

**Recommended Fix:** Enqueue an asynchronous callout via Queueable for each order, outside the batch execute. The Queueable executes in its own transaction and can make the HTTP callout.

---

## Security Findings

### Example 1: Missing Sharing Model

**File:** `TPM_ConfigController.cls`
**Class:** `TPM_ConfigController`
**Method:** `updateConfig()`
**Lines:** 19-27

**Pattern:**
```apex
public class TPM_ConfigController { // No sharing declaration
    @AuraEnabled
    public static void updateConfig(Id configId, String value) {
        TPM_Config__c config = [SELECT Id FROM TPM_Config__c WHERE Id = :configId];
        config.Value__c = value;
        update config; // System context, no FLS check
    }
}
```

**Risk:** Class runs in system context. Any authenticated user who can call this method can update any TPM_Config__c record, including production configuration values. No field-level security enforcement.

**Severity:** Critical
**Certainty:** Confirmed

**Security Review Impact:** Likely fail.

**Recommended Fix:** Add `with sharing` to the class. Add `Security.stripInaccessible` before DML. Add a custom permission gate for production mutations: `FeatureManagement.checkPermission('TPM_Config_Admin')`.

---

### Example 2: Cacheable Mutation Method

**File:** `AccountController.cls`
**Method:** `updateAccount()`
**Lines:** 12

**Pattern:**
```apex
@AuraEnabled(cacheable=true)
public static void updateAccount(Id accountId, String name) {
    Account acc = [SELECT Id FROM Account WHERE Id = :accountId];
    acc.Name = name;
    update acc;
}
```

**Risk:** `cacheable=true` means Salesforce may cache the result of this method call and reuse it across component instances. A mutation operation cached and replayed could overwrite data with stale values, or could execute in a user context different from the one that called it.

**Severity:** High
**Certainty:** Confirmed

**Recommended Fix:** Remove `cacheable=true`. Mutation methods must not be cacheable. Mark only read-only data retrieval methods as cacheable.

---

## Async Findings

### Example 1: Missing Idempotency Guard

**File:** `ErpNotificationQueueable.cls`
**Method:** `execute()`
**Lines:** 31-44

**Pattern:** Queueable sends an order notification to the ERP. If the transaction commits and then the callout fails (network error), the Queueable retries. The ERP receives the notification twice.

```apex
public void execute(QueueableContext ctx) {
    HttpRequest req = new HttpRequest();
    req.setEndpoint('callout:ErpApi/orders/' + orderId + '/notify');
    req.setMethod('POST');
    Http http = new Http();
    HttpResponse res = http.send(req);
    // No idempotency key in request, no guard against re-execution
}
```

**Risk:** Retry causes duplicate ERP notification. ERP processes the same order twice. Could cause duplicate shipments or billing.

**Severity:** High
**Certainty:** Confirmed

**Recommended Fix:** Add an idempotency key to the request header. Add a check at the start of execute() that queries a custom object or Custom Metadata to see if this order ID has already been processed in this run context.

---

### Example 2: Batch Chain Without Depth Check

**File:** `OrderCompletionBatch.cls`
**Method:** `finish()`
**Lines:** 67-72

**Pattern:**
```apex
public void finish(Database.BatchableContext ctx) {
    // Always chains next step regardless of depth
    Database.executeBatch(new OrderLineProcessingBatch(), 200);
}
```

**Risk:** If `OrderCompletionBatch` is itself triggered by another batch, the chain depth could reach the Salesforce limit of 5 chain depth levels (0, 1, 2, 3, 4, 5). At depth 5, `Database.executeBatch` throws a `LimitException`.

**Severity:** High
**Certainty:** Likely

**Recommended Fix:** Track chain depth in Custom Metadata. Check current depth before enqueuing. If depth limit is reached, schedule the next batch via `System.scheduleBatch` instead of direct `Database.executeBatch`.

---

## What this chapter covered

- Worked finding examples for Architecture, Governor Limits, Security, and Async lenses
- Each example with file, class, method, lines, pattern, risk, severity, certainty, and fix
- Realistic code patterns that surface in production Salesforce codebases

## References

- [Apex Enterprise Patterns](https://developer.salesforce.com/docs/atlas.en-us.apexcode.meta/apexcode/apex_application_framework.htm)
- [Apex Security Best Practices](https://developer.salesforce.com/docs/atlas.en-us.apexcode.meta/apexcode/apex_security_best_practices.htm)
- [Apex Governor Limits](https://developer.salesforce.com/docs/atlas.en-us.apexcode.meta/apexcode/apex_limits.htm)