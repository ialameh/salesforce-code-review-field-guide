# Glossary

**AuraEnabled** — An Apex method annotation that exposes the method to Lightning component controllers (both LWC and Aura). Mutation methods must not be cacheable.

**Bulkification** — Writing Apex code that processes lists of records efficiently, not assuming single-record processing. Required for triggers and batch classes.

**CRUD** — Create, Read, Update, Delete. The four permissions on an sObject field. FLS is field-level CRUD enforcement.

**Custom Metadata** — A Salesforce metadata type that stores configuration data in the metadata layer, allowing behavior changes without code deployment.

**DML** — Data Manipulation Language. INSERT, UPDATE, UPSERT, DELETE operations in Salesforce. Each DML statement counts against the 150-DML governor limit.

**FLS** — Field Level Security. A Salesforce security model that controls access to individual fields on an sObject based on the user's profile.

**Governor Limit** — A Salesforce runtime limit on resources (SOQL queries, DML statements, heap, CPU time) that applies per transaction.

**LDV** — Large Data Volume. Typically refers to sObjects with more than 1 million records where query performance can degrade.

**LWC** — Lightning Web Component. The modern Salesforce frontend framework. LWC components run in the browser and call Apex controllers via wire adapters or imperative calls.

**Named Credential** — A Salesforce metadata type that stores an endpoint URL and authentication configuration for outbound callouts. Required for all production callouts.

**Queueable** — An Apex interface for asynchronous execution that allows chaining and provides an ID for tracking.

**SOQL** — Salesforce Object Query Language. The query language for Salesforce records. Each SOQL query counts against the 100-query governor limit.

**With Sharing** — An Apex class declaration that enforces record sharing rules and field-level security based on the user's access.

**Without Sharing** — An Apex class declaration that runs in system context, ignoring sharing rules. Use only when necessary and documented.

**Inherited Sharing** — An Apex class declaration that defers sharing enforcement to the calling context.

**WITH USER_MODE** — A SOQL clause that enforces field-level security on the returned records without requiring explicit FLS checks in code.

**Security.stripInaccessible** — An Apex method that removes fields from records that the current user cannot access, used to enforce FLS on DML operations.

**Platform Event** — A Salesforce event bus publishing mechanism for decoupled, async communication between systems.

**Selector Pattern** — An Apex enterprise pattern that centralizes SOQL queries in dedicated selector classes, separating data access from business logic.

**Service Layer Pattern** — An Apex enterprise pattern that centralizes business logic in service classes, keeping controllers and triggers thin.

**Trigger Framework** — A pattern that keeps trigger logic in handler classes rather than directly in the trigger, enabling testability and preventing mixed logic.

---

*All terms are defined in the context of Salesforce Apex development and code review. For general Salesforce terminology, refer to the [Salesforce glossary](https://developer.salesforce.com/docs/atlas.en-us.salesforce_pubs_collab.current/salesforce_pubs_collab_a19).*