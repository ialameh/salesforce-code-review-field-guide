# DE6. Async and Transaction Lens

The Async and Transaction lens looks for code that runs asynchronously and has failure modes that are hard to detect and recover from. Async code is invisible to the user who triggered it and must be self-contained, idempotent, and observable.

## What this lens catches

- Queueable chains that could exceed the chain depth limit (5)
- Batch jobs that chain without checking `FlexQueue` availability
- Future methods that are not idempotent (retry causes duplicate action)
- Platform Event publish without retry handling
- `Database.Savepoint` used incorrectly or not at all for partial failure recovery
- `Database.RaisesPlatformEvents` on sObject that is not configured for platform events
- AsyncApexJob tracking that assumes only one job runs at a time
- Schedulable classes that fan out to too many jobs (超越了batch scope limits)
- Memory leaks in stateful batch classes
- Queueables enqueued in triggers without a guard (could exceed enqueue limit)
- Mixed DML operations that fail because they are in different transaction contexts

## Async failure modes

**Job fails halfway.** If a Queueable fails after writing some records but before calling the external system, what happens? Is there a rollback? Is the failure logged? Does the job retry?

**User starts same job twice.** If the user clicks a button twice and two Queueables are enqueued, do both run? Is there an idempotency guard?

**Batch chain stops at step 3 of 8.** If step 3 of a batch chain fails, does step 4 ever run? Is there a recovery mechanism?

**Platform Event publish fails.** If the publish call returns false (because the event bus is at capacity), does the code handle it? Does it retry? Does it log?

**Record lock occurs.** If a Queueable tries to update a record that is locked by another transaction, does it retry? How many times?

**Deployment happens while job is running.** If a class is modified and deployed while an existing Queueable is still running, does the job fail? Is there a version compatibility issue?

## Required questions

- If this async job fails mid-execution, can you determine what state the system is in?
- Is there a retry mechanism? How many retries? With what backoff?
- What happens if the same job is enqueued twice concurrently?
- Is the async job idempotent? Can it run safely on the same input twice?
- Can you trace the async job from enqueue to completion in the logs?

## Idempotency patterns

```apex
// CORRECT: Idempotent Queueable with state guard
public class ProcessOrderQueueable implements Queueable {
    private String orderId;
    private String runId; // Unique per enqueue, generated at trigger time

    public void execute(QueueableContext ctx) {
        // Check if already processed by this run
        Process__c state = [SELECT Status__c FROM Process__c WHERE OrderId__c = :orderId AND RunId__c = :runId LIMIT 1];
        if (state.Status__c == 'Completed') return; // Already done
        // ... process
    }
}
```

```apex
// VIOLATION: Not idempotent
public class ProcessOrderQueueable implements Queueable {
    public void execute(QueueableContext ctx) {
        // No guard. If this runs twice, it processes twice.
        // ... process order
    }
}
```

## What this chapter covered

- Async failure modes and what to look for in each
- Required questions for every async component
- Idempotency patterns and how to verify them
- Severity rules for async findings

## References

- [Apex Queueable Interface](https://developer.salesforce.com/docs/atlas.en-us.apexcode.meta/apexcode/apex_queueable_interface.htm)
- [Apex Batch Apex](https://developer.salesforce.com/docs/atlas.en-us.apexcode.meta/apexcode/apex_batch.htm)
- [Platform Events Best Practices](https://developer.salesforce.com/docs/atlas.en-us.apexcode.meta/apexcode/apex_platform_events_best_practices.htm)