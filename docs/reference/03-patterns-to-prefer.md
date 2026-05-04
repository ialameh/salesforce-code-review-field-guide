# R3. Patterns to Prefer and Smells to Flag

A curated list of patterns that are safe and production-proven versus patterns that have caused production failures. Use this as a reference when making lens findings.

## Apex sharing and security patterns

**Prefer:**
```apex
public with sharing class AccountService {
    public static void updateAccount(Account acc) {
        acc = (Account) Security.stripInaccessible(AccessType.UPDATABLE, acc);
        update acc;
    }
}
```

**Prefer:**
```apex
List<Account> accounts = [SELECT Id, Name FROM Account WITH USER_MODE LIMIT 100];
```

**Prefer:**
```apex
FeatureManagement.checkPermission('Custom_Permission_Name');
```

**Flag:**
```apex
public class AccountService { // missing with sharing
    public static void updateAccount(Account acc) {
        update acc; // no FLS check, system context
    }
}
```

## Bulkification patterns

**Prefer:**
```apex
Map<Id, List<Contact>> accountIdToContacts = new Map<Id, List<Contact>>();
for (Contact c : [SELECT AccountId FROM Contact WHERE AccountId IN :accountIds]) {
    if (!accountIdToContacts.containsKey(c.AccountId)) {
        accountIdToContacts.put(c.AccountId, new List<Contact>());
    }
    accountIdToContacts.get(c.AccountId).add(c);
}
```

**Flag:**
```apex
for (Account acc : triggerNew) {
    List<Contact> contacts = [SELECT Id FROM Contact WHERE AccountId = :acc.Id];
    // SOQL inside for loop
}
```

## Async patterns

**Prefer:**
```apex
public class MyQueueable implements Queueable {
    private String idempotencyKey;
    public void execute(QueueableContext ctx) {
        // Check idempotency before processing
        if (alreadyProcessed(idempotencyKey)) return;
        // ... work
    }
}
```

**Prefer:**
```apex
public class MyBatch implements Database.Batchable<SObject>, Database.Stateful {
    public void execute(Database.BatchableContext ctx, List<SObject> scope) {
        // Bulk-safe, scope-limited
    }
    public void finish(Database.BatchableContext ctx) {
        // Chain next batch or log completion
    }
}
```

**Flag:**
```apex
public class MyBatch implements Database.Batchable<SObject> {
    // Not implementing Database.Stateful but holding state across chunks
    private List<SObject> accumulatedState = new List<SObject>();
    // This will not work as expected in batch context
}
```

## SOQL patterns

**Prefer:**
```apex
List<Account> accounts = [
    SELECT Id, Name
    FROM Account
    WHERE Status__c = 'Active'
    AND LastModifiedDate > :cutoffDate
    LIMIT 1000
];
```

**Prefer:**
```apex
// Use a Map to deduplicate and avoid repeated lookups
Map<Id, Account> accountMap = new Map<Id, Account>(
    [SELECT Id, Name FROM Account WHERE Id IN :accountIds]
);
```

**Flag:**
```apex
// LIKE with leading wildcard cannot use index
List<Account> accounts = [SELECT Id FROM Account WHERE Name LIKE '%Acme%'];
```

## Test patterns

**Prefer:**
```apex
@IsTest
static void testUpdateAccountHappyPath() {
    Account acc = new Account(Name = 'Test');
    insert acc;
    acc.Name = 'Updated';
    Test.startTest();
    update acc;
    Test.stopTest();
    Account result = [SELECT Name FROM Account WHERE Id = :acc.Id];
    System.assertEquals('Updated', result.Name);
}
```

**Prefer:**
```apex
@IsTest
static void testUpdateAccountWithFlsEnforcement() {
    User restrictedUser = [SELECT Id FROM User WHERE Profile.Name = 'Standard User' LIMIT 1];
    System.runAs(restrictedUser) {
        Account acc = new Account(Name = 'Test');
        insert acc;
        acc.Name = 'Updated';
        // Verify FLS blocks the write
        try {
            update acc;
            System.assert(false, 'Expected exception');
        } catch (System.DmlException e) {
            System.assert(e.getMessage().contains('INSUFFICIENT_ACCESS_OR_PRIVILEGE'));
        }
    }
}
```

**Flag:**
```apex
@IsTest(seeAllData=true) // pollutes test state, creates org dependency
static void testSomething() {
    // Relies on existing org data - not portable, not isolated
}
```

**Flag:**
```apex
try {
    service.run();
} catch (Exception ignored) {}
System.assert(true); // fake assertion - always passes
```

## Named Credential patterns

**Prefer:**
```apex
HttpRequest req = new HttpRequest();
req.setEndpoint('callout:MyNamedCredential/path/to/resource');
req.setMethod('GET');
req.setTimeout(120000); // 2 minute timeout for long-running callouts
```

**Flag:**
```apex
req.setEndpoint('https://api.external.com/resource');
req.setHeader('Authorization', 'Bearer sk_live_abcdef123456');
// Hardcoded endpoint and secret - security and deployment risk
```

## Error handling patterns

**Prefer:**
```apex
try {
    Database.update(records, false);
} catch (Exception e) {
    LogHandler.logError('ClassName.methodName', UserInfo.getUserId(), recordId,
        e.getMessage(), e.getStackTraceString());
    throw new CustomException('Failed to update records: ' + recordId);
}
```

**Flag:**
```apex
catch (Exception e) {
    // ignore
}
```

**Flag:**
```apex
catch (Exception e) {
    return null; // silent failure - caller has no way to know something went wrong
}
```

## What this chapter covered

- Production-proven patterns for security, bulkification, async, SOQL, tests, callouts, and error handling
- Smell patterns that should trigger a lens finding
- Side-by-side preferred versus flagged code examples

## References

- [Apex Security Best Practices](https://developer.salesforce.com/docs/atlas.en-us.apexcode.meta/apexcode/apex_security_best_practices.htm)
- [Apex Governor Limits](https://developer.salesforce.com/docs/atlas.en-us.apexcode.meta/apexcode/apex_limits.htm)
- [Apex Batch Best Practices](https://developer.salesforce.com/docs/atlas.en-us.apexcode.meta/apexcode/apex_batch.htm)
- [LWC Security](https://developer.salesforce.com/docs/atlas.en-us.lightning.meta/lightning/lwc_security.htm)