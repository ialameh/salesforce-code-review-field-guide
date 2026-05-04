# DE4. Security and Authorization Lens

The Security and Authorization lens looks for code that fails to enforce who can do what. It checks for missing CRUD, missing FLS, unsafe AuraEnabled methods, insufficient permission gates, and data exposure risks. This lens is the most likely to identify findings that would cause a Salesforce Security Review failure.

## What this lens catches

- Apex classes missing `with sharing` when they access records
- `@AuraEnabled` methods that mutate data without a permission gate
- `@AuraEnabled` methods marked `cacheable=true` that perform mutations
- Missing `WITH USER_MODE` in SOQL queries
- Missing `Security.stripInaccessible` before DML
- Missing `Schema.sObjectType.Account.isAccessible()` pattern for field-level checks
- `FeatureManagement.checkPermission` missing where production mutations occur
- Hardcoded credentials or secrets in source code
- Named Credentials not used for callouts (hardcoded URLs and tokens)
- Error messages that expose internal system details
- Debug logs that contain sensitive record data
- Guest user access to mutating AuraEnabled methods
- Client-side-only authorization (button hiding is not authorization)

## The sharing model

Every Apex class that accesses records must declare a sharing model:

- `with sharing` — enforces record access based on sharing rules and profiles
- `without sharing` — does not enforce sharing (use only when necessary and explicitly)
- `inherited sharing` — defers to the calling context (risky if calling from `without sharing`)

If a class accesses records and does not declare a sharing model, it runs in the system context. That means it can read and write any record regardless of sharing rules. That is a **Critical** finding.

## Field-level security enforcement

Checking CRUD (create, read, update, delete) at the object level is not enough. The code must also check FLS (Field Level Security) before reading or writing specific fields.

```apex
// CORRECT: WITH USER_MODE and Security.stripInaccessible
public with sharing class AccountController {
    public static void updateAccount(Account acc) {
        acc = (Account) Security.stripInaccessible(AccessType.UPDATABLE, acc);
        update acc;
    }
}
```

```apex
// VIOLATION: No sharing model, no FLS check
public class AccountController {
    public static void updateAccount(Account acc) {
        update acc; // runs in system context, no FLS check
    }
}
```

## Required questions

- Does every AuraEnabled mutation method have an explicit permission gate?
- Does the code use `WITH USER_MODE` or `Security.stripInaccessible`?
- What happens if a guest user calls this AuraEnabled method?
- Is the error message exposing stack traces or internal field names?
- Are any secrets (passwords, tokens, endpoint URLs) hardcoded in the source?
- What is the minimum permission a user needs to call this method?

## Security review readiness test

Ask: if this code were submitted for a Salesforce Security Review, would it pass?

Indicators it would likely pass:
- `with sharing` on all classes that access records
- `WITH USER_MODE` on all queries
- `Security.stripInaccessible` before all DML
- Custom permission gates on mutating AuraEnabled methods
- Named Credentials for all external callouts
- No hardcoded secrets in source
- Error messages that do not expose internal details

Indicators it would likely fail:
- Any `without sharing` class that mutates records
- Missing FLS checks on mutating AuraEnabled methods
- Hardcoded credentials or endpoint URLs
- Error messages that include field names or sObject types

## What this chapter covered

- The security patterns this lens detects
- The sharing model and when to use each setting
- FLS enforcement patterns with `WITH USER_MODE` and `Security.stripInaccessible`
- Required security questions for every entry point
- The security review readiness test

## References

- [Salesforce Security Review Requirements](https://developer.salesforce.com/docs/atlas.en-us.securityGuide.meta/securityguide/sec_code_package_security_review.htm)
- [Apex Security Best Practices](https://developer.salesforce.com/docs/atlas.en-us.apexcode.meta/apexcode/apex_security_best_practices.htm)
- [Secure Coding Techniques](https://developer.salesforce.com/docs/atlas.en-us.apexcode.meta/apexcode/apex_secure_coding.htm)
- [User Mode for SOQL Queries](https://developer.salesforce.com/docs/atlas.en-us.apexcode.meta/apexcode/apex_system_schema_database.htm)