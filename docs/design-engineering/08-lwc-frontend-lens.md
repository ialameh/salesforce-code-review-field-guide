# DE8. LWC and Frontend Contract Lens

The LWC and Frontend Contract lens looks for problems in Lightning Web Components and the contract between the client-side component and its Apex controller. LWC problems are often invisible in testing because tests run in isolation without the full rendering lifecycle.

## What this lens catches

- `@wire` adapters used for imperative operations (should use `imperative @salesforce/apex`)
- `@AuraEnabled(cacheable=true)` on mutation methods (cacheable should mean read-only)
- Tracked object properties mutated directly instead of using helper methods
- `JSON.stringify()` on sObject or record references (causes circular reference errors)
- Missing error handling on Apex callout results
- Missing loading states during async Apex calls
- Client-side-only validation used as authorization (server must validate)
- `lds:record-ui` or `@wire(getRecord)` overfetching fields not used in the template
- Event bubbling with `composed: true` that crosses component boundaries unexpectedly
- `NavigationMixin.Navigate` called without checking user permissions
- Promise chains without `.catch()` handlers
- Debouncing missing on search inputs that trigger Apex calls
- LWR (Lightning Web Runtime) constraints not considered for Experience Cloud sites

## The cacheable mutation violation

This is the most common LWC security finding:

```javascript
// VIOLATION: AuraEnabled marked cacheable but performs mutation
@AuraEnabled(cacheable=true)
public static void updateAccount(Id accountId, String name) {
    Account acc = [SELECT Id FROM Account WHERE Id = :accountId];
    acc.Name = name;
    update acc; // MUTATION on a cacheable endpoint
}
```

Cacheable endpoints are cached by the Lightning platform. A mutation on a cacheable endpoint can succeed but return stale data to subsequent callers, or can be called by a cached version of the component in a different user context.

The fix: remove `cacheable=true` for any method that performs DML.

## Required questions

- Is every `@wire` call handling the error case?
- Is every mutation Apex call using a non-cacheable AuraEnabled method?
- Is the component using `@track` on objects that are mutated in place (which does not trigger reactivity)?
- Is there a loading state shown during every async operation?
- Is client-side validation supplemented by server-side validation?

## What this chapter covered

- LWC patterns that cause production failures
- The cacheable mutation violation and how to fix it
- Required questions for every LWC component
- Severity rules for LWC findings

## References

- [LWC Security Best Practices](https://developer.salesforce.com/docs/atlas.en-us.lightning.meta/lwc/lwc_security.htm)
- [Lightning Data Service](https://developer.salesforce.com/docs/atlas.en-us.lightning.meta/lightning/data_home.htm)
- [AuraEnabled Annotation](https://developer.salesforce.com/docs/atlas.en-us.apexcode.meta/apexcode/apex_aura_enabled.htm)