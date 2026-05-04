# PM2. Review Scope Decisions

Every review engagement starts with a scope decision. Scope determines what gets reviewed and what does not. A poorly scoped review wastes time on the wrong things and leaves the real risks uncovered.

## Scope axes

A review scope can be defined along four axes:

**Codebase axis:** Full codebase versus selected components. Full codebase reviews are comprehensive but take days. Selected component reviews are faster but require the requester to know which components are risky.

**Depth axis:** Triage (20-minute pass) versus full 13-lens review versus deep-dive on one lens. Triage finds the obvious problems. Full review finds the subtle ones. Deep-dive is appropriate when there is a known problem area.

**Purpose axis:** Pre-deployment review, security review, LDV assessment, CI/CD readiness, or post-incident investigation. Purpose determines which lenses get priority.

**Audience axis:** Internal team review, external audit, security review submission, or training exercise. External audiences require more formal output formatting.

## Scoping questions to answer before starting

1. What is the deliverable? A verbal report, a written report, or a formal document?
2. What is the deadline? Pre-deployment reviews have hard deadlines.
3. What is in scope? (Class list or directory) What is explicitly out of scope?
4. Who is the audience for the report? Technical leads, architects, or executives?
5. Is there a prior review or known problem area? (Focus the lenses there)
6. Are there test classes for all production classes? (If not, test quality lens needs to flag this early)
7. Are there any org-specific constraints? (Custom permissions, LDAP integration, naming conventions)

## Scope document template

```md
## Review Scope

**Purpose:** [Pre-deployment / Security Review / LDV Assessment / CI/CD Readiness / Post-incident]
**Audience:** [Technical leads / Architects / Executives / External auditor]
**Deadline:** [Date and time]

**In Scope:**
- [File, directory, or component list]
- [Include test classes: Yes/No]
- [Include LWC: Yes/No]
- [Include Flows: Yes/No]
- [Include Custom Metadata: Yes/No]

**Out of Scope:**
- [Explicitly excluded components]

**Priority Lenses:**
1. [Most important lens for this review]
2. [Second most important]
3. [Third most important]

**Deliverable Format:** [Verbal / Written brief / Formal 12-section report]
```

## When to refuse or reduce scope

A scope that is too large for the time available produces a superficial review that misses real problems. If you are given a 2-day window to review a 50-class codebase with 13 lenses, do not accept that scope. Either reduce the scope to the highest-risk components or extend the time.

A triage of the full codebase in 2 days is more valuable than a partial 13-lens review that covers half the classes.

## What this chapter covered

- The four axes of review scope
- Scoping questions to answer before starting
- Scope document template
- When to reduce scope rather than accept an unrealistic timeline

## References

- [Apex Enterprise Patterns](https://developer.salesforce.com/docs/atlas.en-us.apexcode.meta/apexcode/apex_application_framework.htm)
- [Salesforce Security Review Guide](https://developer.salesforce.com/docs/atlas.en-us.securityGuide.meta/securityguide/sec_code_package_security_review.htm)