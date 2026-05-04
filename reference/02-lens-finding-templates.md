# R2. Lens Finding Templates

Use these templates when writing findings under each lens. The template forces specificity and consistency. Copy the template structure into your review findings document and fill in the placeholders.

## Template: Architecture Finding

```text
## Architecture Finding

**File:** `<FileName>.cls`
**Class:** `<ClassName>`
**Method:** `<methodName>` or `Class constructor`
**Lines:** `<startLine> - <endLine>`

**Pattern:** [What the code does]

**Risk:** [What breaks and under what conditions]

**Severity:** [Critical / High / Medium / Low]

**Certainty:** [Confirmed / Likely / Possible / Cannot Verify]

**Recommended Fix:** [Concrete steps to resolve the finding]
```

## Template: Governor Limits Finding

```text
## Governor Limits Finding

**File:** `<FileName>.cls`
**Class:** `<ClassName>`
**Method:** `<methodName>`
**Lines:** `<startLine> - <endLine>`

**Pattern:** [SOQL query / DML statement / Callout] inside [for loop / trigger handler]

**Governor Limit at Risk:** [SOQL queries / DML statements / Heap / CPU / Callouts]

**Trigger Context:** [AfterInsert / BeforeUpdate / etc.] with `<estimatedRecordCount>` records

**Risk:** [At X records, this will hit Y limit]

**Severity:** [Critical / High / Medium / Low]

**Certainty:** [Confirmed / Likely / Possible]

**Recommended Fix:** [Move query outside loop / use Map to batch / add LIMIT / etc.]
```

## Template: Security Finding

```text
## Security Finding

**File:** `<FileName>.cls`
**Class:** `<ClassName>`
**Method:** `<methodName>`
**Lines:** `<startLine> - <endLine>`

**Pattern:** [Missing sharing / Missing FLS / Missing permission gate / Hardcoded secret]

**Security Vector:** [Unauthorized record access / Data leakage / Credential exposure]

**Severity:** [Critical / High / Medium / Low]

**Certainty:** [Confirmed / Likely / Possible]

**Security Review Impact:** [Would likely fail / Could pass with fixes]

**Recommended Fix:** [Add with sharing / add WITH USER_MODE / add FeatureManagement.checkPermission / remove hardcoded secret]
```

## Template: Async Finding

```text
## Async Finding

**File:** `<FileName>.cls`
**Class:** `<ClassName>`
**Method:** `<methodName>`
**Lines:** `<startLine> - <endLine>`

**Pattern:** [Queueable / Batch / Future / Scheduled] with [missing retry / missing idempotency / chain depth risk]

**Failure Scenario:** [Job fails halfway / Duplicate enqueue / Batch chain breaks]

**Recovery:** [Is there a recovery mechanism? Yes/No]

**Severity:** [Critical / High / Medium / Low]

**Certainty:** [Confirmed / Likely / Possible]

**Recommended Fix:** [Add idempotency guard / add retry with backoff / add active-run guard]
```

## Template: Test Quality Finding

```text
## Test Quality Finding

**File:** `<FileName>Test.cls`
**Class:** `<TestClassName>`
**Method:** `<testMethodName>`

**Pattern:** [No assertion / Swallowed exception / seeAllData / Hardcoded ID]

**What the test proves:** [Nothing / Execution but not correctness / Org-dependent state]

**Risk:** [Test passes when code is broken / Test fails in CI unpredictably / Test is not portable]

**Severity:** [Critical / High / Medium / Low]

**Certainty:** [Confirmed / Likely / Possible]

**Recommended Fix:** [Add assertion / remove seeAllData / use Test.createNoDefaults()]
```

## Template: Bulkification Finding

```text
## Bulkification Finding

**File:** `<FileName>.trigger` or `<FileName>.cls`
**Class:** `<ClassName>`
**Method:** `<methodName>`
**Lines:** `<startLine> - <endLine>`

**Pattern:** [Per-record DML / Per-record SOQL / scope[0] assumption / Static state in trigger]

**Execution Context:** [Trigger / Batch / Queueable] with `<N>` records

**Risk:** [At N records, hits governor limit / Produces incorrect results]

**Severity:** [Critical / High / Medium / Low]

**Certainty:** [Confirmed / Likely / Possible]

**Recommended Fix:** [Use Map keyed by Id / move DML outside loop / add isEmpty check]
```

## Template: Cross-Lens Contradiction

```text
## Cross-Lens Contradiction

**Lens A finding:** [What lens A says]

**Lens B finding:** [What lens B says]

**Tension:** [Why both are correct from their perspective]

**Resolution:** [What must be true for this pattern to be safe]

**Decision:** [Accept pattern with documented constraints / Reject pattern until fixed]
```

## Using these templates

Fill every field. A finding with missing fields is not a finding, it is a note. Notes do not produce fixes.

If a field is not applicable, write "N/A" with a reason. Do not leave fields blank.

## What this chapter covered

- Finding templates for Architecture, Governor Limits, Security, Async, Test Quality, and Bulkification
- Cross-lens contradiction template
- Instructions for completing every field

## References

- [Apex Governor Limits](https://developer.salesforce.com/docs/atlas.en-us.apexcode.meta/apexcode/apex_limits.htm)
- [Apex Security Best Practices](https://developer.salesforce.com/docs/atlas.en-us.apexcode.meta/apexcode/apex_security_best_practices.htm)
- [Apex Batch Apex](https://developer.salesforce.com/docs/atlas.en-us.apexcode.meta/apexcode/apex_batch.htm)