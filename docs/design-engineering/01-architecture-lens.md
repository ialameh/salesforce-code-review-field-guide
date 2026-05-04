# DE1. Architecture Lens

The Architecture lens looks for structural problems: classes that do too much, responsibilities that are not separated, boundaries that are not enforced, and coupling that makes the system hard to change safely.

## What this lens catches

- God classes (classes over 400 lines that do many things)
- Controllers doing business logic
- Triggers doing business logic instead of delegating to handlers
- Services that are actually domain logic mixed with data access
- Selectors used where direct SOQL would be simpler
- Missing abstraction where it is needed
- Overengineering where simple code would suffice
- Circular dependencies between classes
- Static state shared across contexts
- Inner classes used as primary organization structure

## What good architecture looks like

A well-structured Salesforce codebase has clear boundaries:

- **Entry point** (controller, REST, trigger) receives the request and does minimal logic
- **Service layer** contains business logic, no SOQL, no DML
- **Selector layer** handles all queries, returns sObject lists
- **Domain layer** encapsulates record-specific behavior
- **Trigger handler** delegates to domain or service, does not contain business logic

If you can read the entry point and understand the flow without reading the service implementation, the architecture is clean.

## The inspection checklist

Check each class for:

1. **Single responsibility:** Does this class have one job? Can you describe what it does in one sentence?
2. **Abstraction level:** Does this class mix high-level business logic with low-level data access?
3. **Dependency direction:** Do high-level classes depend on low-level classes, or vice versa?
4. **Public surface:** Are public methods the minimum needed for external callers?
5. **Inner class usage:** Are inner classes doing real work, or are they just organizational?

## Required questions

- Can a new developer understand this class in 5 minutes?
- Is the class doing something a platform feature (flow, declarative tool) could do?
- Is this class tested in isolation, or only as part of a larger integration test?
- If this class were removed, would the system break in a way that is immediately obvious?

## Severity rules

A class that is over 400 lines and has more than 5 public methods is a **High** finding regardless of what it does. Large classes hide governor limit issues, security issues, and bulkification problems.

A class that mixes business logic with SOQL is a **Medium** finding. It works today but cannot be tested independently.

A trigger with more than 50 lines of logic is a **High** finding. Business logic in triggers is a maintenance hazard.

## What this chapter covered

- The structural problems the Architecture lens detects
- What good Salesforce architecture looks like at each layer
- The inspection checklist
- Required questions to ask about each class
- Severity rules for architecture findings

## References

- [Apex Enterprise Patterns](https://developer.salesforce.com/docs/atlas.en-us.apexcode.meta/apexcode/apex_application_framework.htm)
- [Apex Trigger Frameworks](https://developer.salesforce.com/docs/atlas.en-us.apexcode.meta/apexcode/apex_triggers.htm)
- [FFLIB Application Framework](https://github.com/apex-enterprise-patterns/fflib-apex-common)