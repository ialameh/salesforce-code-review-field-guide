# References

Canonical sources organized by topic. All claims in this field guide are backed by a source in this file.

## Apex and Governor Limits

- [Salesforce Governor Limits](https://developer.salesforce.com/docs/atlas.en-us.apexcode.meta/apexcode/apex_limits.htm) — Official limits reference for all Apex governor limits
- [Understanding Execution Governors](https://developer.salesforce.com/docs/atlas.en-us.apexcode.meta/apexcode/apex governors_execution.htm) — How governor limits are enforced at runtime
- [Apex Governor Limits Best Practices](https://developer.salesforce.com/docs/atlas.en-us.apexcode.meta/apexcode/apex_bestpractices governor_limits.htm) — Official best practices for avoiding limit failures

## Security and Authorization

- [Apex Security Best Practices](https://developer.salesforce.com/docs/atlas.en-us.apexcode.meta/apexcode/apex_security_best_practices.htm) — CRUD/FLS enforcement, sharing model, secure coding
- [Secure Coding Techniques](https://developer.salesforce.com/docs/atlas.en-us.apexcode.meta/apexcode/apex_secure_coding.htm) — SOQL injection prevention, XSS prevention, CSRF mitigation
- [Salesforce Security Review Requirements](https://developer.salesforce.com/docs/atlas.en-us.securityGuide.meta/securityguide/sec_code_package_security_review.htm) — Official security review requirements for Security Review submission
- [User Mode for SOQL Queries](https://developer.salesforce.com/docs/atlas.en-us.apexcode.meta/apexcode/apex_system_schema_database.htm) — WITH USER_MODE and Security.stripInaccessible documentation

## Architecture and Patterns

- [Apex Enterprise Patterns](https://developer.salesforce.com/docs/atlas.en-us.apexcode.meta/apexcode/apex_application_framework.htm) — Selector, Service, Domain, and Application frameworks
- [FFLIB Application Framework](https://github.com/apex-enterprise-patterns/fflib-apex-common) — Open-source implementation of Apex enterprise patterns
- [Apex Trigger Frameworks](https://developer.salesforce.com/docs/atlas.en-us.apexcode.meta/apexcode/apex_triggers.htm) — Trigger handler patterns and execution order
- [Trigger Execution Order](https://developer.salesforce.com/docs/atlas.en-us.apexcode.meta/apexcode/apex_triggers_order.htm) — Before and after trigger execution order

## Bulkification and Performance

- [Apex Bulkification Best Practices](https://developer.salesforce.com/docs/atlas.en-us.apexcode.meta/apexcode/apex_bulkification.htm) — How to write bulk-safe Apex
- [SOQL Best Practices](https://developer.salesforce.com/docs/atlas.en-us.apexcode.meta/apexcode/apex_troubleshooting_soql.htm) — Query optimization, query selectivity, LDV handling
- [Query & Search Optimization Best Practices](https://developer.salesforce.com/docs/atlas.en-us.salesforce_large_data_volumes/BestPractice/Salesforce_PaginatedData_Retrieval_Through_Query_Optimizer.htm) — Large data volume query optimization

## Async Apex

- [Apex Queueable Interface](https://developer.salesforce.com/docs/atlas.en-us.apexcode.meta/apexcode/apex_queueable_interface.htm) — Queueable interface, chain depth, limits
- [Apex Batch Apex](https://developer.salesforce.com/docs/atlas.en-us.apexcode.meta/apexcode/apex_batch.htm) — Batchable interface, scope sizing, stateful batch
- [Platform Events Best Practices](https://developer.salesforce.com/docs/atlas.en-us.apexcode.meta/apexcode/apex_platform_events_best_practices.htm) — Event-driven architecture, publish/subscribe patterns

## Testing

- [Apex Testing Best Practices](https://developer.salesforce.com/docs/atlas.en-us.apexcode.meta/apexcode/apex_testing_best_practices.htm) — Test isolation, assertions, seeAllData risks
- [IsTest Annotations](https://developer.salesforce.com/docs/atlas.en-us.apexcode.meta/apexcode/apex_testing_istest.htm) — @IsTest, seeAllData, testSetup

## LWC and Frontend

- [LWC Security Best Practices](https://developer.salesforce.com/docs/atlas.en-us.lightning.meta/lightning/lwc_security.htm) — Client-side security, server-side enforcement requirement
- [Lightning Data Service](https://developer.salesforce.com/docs/atlas.en-us.lightning/meta/lightning/data_home.htm) — LDS for optimistic UI, record caching

## Integration

- [Named Credentials](https://developer.salesforce.com/docs/atlas.en-us.apexcode.meta/apexcode/apex_named_credentials.htm) — Named Credential configuration and usage
- [HTTP Callouts](https://developer.salesforce.com/docs/atlas.en-us.apexcode.meta/apexcode/apex_callouts.htm) — HttpRequest, HttpResponse, callout limits
- [Integration Best Practices](https://developer.salesforce.com/docs/atlas.en-us.apexcode.meta/apexcode/apex_integration.htm) — Patterns for external system integration

## CI/CD and Deployment

- [Salesforce Deployment Guide](https://developer.salesforce.com/docs/atlas.en-us.sfdx_dev.meta/sfdx_dev/sfdx_dev_develop_mdapi.htm) — Source deployment, package.xml structure
- [Unlocked Packages](https://developer.salesforce.com/docs/atlas.en-us.packagingDistLear.meta/packagingDistLear/packaging_intro.htm) — Unlocked package best practices
- [Salesforce CI/CD Best Practices](https://developer.salesforce.com/docs/atlas.en-us.sfdx_dev.meta/sfdx_dev/sfdx_dev_ci.htm) — CI/CD pipeline design for Salesforce

## Custom Metadata and Configuration

- [Custom Metadata Types](https://developer.salesforce.com/docs/atlas.en-us.apexcode.meta/apexcode/apex_custommetadata_types.htm) — Custom metadata type creation and usage
- [Custom Metadata Relationships](https://developer.salesforce.com/docs/atlas.en-us.apexcode.meta/apexcode/apex_custom_metadata_relationships.htm) — Relationship field types in custom metadata
- [Apex Metadata API](https://developer.salesforce.com/docs/atlas.en-us.apexcode.meta/apexcode/apex_metadata_api.htm) — Runtime metadata reading from Apex

## Documentation

- [Apex Coding Conventions](https://developer.salesforce.com/docs/atlas.en-us.apexcode.meta/apexcode/apex_coding_conventions.htm) — Naming conventions, comment style, code organization
- [Salesforce Large Data Volumes PDF](https://developer.salesforce.com/docs/atlas.en-us.salesforce_large_data_volumes/Salesforce_Large_Data_Volumes.pdf) — Official LDV documentation with thresholds and optimization guidance
- [Salesforce Developer Training](https://developer.salesforce.com/training) — Official training paths for Apex and Salesforce development