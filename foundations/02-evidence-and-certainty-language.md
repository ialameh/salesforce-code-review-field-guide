# F2. Evidence and Certainty Language

A finding without evidence is an opinion. An opinion does not change behavior. Evidence changes behavior.

This guide requires every finding to be grounded in specific, observable facts from the code. This chapter defines the evidence standard, the certainty model, and the format for writing findings that land.

## The evidence standard

A finding must contain all of the following:

| Element | Example |
|---|---|
| File name | `TPM_NCConfigController.cls` |
| Class name | `TPM_NCConfigController` |
| Method name | `runNow()` |
| Line number or range | Line 47 |
| Code pattern | `@AuraEnabled public void runNow()` |
| Risk | No custom permission gate. Any user with Apex class access can trigger the production process. |
| Severity | High |
| Certainty | Confirmed |

A finding that says "this method may have security issues" is not a finding. A finding that says "`TPM_NCConfigController.runNow()` at line 47 is `@AuraEnabled` and enqueues a batch process with no custom permission gate. Any user with access to the Apex class can trigger the production process. Severity: High. Certainty: Confirmed." is a finding.

The second format produces action. The first produces argument.

## Certainty model

Use one of four certainty labels for every finding:

**Confirmed** — You read the code and the pattern is present. There is no ambiguity.

```apex
// CONFIRMED: SOQL inside for loop at line 34
for (Project__c proj : triggerNew) {
    List<Resource__c> resources = [SELECT Id FROM Resource__c WHERE ProjectId__c = :proj.Id];
    // ...
}
```

**Likely** — The pattern is present but could be mitigated by something you cannot verify from the supplied files. Note it as Likely with a reason.

```apex
// LIKELY: HttpRequest inside for loop at line 55
// Cannot confirm if there is a callout limit gate inside the service call
```

**Possible** — The pattern could exist given the structure, but you did not see direct evidence. Flag it as Possible with a specific question to investigate.

**Cannot Verify** — The finding requires runtime context, org data, or a test run that you cannot perform with the supplied files. State this explicitly and describe what would be needed to verify it.

## Writing findings that land

A finding has three parts:

1. **What you found** — The specific code pattern, named exactly
2. **Why it is a risk** — The production consequence, described plainly
3. **What to do** — The specific fix, in concrete terms

The format:

```text
[SEVERITY] [ClassName.methodName] [LINE] — [what you found]. [Why it is a risk]. [What to do].
```

Example:

```text
High | TPM_NCConfigController.runNow() | line 47 | @AuraEnabled method enqueues a batch process without a custom permission gate. Any user with Apex class access can trigger a production operation. Add FeatureManagement.checkPermission('TPM_Production_Run') before enqueueing.
```

## What not to write

Do not write vague findings:

- "Consider adding a permission check." — What permission? Where?
- "This may cause issues at scale." — What scale? What issues?
- "The architecture could be improved." — In what way? For what purpose?

Vague findings do not produce fixes. Specific findings do.

## Finding count versus quality

A review with 50 vague findings is less useful than a review with 5 specific findings. Prioritize specificity over count.

When you have many findings, group them. Write the most severe findings individually. Write a paragraph summarizing the pattern for lower-severity groups.

## What this chapter covered

- The seven-element evidence standard for findings
- The four-level certainty model and when to use each label
- The finding format that produces action
- What vague findings look like and why to avoid them

## References

- [Apex Security Best Practices](https://developer.salesforce.com/docs/atlas.en-us.apexcode.meta/apexcode/apex_security_best_practices.htm)
- [Security Review Requirements](https://developer.salesforce.com/docs/atlas.en-us.securityGuide.meta/securityguide/sec_code_package_security_review.htm)