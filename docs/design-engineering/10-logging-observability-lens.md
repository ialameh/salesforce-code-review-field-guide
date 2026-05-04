# DE10. Logging and Observability Lens

The Logging and Observability lens looks for code that makes production failures hard to diagnose. When something breaks at 2am, the only thing that matters is whether the logs tell you what happened, to which record, for which user, in which transaction.

## What this lens catches

- Swallowed exceptions (`catch (Exception e) {}` or `catch (Exception e) { return null; }`)
- `System.debug` in production code paths (not in test or debug-only sections)
- `Test.isRunningTest()` in production code (should not gate production behavior)
- Missing correlation IDs on async jobs (cannot trace a job across systems)
- Missing user ID and record ID in log context
- Log statements that contain sensitive data (PII, passwords, tokens)
- `System.debug(JSON.serialize(records))` in production paths (heap bloat)
- Platform Event publish without a corresponding subscriber log
- Async jobs that fail silently with no error record created
- No monitoring hook for critical business metrics

## The exception handling inspection

For every `try-catch` block in the codebase, check:

```text
catch (Exception e) {
    // [What happens here?]
    // Does it log? Does it rethrow? Does it silently swallow?
}
```

```apex
// VIOLATION: Silent swallow
catch (Exception e) {
    // ignore
}
```

```apex
// VIOLATION: Silent return
catch (Exception e) {
    return null;
}
```

```apex
// CORRECT: Structured logging
catch (Exception e) {
    LogHandler.logError(
        'ClassName.methodName',
        UserInfo.getUserId(),
        recordId,
        e.getMessage(),
        e.getStackTraceString()
    );
    throw new CustomException('Failed to process record: ' + recordId);
}
```

## What to log for every operation

Every operation that changes data or calls an external system should log:

| Element | Why it matters |
|---|---|
| Timestamp | Reconstruct when it happened |
| User ID | Determine who was driving |
| Record ID | Identify what was affected |
| Correlation ID | Trace across async boundaries |
| Operation name | Identify what was attempted |
| Before/after state (sanitized) | Understand the change |
| Error message and stack trace | Diagnose the failure |
| External correlation ID | Trace in the external system |

## Required questions

- When this code fails in production, can you determine what happened and for whom?
- Is there a log entry for every async job enqueue and completion?
- Are exceptions rethrown or logged before being swallowed?
- Is sensitive data (PII, credentials) ever written to logs?
- Is there a monitoring hook (Platform Event, custom log object) for critical operations?

## What this chapter covered

- Logging patterns that make failures hard to diagnose
- The exception handling inspection format
- The minimum log entry elements for production diagnostics
- Required questions for observability assessment

## References

- [Apex Logging Best Practices](https://developer.salesforce.com/docs/atlas.en-us.apexcode.meta/apexcode/apex_debugging.htm)
- [Platform Events Monitoring](https://developer.salesforce.com/docs/atlas.en-us.apexcode.meta/apexcode/apex_platform_events_best_practices.htm)