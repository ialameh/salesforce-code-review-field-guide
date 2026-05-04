# R1. Final Report Format

The final report is the output of the 9-stage review pipeline. It has 12 sections. All 12 are required. Skipping a section is acceptable only if the section is not applicable, and that must be stated explicitly.

## The 12 sections

```md
## 1. Architecture Summary

One paragraph explaining the runtime flow. Entry point to final state.

## 2. Inventory Summary

| Type | Count | Notes |
|---|---:|---|
```

## 3. Overall Verdict

```md
Strong:
Material gaps:
Production readiness:
```

Be direct. Production readiness is one of: Production ready | Production ready after minor fixes | Production ready after high-priority fixes | Risky for production | Not deployable.

## 4. Findings by Lens

For each lens with findings:

```md
### [Lens Name]

| # | Sev | Location | Finding | Recommended Fix |
|---|---|---|---|---|
```

For each finding without a lens assignment, put it in an **Uncategorized** table.

## 5. Top 10 Prioritized Fixes

```md
| Rank | Severity | Item | Effort | Impact |
|---|---|---|---|---|
```

Effort: S (small, less than half a day), M (moderate, design or tests needed), L (larger refactor).

## 6. Class-by-Class Verdict

```md
| Class | Verdict |
|---|---|
```

Short but meaningful. Not just "OK" or "Needs work." Describe what you found.

## 7. Test Quality Review

Include:
- Strongest tests
- Weakest tests
- Fake assertions
- Missing negative tests
- Missing bulk tests
- Missing security tests
- `seeAllData=true` risks
- CI instability risks

## 8. Security Review Readiness

One of: Likely pass | Likely pass after fixes | Risky | Likely fail.

Explain why.

## 9. Failure Simulation

```md
| Scenario | Risk | Fix |
|---|---|---|
```

## 10. Metadata vs Code Matrix

```md
| Config / Metadata | Expected Runtime Behaviour | Verified? | Risk |
|---|---|---|---|
```

## 11. Architectural Insights

Deepest observations. Examples:
- "The adapter seam is the right choice because managed-package callout classes are hard to mock directly."
- "The metadata-driven dispatcher is powerful, but only safe if deploy-time validation verifies every configured handler."
- "The test suite has breadth but not depth because assertions prove execution, not behaviour."

## 12. Final Recommendation

One of:
- Production ready
- Production ready after minor fixes
- Production ready after high-priority fixes
- Risky for production
- Not deployable

Explain in 3 to 6 sentences.

## 13. Reviewer Self-Check

```md
**Surprised by:** [What caught me off guard]

**Skipped or treated lightly:** [Lenses I did not apply fully]

**Would say differently face-to-face:** [What I softened or omitted]

**Do not fully understand:** [Parts of the system I read but did not grasp]

**Style issues treated as production risks:** [Yes/No, with explanation]

**Reluctant to deploy:** [Yes/No, with explanation]
```

## Output format rules

- Findings must be specific: name the file, class, method, line.
- Severity labels must be consistent: Critical, High, Medium, Low, Strength.
- Certainty labels must be present: Confirmed, Likely, Possible, Cannot Verify.
- No vague language: write "this fails" not "this may fail."
- No suggestions without fixes: every finding must have a concrete recommended fix.

## What this chapter covered

- The required 12-section structure for every final report
- What each section must contain
- Output format rules for findings, verdicts, and recommendations

## References

- [Apex Enterprise Patterns](https://developer.salesforce.com/docs/atlas.en-us.apexcode.meta/apexcode/apex_application_framework.htm)
- [Security Review Requirements](https://developer.salesforce.com/docs/atlas.en-us.securityGuide.meta/securityguide/sec_code_package_security_review.htm)