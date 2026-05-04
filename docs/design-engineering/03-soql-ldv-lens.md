# DE3. SOQL and LDV Lens

The SOQL and LDV lens looks for queries that will degrade or fail as data volume grows. A query that performs well at 10,000 records may become a full-table scan at 1 million records. This lens catches the patterns that look correct today but will fail under large data volumes.

## What this lens catches

- Queries with non-selective `WHERE` clauses (no indexed or high-cardinality filter first)
- `LIKE '%term%'` queries (cannot use indexes)
- Queries filtering on formula fields (evaluated at query time, not indexed)
- Queries with `ORDER BY` on non-indexed fields on large tables
- Subqueries in `SELECT` that return unbounded results (relationship query explosion)
- Queries without `LIMIT` that return large result sets
- Queries using `NOT IN` with large subqueries
- Queries that filter on `Null` fields without a compensating index
- Dynamic SOQL built without input validation (SOQL injection risk)
- Queries against tables known to be LDV (Large Data Volumes) without selective filters

## LDV thresholds

Salesforce publishes guidance on which objects commonly exceed 1 million records:

- Contact
- Task
- Event
- Case
- Lead
- Opportunity
- Custom objects with no archival policy

When a query targets an LDV object without a selective filter, the query may time out or cause slow performance for all users of that object.

## What selective means

A selective filter uses an indexed field or a high-cardinality field that returns less than about 30% of the table. The Salesforce query optimizer makes the final call, but these field types are typically selective:

- ID (always indexed)
- External ID (indexed)
- Name (indexed)
- OwnerId (indexed)
- RecordTypeId (indexed)
- Custom indexed fields

Fields that are not typically selective:

- Date fields (low cardinality unless filtered to a narrow range)
- Picklist fields (low cardinality)
- Boolean fields (two values, never selective)
- Formula fields (calculated at query time)
- Multi-select picklist fields

## Required questions

- Would this query survive 1 million records on this object?
- Is the most selective filter in the `WHERE` clause?
- Is there a date bounds filter that limits the return set?
- Does this query appear in a batch or trigger context where LDV is likely?
- What is the expected result set size at the 90th percentile of data volume?

## Query inspection format

For every query you find, apply this checklist:

```text
Query: [SELECT Id FROM Custom__c WHERE Status__c = :status AND CreatedDate > :cutoff LIMIT 1000]
Object: Custom__c (estimated rows: [from inventory])
Filter 1: Status__c [Indexed? Unknown. Check Custom Index metadata]
Filter 2: CreatedDate [Indexed by default. Date range is selective]
LIMIT: 1000 [Present. Good.]
Risk: If Status__c is not indexed and Custom__c has > 500k rows, this could time out.
```

## What this chapter covered

- Query patterns that fail under LDV conditions
- Which objects are commonly LDV
- What selective filtering means and how to assess it
- The query inspection format for documentation
- Required questions for assessing LDV risk

## References

- [Salesforce SOQL Best Practices](https://developer.salesforce.com/docs/atlas.en-us.apexcode.meta/apexcode/apex_troubleshooting_soql.htm)
- [Query & Search Optimization Best Practices](https://developer.salesforce.com/docs/atlas.en-us.salesforce_large_data_volumes/BestPractice/Salesforce_PaginatedData_Retrieval_Through_Query_Optimizer.htm)
- [Salesforce Large Data Volumes](https://developer.salesforce.com/docs/atlas.en-us.salesforce_large_data_volumes/Salesforce_Large_Data_Volumes.pdf)