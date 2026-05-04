# O1. Intake and Inventory

Before you judge a single line of code, you need to know what exists. The intake and inventory stage is a complete enumeration of every artifact in the review scope. It takes 10 to 15 minutes for a mid-sized project and produces the map that every subsequent lens relies on.

Skipping this stage is the most common reason reviews miss things. If you do not know what exists, you cannot know what is missing.

## What to inventory

Classify every artifact in the provided source:

| Type | What to count | What to flag |
|---|---|---|
| Apex classes | Count, line range | Classes over 300 lines |
| Apex triggers | Count, object association | Large handler classes |
| Test classes | Count, coverage class association | `@IsTest(seeAllData=true)` |
| Batch classes | Count | Stateful usage, finish chaining |
| Queueable classes | Count | Enqueue patterns, chained depth |
| Schedulable classes | Count | Fan-out patterns |
| Future methods | Count | HTTP callouts, retry logic |
| Platform Event publishers | Count | Publish limits, retry handling |
| Platform Event subscribers | Count | Event handler behavior |
| AuraEnabled methods | Count | Cacheable flags, mutation vs read |
| RestResource endpoints | Count | Auth requirements |
| Invocable methods | Count | Internal vs public exposure |
| LWCs | Count | Wire adapters, imperative calls |
| Flows | Count | Type, trigger vs screen flow |
| Custom Metadata types | Count | Field usage in code |
| Custom Settings | Count | Hierarchy usage |
| Named Credentials | Count | Auth protocols |
| Remote Site Settings | Count | Endpoints |
| Permission Sets | Count | Custom permissions |
| Profiles | Count | Login hours, IP ranges |

For each type, note the most complex or largest artifact. Complexity is a risk multiplier. A 50-line class with one AuraEnabled method and a SOQL query is low complexity. A 600-line class with four AuraEnabled methods, two queueables, and a metadata reader is high complexity.

## The inventory output

Produce this markdown table as the first artifact of every review:

```md
## Inventory Summary

| Type | Count | Notes |
|---|---:|---|
| Apex classes | | |
| Apex triggers | | |
| Test classes | | |
| Batch classes | | |
| Queueable classes | | |
| Schedulable classes | | |
| Future methods | | |
| REST endpoints | | |
| AuraEnabled methods | | |
| Invocable methods | | |
| LWCs | | |
| Flows | | |
| Custom Metadata types | | |
| Named Credentials | | |
| Async components | | |
| Integration points | | |
```

If line counts are available, include them. Total lines of Apex is a useful proxy for review complexity.

## What to do with the inventory

The inventory tells you where to focus. A codebase with 5 batch classes and 2 queueables needs heavy Async lens coverage. A codebase with 30 AuraEnabled methods needs heavy Security and Bulkification coverage.

A codebase with no test classes at all is a special case. Flag it immediately. No test classes means test quality cannot be assessed. That is a High finding before you even open a single class.

## Common mistakes

**Counting only, not flagging.** Listing 20 Apex classes without noting which ones are over 300 lines misses the risk multiplier. The large classes are where governor limit issues hide.

**Treating test classes as less important.** Test classes are the only way to verify behavior. A codebase with weak test classes is a codebase where behavior is unverified. That is a High finding.

**Skipping flows and metadata.** Flows are executable artifacts that can cause data mutations just like Apex. Custom Metadata drives runtime behavior. Both must be in the inventory.

## What this chapter covered

- Complete inventory taxonomy for Salesforce codebases
- Complexity flagging criteria
- The inventory summary markdown format
- How to use inventory to prioritize lens coverage

## References

- [Apex Enterprise Patterns](https://developer.salesforce.com/docs/atlas.en-us.apexcode.meta/apexcode/apex_application_framework.htm)
- [Salesforce Metadata Coverage](https://developer.salesforce.com/docs/metadata-coverage)