# DE2. Governor Limits Lens

The Governor Limits lens looks for code that will hit a Salesforce governor limit under normal or elevated load. Every Salesforce transaction runs in a shared context with strict limits on SOQL queries, DML statements, heap size, CPU time, and callouts. Code that violates these limits fails at runtime with no recovery.

## What this lens catches

- SOQL queries inside `for` loops
- DML statements inside `for` loops
- HTTP callouts inside `for` loops
- `Schema.describe` calls inside `for` loops
- JSON serialization inside `for` loops
- Unbounded `List` or `Map` growth inside loops
- Nested loops that multiply record counts
- `Database.getQueryLocator` used without a `LIMIT`
- Repeated metadata queries that could be cached
- `System.debug` with large object serialization in production paths
- Heap-heavy operations (large JSON payloads, attachment processing) without size checks

## The governor limits to know

| Limit | Value | Failure mode |
|---|---|---|
| SOQL queries per transaction | 100 | `System.QueryException` |
| DML statements per transaction | 150 | `System.DmlException` |
| Callouts per transaction | 100 | `System.CalloutException` |
| Heap size (sync) | 6MB | `System.LimitException` |
| Heap size (async) | 12MB | `System.LimitException` |
| CPU time (sync) | 10 seconds | `System.LimitException` |
| Max rows per SOQL | No limit, but performance degrades | Slow query |

## The multiplier problem

The most common governor limit failure is not a single query inside a loop. It is a loop over records where each record triggers a query that itself queries related records.

```apex
// VIOLATION: Nested query multiplier
for (Account acc : triggerNew) {
    List<Contact> contacts = [SELECT Id, Name FROM Contact WHERE AccountId = :acc.Id];
    for (Contact c : contacts) {
        List<Task> tasks = [SELECT Id FROM Task WHERE WhoId = :c.Id];
        // 3 queries per account, multiplied by triggerNew size
    }
}
```

If `triggerNew` has 100 accounts, this can execute 300 SOQL queries before the inner task queries are counted.

## Required questions

- What happens at 200 records in the trigger?
- What happens at 10,000 records in a batch?
- What limit is most likely to fail first in this code path?
- Is there any guard that prevents the loop from growing unbounded?
- Could this code run in a context with a lower limit (e.g., a trigger firing during a batch)?

## Severity rules

SOQL inside a `for` loop is **Critical** if the loop size is unbounded (trigger, batch) and the query returns more than a few records.

DML inside a `for` loop is **Critical** for the same reason.

Any code that does not have an explicit size check before processing a collection is **High** until proven otherwise.

## What this chapter covered

- The governor limits that matter for reviews
- The multiplier problem with nested queries
- Required questions for every code path
- Severity rules for governor limit findings

## References

- [Salesforce Governor Limits](https://developer.salesforce.com/docs/atlas.en-us.apexcode.meta/apexcode/apex_limits.htm)
- [Understanding Execution Governors](https://developer.salesforce.com/docs/atlas.en-us.apexcode.meta/apexcode/apex governors execution.htm)
- [Apex Best Practices: Avoid Governor Limits](https://developer.salesforce.com/docs/atlas.en-us.apexcode.meta/apexcode/apex_bestpractices governor_limits.htm)