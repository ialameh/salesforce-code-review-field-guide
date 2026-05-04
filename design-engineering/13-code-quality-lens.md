# DE13. Code Quality Lens

The Code Quality lens looks for maintainability problems: dead code, magic numbers, public/global overexposure, inconsistent patterns, poor naming, and weak exception handling. This lens is the one most likely to produce Low-severity findings. It is also the one most likely to be over-applied and inflated to Medium or High.

The rule for this lens: do not over-prioritize style issues. Style is low severity unless it hides a real defect.

## What this lens catches

- Dead code (methods never called, variables never used)
- Magic numbers (hardcoded integers without a named constant)
- Hardcoded strings used multiple times without a constant
- Public or global classes that should be private or inner class
- Methods over 100 lines that could be split
- Inconsistent naming patterns within the same class
- Comments that contradict the code
- `Exception` used where a more specific type is available
- No-op `catch` blocks (swallowed exceptions)
- Unused method parameters
- Duplicate logic across classes that could be extracted to a shared utility

## The dead code check

For every class, check:

1. Are there methods that are never called from within the class or from any test?
2. Are there variables that are declared but never used?
3. Are there constants that are defined but never referenced?

Use your editor or `grep` to find references. A method with zero references and no test is either dead code or a planned-but-never-implemented feature. Either way, it should be flagged.

## Magic number inspection

```apex
// VIOLATION: Magic number
if (accountList.size() > 200) {
    // ... do something
}
```

```apex
// CORRECT: Named constant
private static final Integer MAX_ACCOUNTS_PER_BATCH = 200;
if (accountList.size() > MAX_ACCOUNTS_PER_BATCH) {
    // ...
}
```

Magic numbers are a Low finding unless the magic number is a governor limit threshold, in which case it may surface a governor limit issue (apply Governor Limits lens).

## Severity rules

Dead code in an active class is **Low**. Dead code that is called by other code is not dead code, it may be a missing abstraction.

A public class that should be private is **Medium** only if it creates a risk of unintended usage. If it is truly internal and not exposed, it should be private.

Inconsistent naming within a class is **Low**.

A method over 100 lines with no clear single responsibility is **Medium**.

## What this chapter covered

- Maintainability patterns this lens detects
- The dead code inspection process
- Magic number identification
- Severity rules that prevent style inflation

## References

- [Apex Coding Conventions](https://developer.salesforce.com/docs/atlas.en-us.apexcode.meta/apexcode/apex_coding_conventions.htm)
- [Apex Best Practices](https://developer.salesforce.com/docs/atlas.en-us.apexcode.meta/apexcode/apex_bestpractices.htm)