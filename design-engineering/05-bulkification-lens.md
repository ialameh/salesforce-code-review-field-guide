# DE5. Bulkification Lens

The Bulkification lens looks for code that assumes single-record processing and fails when given a list of records. Salesforce triggers, batches, and flows process records in bulk. Code that does not handle lists will fail at normal volumes.

## What this lens catches

- `triggerNew[0]` used without checking list size
- Per-record DML inside a loop (separate `insert` or `update` per record)
- Per-record SOQL inside a loop
- Per-record callouts inside a loop
- `Database.insert(records, false)` missing where partial success is appropriate
- Collections keyed by array index instead of by Id
- Static variables that carry per-record state across a trigger batch
- Trigger recursion that is not controlled by a static guard
- Non-idempotent code that will produce duplicate results on retry

## Single-record assumption patterns

```apex
// VIOLATION: Single-record assumption
trigger AccountTrigger on Account (after insert) {
    Account firstAccount = triggerNew[0]; // Assumes at least one record
    firstAccount.CustomField__c = 'value';
}
```

```apex
// VIOLATION: Per-record DML
for (Account acc : triggerNew) {
    acc.CustomField__c = 'processed';
    update acc; // DML per record, not bulk
}
```

```apex
// CORRECT: Bulk DML
List<Account> toUpdate = new List<Account>();
for (Account acc : triggerNew) {
    acc.CustomField__c = 'processed';
    toUpdate.add(acc);
}
update toUpdate; // Single DML statement for all records
```

## Required test scenarios

For every method that processes records, mentally test against:

| Scenario | What to check |
|---|---|
| 0 records | Does the code handle an empty triggerNew? |
| 1 record | Does the code work for the common single-record case? |
| 200 records | Does the code hit any governor limits? |
| Duplicate records | Does duplicate Id processing cause double writes? |
| Records without optional fields | Does the code assume fields are populated? |
| Batch chunking | Does the code hold state correctly across chunk boundaries? |
| Concurrent execution | Do two concurrent trigger invocations share static state incorrectly? |

## Trigger recursion guards

Static variables that control recursion are a common pattern. They are also a common source of bugs.

```apex
// Common pattern: static boolean guard
public class AccountTriggerHandler {
    private static boolean alreadyProcessed = false;

    public static void handleAfterInsert(List<Account> records) {
        if (alreadyProcessed) return;
        alreadyProcessed = true;
        // ... process records
    }
}
```

The bug: `alreadyProcessed` is static and per-transaction. In a single transaction that causes multiple trigger invocations, the second invocation is correctly blocked. But in a test class, static variables persist across test methods, causing tests to pass or fail depending on execution order.

Check whether the recursion guard is tested in isolation and whether its static persistence is understood by the team.

## Severity rules

Per-record DML inside a loop is **Critical** in a trigger or batch context. The code will hit the 150-DML limit at approximately 150 records.

Per-record SOQL inside a loop is **Critical** for the same reason. The code will hit the 100-query limit at approximately 100 records.

Single-record assumption without a guard is **High** if the code could be called from a trigger with multiple records.

## What this chapter covered

- The single-record assumption patterns this lens detects
- Correct bulk DML patterns
- Required test scenarios for bulk safety
- Trigger recursion guard patterns and their pitfalls
- Severity rules for bulkification findings

## References

- [Apex Bulkification Best Practices](https://developer.salesforce.com/docs/atlas.en-us.apexcode.meta/apexcode/apex_bulkification.htm)
- [Trigger Frameworks and Bulkification](https://developer.salesforce.com/docs/atlas.en-us.apexcode.meta/apexcode/apex_triggers.htm)