# O4. Cross-Lens Contradiction Check

The hardest part of a multi-lens review is not finding problems. It is reconciling findings where two lenses disagree or where one lens sees a risk that another lens has rated as low.

This stage exists to force those contradictions into the open rather than quietly resolving them in favor of the majority finding.

## Why contradictions happen

Every lens looks at the code from a different angle with different values. The Architecture lens may praise a metadata-driven dispatcher as clean and extensible. The Security lens may flag the same dispatcher as unsafe because it loads handler classes by string name without validation. Both lenses are reading the same code correctly. The tension is real.

Resolving a contradiction does not mean picking one lens and discarding the other. It means acknowledging the tension and describing the trade-off explicitly so the reader can make an informed decision.

## Types of contradictions

**Risk level contradiction:** One lens says High, another says Low for the same code pattern.

Example: A SOQL query inside a loop is called High by the Governor Limits lens but Low by the Code Quality lens (because the method is small and readable). The Governor Limits lens is correct. The Risk level contradiction resolves in favor of High.

**Pattern contradiction:** One lens sees a pattern as a strength, another sees it as a risk.

Example: Dynamic SOQL is used to build runtime queries based on Custom Metadata. The Architecture lens calls it flexible and configurable. The Security lens flags it as a potential SOQL injection risk without sanitization. The Security lens is also correct. The resolution: the dynamic SOQL is safe if and only if the input values are validated against a strict allowlist, and this must be verified and documented.

**Severity inflation:** Multiple lenses find minor versions of the same issue and each rates it Medium, making it look like a major problem when it is actually a collection of small issues.

Example: Three different lenses flag three different naming inconsistencies in three different classes. Each is Low. The report should not present this as a Medium finding. The Cross-Lens check should collapse these into a single Low finding with three instances.

## How to resolve contradictions

For each contradiction you find, write a resolution entry:

```md
## Cross-Lens Contradictions

| Tension | Resolution | Decision |
|---|---|---|
| Architecture praises metadata dispatcher. Security flags dynamic class loading. | The dispatcher is safe if handler class names are validated against a hardcoded allowlist before instantiation. If no validation exists, the risk is High and the pattern should be replaced with a static factory. | Reject pattern if validation is absent. Accept pattern with documentation if validation is confirmed. |
| Governor Limits flags SOQL in loop as High. Code Quality calls it Low because the loop is short. | The loop is short today. It will grow. The governor limit applies regardless of loop length. Severity is High. | Keep severity High. Add note that loop length is not a mitigating factor. |
```

## The contradiction check in the final report

The final report must include a Cross-Lens Contradictions section. This section is not optional padding. It is where the review earns trust from senior readers who will immediately see if you suppressed a finding or inflated a severity for political reasons.

If there are no contradictions, state that explicitly: "No significant cross-lens contradictions were identified."

## What this chapter covered

- Why lens contradictions happen and are expected
- The three types of contradictions (risk level, pattern, severity inflation)
- How to write a resolution entry that acknowledges both lenses
- Why the contradictions section belongs in every final report

## References

- [Salesforce Security Review Guide](https://developer.salesforce.com/docs/atlas.en-us.securityGuide.meta/securityguide/sec_code_package_security_review.htm)
- [Apex Governor Limits](https://developer.salesforce.com/docs/atlas.en-us.apexcode.meta/apexcode/apex_limits.htm)