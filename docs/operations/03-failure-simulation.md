# O3. Failure Simulation

The failure simulation stage is where you stop reviewing code and start imagining what can go wrong. For each major process, you simulate failure scenarios that the code did not explicitly protect against.

This stage catches the problems that correct-looking code has when it meets the real world: empty data, large data, concurrent runs, partial failures, external system failures, and deployment mismatches.

## The 10 failure scenarios

For every significant process, test these scenarios mentally:

| # | Scenario | What to check |
|---|---|---|
| 1 | Happy path | Normal data, normal volume, authorized user |
| 2 | Empty data path | What happens when no records match the query? |
| 3 | Large data path | What happens at 200 records? At 10,000? |
| 4 | Permission-restricted user | What does the code do when FLS blocks a field? |
| 5 | Concurrent execution | Two users start the same job at once |
| 6 | Partial failure | DML succeeds on 8 of 10 records |
| 7 | Retry | The same job starts again after a failure |
| 8 | External system failure | Callout times out or returns an error |
| 9 | Deployment/config mismatch | Metadata says X but code expects Y |
| 10 | Post-failure diagnosis | Can you determine what happened from the logs? |

## What to look for in each scenario

**Empty data path:** Does the code assume records exist? Does it access `scope[0]` or `records[0]` without checking `isEmpty()`? Does it return null where a list is expected?

**Large data path:** Does the code query 200 records and then loop over them doing DML inside the loop? Does it build an in-memory collection that could exceed 12MB heap?

**Permission-restricted user:** Does the code write to fields that the user does not have write access to? Does it silently skip, or does it throw an exception?

**Concurrent execution:** If two users start the same batch job, does the second one detect the first and abort? Or do both run and cause duplicate work?

**Partial failure:** When `Database.update(records, false)` is called, does the code handle the partial success case? Or does it assume all-or-nothing?

**Retry:** If the job is retried, does it re-process already-processed records? Is there an idempotency key or a guard that prevents double execution?

**External system failure:** If the HTTP callout fails, does the Queueable retry? Is there a limit on retries? Is the failure logged in a way that someone can diagnose it?

**Deployment/config mismatch:** Does the code call a handler class that no longer exists in the deployment? Does a Custom Metadata config that was present in staging get deleted before production deployment?

**Post-failure diagnosis:** When something breaks at 2am, can you determine what happened from the logs? Or do you have no visibility into the process?

## Failure simulation output

```md
## Failure Simulation

| Scenario | Expected Behaviour | Actual Risk | Severity | Fix |
|---|---|---|---|---|
| 200 records in trigger | Governor limit holds | 100 SOQL limit hit | High | Move query outside loop |
| Concurrent batch run | Second run detects first | Both run to completion | High | Add active-run guard |
| Empty data path on query | Returns empty list, handles gracefully | Null pointer on scope[0] | High | Add isEmpty check |
```

## Severity rules for failure simulation

A scenario that will definitely fail under normal conditions is **Critical**.
A scenario that will likely fail under elevated conditions is **High**.
A scenario that only fails under extreme conditions is **Medium**.
A scenario that is handled correctly is **Strength**.

## What this chapter covered

- The 10-scenario failure simulation framework
- What to check in each scenario
- How to document findings in the failure simulation table
- Severity rules for simulation findings

## References

- [Apex Batch Governor Limits](https://developer.salesforce.com/docs/atlas.en-us.apexcode.meta/apexcode/apex_batch.htm)
- [Database Class Methods](https://developer.salesforce.com/docs/atlas.en-us.apexcode.meta/apexcode/apex_system_database.htm)