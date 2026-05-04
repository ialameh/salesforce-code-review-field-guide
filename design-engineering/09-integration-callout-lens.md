# DE9. Integration and Callout Lens

The Integration and Callout lens looks for problems in outbound and inbound integrations: HTTP callouts, Named Credentials, token handling, retry logic, and the handling of failures when Salesforce communicates with external systems.

## What this lens catches

- Hardcoded endpoint URLs instead of Named Credentials
- Hardcoded API keys or tokens in source code
- HTTP callouts without timeout configuration (defaults to 10 seconds, which may be too long or too short)
- HTTP callouts without retry logic (immediate failure on timeout or 5xx response)
- No idempotency key on outbound calls (retry causes duplicate external actions)
- Request payloads that include fields the user does not have FLS access to
- Response validation that does not check HTTP status code (200 OK assumed)
- Large response payloads loaded into memory without size limits
- `HttpRequest` body built with string concatenation instead of JSON.serialize
- Named Credential auth that is not tested in the sandbox (auth can break silently on credential rotation)
- Remote Site Settings not configured for the target endpoint
- Callout limit (100 per transaction) not accounted for in bulk scenarios

## Named Credentials requirement

All production callouts must use Named Credentials. Hardcoded URLs and credentials in source code are a **Critical** security finding.

Named Credentials provide:
- Centralized credential storage
- Automatic token refresh for OAuth2
- Protection against credential exposure in logs
- Per-environment configuration (sandbox vs production)

```apex
// CORRECT: Named Credential reference
HttpRequest req = new HttpRequest();
req.setEndpoint('callout:MyNamedCredential/path/to/endpoint');
req.setMethod('GET');
// No username, no password, no URL hardcoded
```

```apex
// VIOLATION: Hardcoded endpoint
req.setEndpoint('https://api.example.com/path/to/endpoint');
req.setHeader('Authorization', 'Bearer sk_live_abcdef123456');
// Credentials visible in source, in logs, in deployment artifacts
```

## Required questions

- Can this callout safely run twice? (Idempotency)
- What happens if the external system succeeds but the Salesforce transaction fails? (Two-phase commit problem)
- What happens if Salesforce commits but the external callout fails?
- Are payloads stripped of FLS-protected fields before leaving Salesforce?
- Is there a circuit breaker or timeout that prevents a slow external system from blocking the Salesforce transaction?

## The two-phase commit problem

Salesforce and the external system cannot both commit atomically. If the external callout succeeds and Salesforce then fails to commit the transaction, the external action has already happened with no rollback on the external side.

This must be explicitly designed around. Options:
- Use a Platform Event to decouple the callout from the transaction
- Use a Queueable with its own transaction boundary
- Accept eventual consistency and log for reconciliation

Finding that this pattern is not considered is a **High** finding.

## What this chapter covered

- Integration failure patterns this lens detects
- Named Credentials as the required pattern for callouts
- Required questions for every integration
- The two-phase commit problem and why it must be addressed

## References

- [Named Credentials](https://developer.salesforce.com/docs/atlas.en-us.apexcode.meta/apexcode/apex_named_credentials.htm)
- [HTTP Callouts](https://developer.salesforce.com/docs/atlas.en-us.apexcode.meta/apexcode/apex_callouts.htm)
- [Integration Best Practices](https://developer.salesforce.com/docs/atlas.en-us.apexcode.meta/apexcode/apex_integration.htm)