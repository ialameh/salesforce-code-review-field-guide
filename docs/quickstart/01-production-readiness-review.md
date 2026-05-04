# Q1. 20-Minute Production Readiness Review

You have 20 minutes. A codebase is in front of you. You need to answer one question before the deadline: is this safe to deploy?

This chapter gives you a structured 20-minute pass that surfaces the highest-impact issues first. It is not a full review. It is a triage exercise. It tells you where to look deeper, not whether the code is perfect.

## The 20-minute clock

Start the clock when you open the first file. Divide your time by priority:

| Time block | Task |
|---|---|
| 0 to 5 min | Intake: list the classes, triggers, LWCs |
| 5 to 12 min | System map: trace entry points to data writes |
| 12 to 17 min | Governor limits sweep: look for loops with queries or DML |
| 17 to 20 min | Top findings and severity rating |

If you find a Critical issue before the clock expires, stop the clock. You have your answer. This is not safe to deploy.

## Stage 1: Intake (5 minutes)

List what you are reviewing. Do not try to understand it yet. Count the pieces.

Run this in your head or on paper:

```text
Apex classes: ___ (count .cls files)
Triggers: ___ (count .trigger files)
Test classes: ___ (count Test.cls or .cls with @IsTest)
LWCs: ___ (count .js files in lwc/)
Batch/Queueable: ___ (identify by class type keyword)
```

Mark the ones that look large (over 200 lines) with an asterisk. These are your risk multipliers. Large classes hide more problems.

Mark the ones with `@AuraEnabled`, `@RestResource`, `Future`, `Queueable`, `Batchable` annotations. These are your entry points. Entry points are where external callers reach the code. They are the first gate and the first risk.

## Stage 2: System Map (7 minutes)

Pick the three most important classes. For each, trace the path:

```text
Entry point → business logic → data access → async/callout → result
```

Ask yourself these questions for each class:

- Where does data enter? (AuraEnabled method, REST endpoint, trigger, schedulable)
- Where does it go after the entry point? (Service call, utility call, domain logic)
- Where does it write data? (DML statement, metadata write, platform event publish)
- Does it enqueue async work? (Queueable, batch, future)
- Does it make a callout? (HTTP request, named credential call)

If you find a path that goes: AuraEnabled method → loop → DML statement → enqueue Queueable, you have found a high-risk pattern. You do not need to measure it yet. You need to flag it.

Mark every path that has a loop with a query or DML inside it. These are your governor limit candidates.

## Stage 3: Governor Limits Sweep (5 minutes)

Look specifically for:

```apex
for (SomeObject__c record : triggerNew) {
    List<Related__c> related = [SELECT Id FROM Related__c WHERE ParentId__c = :record.Id];
    insert record;
    // any of these patterns inside a for loop is a red flag
}
```

The patterns to flag:

1. Any SOQL query inside a `for` loop
2. Any DML statement inside a `for` loop
3. Any `HttpRequest` call inside a `for` loop
4. Any `Database.insert` with `allOrNothing = true` inside a loop
5. Any `continue` or `skip` inside a loop that could cause partial processing

For each flag, note the class name, method name, and line count. You do not need to fix it in the 20-minute window. You need to record it.

## Stage 4: Top Findings and Severity (3 minutes)

Review your flags. Sort them by severity:

- **Critical**: Data corruption, security bypass, governor limit almost certain at normal volume
- **High**: Missing authorization, likely defect, async instability
- **Medium**: LDV risk, edge-case failure, operational complexity
- **Low**: Naming, dead code, style

Write down the top three findings using this format:

```text
1. [SEVERITY] [ClassName.methodName] — [what you found] — [what breaks]
2. [SEVERITY] [ClassName.methodName] — [what you found] — [what breaks]
3. [SEVERITY] [ClassName.methodName] — [what you found] — [what breaks]
```

If you have no Critical or High findings, the code is likely deployable with a note to do a full review before the next release.

If you have one or more Critical findings, the answer is clear. This is not ready.

## What to do next

Run the full 9-stage review pipeline from Chapter O1 through O5 on any codebase that passed the triage but scored High or Medium on three or more findings.

The 20-minute review is not a substitute for the full pipeline. It is a filter.

## What this chapter covered

- How to triage a codebase in 20 minutes
- How to build a quick system map and find high-risk paths
- How to flag governor limit issues without running code
- How to produce a triage verdict with three ranked findings

## References

- [Salesforce Governor Limits](https://developer.salesforce.com/docs/atlas.en-us.apexcode.meta/apexcode/apex_limits.htm) — Official limits reference
- [Apex Governor Limits Best Practices](https://developer.salesforce.com/docs/atlas.en-us.apexcode.meta/apexcode/apex_bestpractices governor_limits.htm) — Developer guide on avoiding limit failures