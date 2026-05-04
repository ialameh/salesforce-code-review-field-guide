# DE11. Test Quality Lens

The Test Quality lens looks at whether the test suite proves behavior or merely inflates coverage numbers. A test that runs code but does not assert anything is not a test. It is a smoke test that tells you the code did not throw an exception, which is not the same as telling you the code did the right thing.

## What this lens catches

- Tests with no assertions or only `System.assert(true)`
- Tests that assert implementation details instead of outcomes
- Tests that catch exceptions and swallow them
- Tests that depend on specific org data (`@IsTest(seeAllData=true)`)
- Tests that do not cover the failure path (negative tests)
- Tests that do not cover bulk operations (200 records)
- Tests that do not cover permission-restricted users
- Tests with hardcoded IDs that will break when records are deleted and recreated
- Tests that are not isolated from each other (order-dependent)
- Tests that take over 10 seconds to run (test timeout issues in CI)
- Tests that use `Test.stopTest()` and `Test.startTest()` inconsistently with actual async behavior

## The assertion quality test

For every test method, ask: if this test passes, what do I know about the system?

A test that passes and says nothing is a **Weak test**.

```apex
// WEAK: No assertion, just execution
@IsTest
static void testUpdateAccount() {
    Account acc = new Account(Name = 'Test');
    insert acc;
    acc.Name = 'Updated';
    update acc; // Did it actually update? No way to know from this test.
}
```

```apex
// STRONG: Assertion proves the outcome
@IsTest
static void testUpdateAccountName() {
    Account acc = new Account(Name = 'Test');
    insert acc;
    acc.Name = 'Updated';
    update acc;
    Account result = [SELECT Name FROM Account WHERE Id = :acc.Id];
    System.assertEquals('Updated', result.Name);
}
```

## Required test categories

Every significant feature needs these test types:

| Test type | What it proves |
|---|---|
| Happy path | Normal input produces normal output |
| Negative test | Invalid input produces appropriate error |
| Bulk test | 200 records processed correctly |
| Permission test | Restricted user gets appropriate behavior |
| FLS test | Fields the user cannot write are blocked |
| Async test | Queueable/Batch runs correctly and chains properly |
| Failure test | External callout failure is handled gracefully |
| Metadata test | Configuration changes produce expected behavior |

## What makes a test fake

A fake test is one that passes under all conditions, including when the code is broken. These patterns produce fake tests:

```apex
// FAKE: assert(true) is always true
System.assert(true);
```

```apex
// FAKE: swallowing exceptions hides failures
try {
    service.run();
} catch (Exception ignored) {}
```

```apex
// FAKE: seeAllData creates dependency on org state
@IsTest(seeAllData=true)
// Any test that reads existing org records will pass or fail based on org state, not code behavior
```

## Test isolation and CI stability

Tests that depend on each other are a CI instability risk. In a CI pipeline that runs all tests in parallel, order-dependent tests fail unpredictably.

Check for:
- Static variables that carry state from one test to the next
- Tests that create records in `Test.setup()` and assume a specific order of execution
- Tests that query records created by other test methods (without `Test.setup()`)

## What this chapter covered

- Test quality patterns that indicate coverage bait versus real coverage
- The assertion quality test
- Required test categories for every significant feature
- Fake test patterns and how to identify them
- Test isolation and CI stability risks

## References

- [Apex Testing Best Practices](https://developer.salesforce.com/docs/atlas.en-us.apexcode.meta/apexcode/apex_testing_best_practices.htm)
- [IsTest Annotations](https://developer.salesforce.com/docs/atlas.en-us.apexcode.meta/apexcode/apex_testing_istest.htm)
- [Testing Custom Settings](https://developer.salesforce.com/docs/atlas.en-us.apexcode.meta/apexcode/apex_testing_custom_settings.htm)