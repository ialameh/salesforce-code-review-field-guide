# DE7. Metadata-Driven Configuration Lens

The Metadata-Driven Configuration lens looks for gaps between what Custom Metadata says and what the code actually does. Configuration-driven code is powerful because it allows behavior changes without code deployment. It is risky because a misconfigured metadata record can silently change behavior in ways that are hard to debug.

## What this lens catches

- Custom Metadata fields referenced in code but not defined in the metadata type
- Custom Metadata fields defined but not used by any code (dead config)
- Custom Metadata fields used by code but set to an invalid value at runtime
- Handler class names stored in metadata that do not exist as Apex classes
- Configuration records that should be active but are not
- Multiple active configuration records where only one should be active
- Org-specific values (record IDs, endpoint URLs) stored in Custom Metadata and deployed across orgs
- Missing default values for required Custom Metadata fields
- Configuration that changes behavior but has no test coverage

## Metadata inspection matrix

For every Custom Metadata Type used in the codebase, build this matrix:

```md
| Metadata Field | Used By (Class.method) | Expected Behaviour | Verified? | Risk |
|---|---|---|---|---|
| Config__mdt.RunAsPermission__c | ConfigController.runNow() | Requires custom permission to execute | Yes | None |
| Config__mdt.BatchSize__c | BatchProcessor.execute() | Controls scope size per batch | No | Mismatch between config and code |
```

## Common metadata mismatch patterns

**Field type mismatch.** The code expects a `Boolean` field but the metadata is a `Text` field. The code does a string comparison instead of a boolean check. This works but is fragile.

**Missing validation.** The code reads a handler class name from metadata and instantiates it via `Type.forName()`. There is no validation that the handler class exists before instantiation. A bad deploy could set a non-existent class name and cause a runtime error.

**Org-specific values in shared metadata.** A Custom Metadata record is promoted from a sandbox with a Production-ready ID. The ID is sandbox-specific and breaks in production. This is a common deployment failure.

**Inactive records still referenced.** A configuration record is deactivated but the code still reads it because there is no active-check guard. This is a silent behavior change.

## Required questions

- What happens if the Custom Metadata record does not exist at runtime?
- What happens if a required field is null?
- What happens if an invalid handler class name is stored in the metadata?
- How is the metadata validated at deploy time?
- Is there a test that covers each metadata configuration scenario?

## What this chapter covered

- The metadata mismatch patterns this lens detects
- The metadata inspection matrix format
- Common metadata configuration failures
- Required questions for metadata-driven code

## References

- [Custom Metadata Types](https://developer.salesforce.com/docs/atlas.en-us.apexcode.meta/apexcode/apex_custommetadata_types.htm)
- [Custom Metadata Relationships](https://developer.salesforce.com/docs/atlas.en-us.apexcode.meta/apexcode/apex_custom_metadata_relationships.htm)
- [Apex Metadata API](https://developer.salesforce.com/docs/atlas.en-us.apexcode.meta/apexcode/apex_metadata_api.htm)