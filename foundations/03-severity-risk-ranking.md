# F3. Severity and Risk Ranking

Not all findings are equal. A finding that can cause wrong data in production is more severe than a naming inconsistency. A finding that could corrupt an external system is more severe than a logging gap.

Severity must be assigned by production impact, not by how easy the fix is, not by how many times the pattern appears, and not by whether the author will be embarrassed by it.

## The severity scale

**Critical** — Production failure, data leak, data corruption, or major security exposure. Deploy this code and the damage is already happening or certain.

Example: An `@AuraEnabled` method that mutates records without any sharing check, without FLS, and without a custom permission gate. Any authenticated user can modify any record.

**High** — Likely defect, missing authorization, compliance gap, or unstable async behavior. The code will probably fail, or will fail under normal conditions, or will allow unauthorized access.

Example: A trigger that does a SOQL query inside the for loop. Under a normal batch of 200 records, the transaction hits the 100-query governor limit.

**Medium** — Scalability risk, LDV edge case, operational complexity, or a behavioral inconsistency that could cause a support case. The code works today but will break under load or with data growth.

Example: A query with `ORDER BY LastModifiedDate` on a table with 8 million rows. The sort is not indexed. This will become a full-table scan as the table grows.

**Low** — Cleanup, clarity, naming, minor coupling, or documentation gap. The code works and is safe. Improving it is optional.

Example: A public method in an inner class that is never called from outside the class. Dead code that should be removed.

**Strength** — A good pattern worth preserving and replicating. Not a finding. A positive signal.

Example: A batch class that uses `Database.Stateful` correctly, implements `finish()` to chain the next batch, and has a full set of unit tests with meaningful assertions.

## Ranking by production impact

Ask these questions for every finding:

1. Can it cause wrong data? (If yes: Critical or High)
2. Can it leak data? (If yes: Critical or High)
3. Can it fail under normal volume? (If yes: High)
4. Can it block users? (If yes: High)
5. Can it corrupt external systems? (If yes: Critical)
6. Can it make deployment unstable? (If yes: High)
7. Is the fix small? (Does not reduce severity)
8. Is the risk hidden? (Increases severity if yes)
9. Is the issue likely to occur soon? (Increases priority within a severity tier)

## Effort does not affect severity

A finding that would take 30 seconds to fix but could cause a production outage is still Critical. A finding that would take a week to refactor but would only affect a rarely used feature is still Medium.

Severity is about impact. Effort is a separate column in the final report.

## The risk ranking table

The final report uses a ranked table:

```md
| Rank | Severity | Item | Effort | Impact |
|---|---|---|---|---|
| 1 | Critical | Missing WITH USER_MODE in ConfigController.runNow() | S | Data corruption risk |
| 2 | High | SOQL inside for loop in OrderTrigger.handleAfterInsert() | S | Governor limit failure |
```

Rank 1 is the most severe finding. Rank goes in priority order for action, not alphabetical order.

## Severity across lenses

Every lens uses the same severity scale. The Architecture lens does not have its own severity system. The Security lens does not have its own severity system. One scale, applied consistently across all lenses.

This prevents the common failure mode where reviewers assign different meanings to the same severity label in different contexts.

## What this chapter covered

- The five-level severity scale (Critical, High, Medium, Low, Strength)
- The nine-question production impact test
- Why effort is not a severity factor
- The ranked findings table format

## References

- [Salesforce Governor Limits](https://developer.salesforce.com/docs/atlas.en-us.apexcode.meta/apexcode/apex_limits.htm)
- [Apex Security Best Practices](https://developer.salesforce.com/docs/atlas.en-us.apexcode.meta/apexcode/apex_security_best_practices.htm)