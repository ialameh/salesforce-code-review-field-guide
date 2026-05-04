# F1. The Multi-Lens Framework

A single reviewer looking at everything sees everything with equal weight. That is the wrong distribution. Security bugs look different from governor limit bugs. Async failures look different from SOQL performance problems. A single lens is not powerful enough to catch all of them at once.

The multi-lens framework solves this by giving you 13 specialist perspectives. Each lens is tuned to a specific failure mode. Running all 13 lenses against the same codebase produces findings that no single reviewer can match.

## How lenses work

A lens is not a checklist. It is a mental model built from patterns that have caused production failures before. When you apply the Security lens, you are not checking a list of rules. You are asking: where could this system fail to enforce who can do what? When you apply the Governor Limits lens, you are asking: where could this system run into a limit wall under normal or elevated load?

Each lens has:

- A specific mission (what it is looking for)
- A minimum evidence standard (what a finding must contain)
- A certainty model (how confident the finding is)
- A severity scale (what the finding means for production)

## The 13 lenses in order

| # | Lens | Primary failure mode |
|---|---|---|
| 1 | Architecture | Mixed responsibilities, tight coupling, god classes |
| 2 | Governor Limits | Limit failures under normal or elevated load |
| 3 | SOQL and LDV | Full-table scans, non-selective queries, LDV failures |
| 4 | Security and Authorization | Unauthorized mutations, data leakage, FLS bypass |
| 5 | Bulkification | Single-record assumptions, per-record queries/DML |
| 6 | Async and Transaction | Queueable failures, retry gaps, race conditions |
| 7 | Metadata-Driven Configuration | Config-code mismatches, missing handlers |
| 8 | LWC and Frontend Contract | Wire misuse, client-only auth, reactivity bugs |
| 9 | Integration and Callout | Hardcoded endpoints, missing retry, token exposure |
| 10 | Logging and Observability | Undiagnosable failures, swallowed exceptions |
| 11 | Test Quality | Coverage bait, fake assertions, missing coverage |
| 12 | CI/CD and Packaging | Deployment failures, org-specific references |
| 13 | Code Quality | Dead code, magic numbers, naming issues |

## Why 13 lenses

Every one of these lenses has a track record of catching problems that reached production. No lens is decorative. Each represents a known failure class in Salesforce implementations:

- Lenses 1 through 3 catch architecture and data-layer problems.
- Lenses 4 and 5 catch permission and bulk-handling problems.
- Lenses 6 and 7 catch async and configuration problems.
- Lenses 8 and 9 catch frontend and integration problems.
- Lenses 10 and 11 catch observability and testing problems.
- Lenses 12 and 13 catch deployment and maintainability problems.

Skipping any lens means accepting the risk of missing that failure class. Skipping the Security lens means accepting the risk of shipping unauthorized mutations. Skipping the Test Quality lens means accepting the risk of shipping tests that pass but prove nothing.

## How to run lenses in practice

Run lenses in parallel. Do not run them one at a time sequentially. When you finish the intake and system map, open all 13 lens checklists and work through the codebase once, making notes under each lens as you encounter evidence.

A single pass through the codebase applying all 13 lenses is faster and more consistent than 13 sequential passes, because the context stays fresh.

For each finding:

1. Name the file, class, and method
2. Copy the relevant code snippet
3. Assign a certainty: Confirmed, Likely, Possible, Cannot Verify
4. Assign a severity: Critical, High, Medium, Low
5. Describe what breaks and under what conditions

## When lenses contradict each other

This is the hardest part of multi-lens review. Sometimes one lens flags something that another lens says is fine.

Example: The Architecture lens says the metadata-driven dispatcher is clean. The Security lens says the dynamic handler class loading is unsafe without validation.

When this happens, do not pick one lens and discard the other. The contradiction is real. Both perspectives are valid. The resolution goes in the final report under the Cross-Lens Contradiction section. The resolution describes the tension, why both lenses are correct from their perspective, and what the recommended path forward is.

A good multi-lens review holds tensions rather than resolving them prematurely.

## What this chapter covered

- Why 13 lenses are necessary and not excessive
- The mission and evidence standard for each lens
- How to run all 13 lenses in a single pass
- How to handle lens contradictions

## References

- [Salesforce Security Review Requirements](https://developer.salesforce.com/docs/atlas.en-us.securityGuide.meta/securityguide/sec_code_package_security_review.htm)
- [Apex Governor Limits Reference](https://developer.salesforce.com/docs/atlas.en-us.apexcode.meta/apexcode/apex_limits.htm)