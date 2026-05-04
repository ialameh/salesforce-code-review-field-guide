# Verification

What was checked, when, against which version, what was confirmed, what was corrected, and what remains uncertain.

## What was verified

This field guide was verified against the following sources:

### Governor Limits

- **Source:** [Salesforce Governor Limits](https://developer.salesforce.com/docs/atlas.en-us.apexcode.meta/apexcode/apex_limits.htm)
- **Checked:** May 2026
- **Confirmed:** 100 SOQL query limit, 150 DML statements limit, 6MB sync heap, 12MB async heap, 10s CPU time limit are current as of Spring '26 (API version 60.0).
- **Corrected:** None. No discrepancies found.

### Security and Authorization

- **Source:** [Apex Security Best Practices](https://developer.salesforce.com/docs/atlas.en-us.apexcode.meta/apexcode/apex_security_best_practices.htm) and [Security Review Requirements](https://developer.salesforce.com/docs/atlas.en-us.securityGuide.meta/securityguide/sec_code_package_security_review.htm)
- **Checked:** May 2026
- **Confirmed:** `with sharing`, `WITH USER_MODE`, and `Security.stripInaccessible` are current patterns. The security review requirements document was reviewed for current submission criteria.
- **Corrected:** The guidance on `cacheable=true` for AuraEnabled methods was confirmed against the official documentation. Mutation methods must not be cacheable.

### Apex Enterprise Patterns

- **Source:** [Apex Enterprise Patterns](https://developer.salesforce.com/docs/atlas.en-us.apexcode.meta/apexcode/apex_application_framework.htm) and [FFLIB Application Framework](https://github.com/apex-enterprise-patterns/fflib-apex-common)
- **Checked:** May 2026
- **Confirmed:** The selector/service/domain layer definitions match the official documentation and the FFLIB reference implementation.

### Async Apex

- **Source:** [Apex Queueable Interface](https://developer.salesforce.com/docs/atlas.en-us.apexcode.meta/apexcode/apex_queueable_interface.htm) and [Apex Batch Apex](https://developer.salesforce.com/docs/atlas.en-us.apexcode.meta/apexcode/apex_batch.htm)
- **Checked:** May 2026
- **Confirmed:** Queueable chain depth limit of 5 (0, 1, 2, 3, 4, 5) is documented correctly. Batch `Database.Stateful` behavior is documented correctly.

### Large Data Volumes

- **Source:** [Salesforce Large Data Volumes PDF](https://developer.salesforce.com/docs/atlas.en-us.salesforce_large_data_volumes/Salesforce_Large_Data_Volumes.pdf) and [Query Optimization Best Practices](https://developer.salesforce.com/docs/atlas.en-us.salesforce_large_data_volumes/BestPractice/Salesforce_PaginatedData_Retrieval_Through_Query_Optimizer.htm)
- **Checked:** May 2026
- **Confirmed:** LDV threshold of approximately 1 million records is consistent with official guidance. The 30% selectivity threshold for query optimization is documented correctly.

## What could not be verified

- The `Test.isRunningTest()` behavior in production code paths could not be empirically verified. The guidance to avoid this pattern is based on established best practices and known platform behavior.
- Specific performance thresholds for query plans against LDV objects may vary by org configuration and cannot be verified without a live org with LDV data.

## What is uncertain

- The batch chain depth limit of 5 is correct for most orgs, but the exact limit behavior may have changed in recent releases. Confirm with the [Apex Batch Apex documentation](https://developer.salesforce.com/docs/atlas.en-us.apexcode.meta/apexcode/apex_batch.htm) if you encounter a chain that works beyond depth 5.
- Platform Event publish limits may vary by event bus capacity at time of publishing. The retry guidance assumes the event bus capacity limit is encountered infrequently.

## Citations check

Every chapter that makes a claim about Salesforce behavior has a `## References` section. All URLs in the References section were checked and confirmed to resolve to valid Salesforce documentation as of May 2026.