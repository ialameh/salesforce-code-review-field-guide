# PM1. Teaching Teams to Review Code

A code review culture does not emerge by declaring it. It emerges when every reviewer produces findings that developers respect and learn from, and when the review process is fast enough to run on every PR without becoming a bottleneck.

This chapter is for team leads and architects who want to establish a review practice, not just a review document.

## Building review skill in a team

The 13-lens framework is comprehensive. It is also too much for a team that is new to structured reviews. The practical on-ramp is to start with three lenses and add more as the team builds confidence.

**Month 1-2: Governor Limits + Security + Bulkification.**
These three lenses catch the issues that cause production failures. Running only these three on every PR takes about 20 minutes per review and catches the majority of serious defects. Train the team on these three first.

**Month 3-4: Add SOQL and LDV + Test Quality.**
With the first three lenses mastered, add query performance and test quality. These require more context to assess but add significant value for LDV-rich codebases.

**Month 5+: Add remaining lenses.**
Architecture, Async, Metadata, CI/CD, and Logging lenses require deeper context and are harder to apply without experience. Add them one at a time.

## The review meeting

A 30-minute review meeting is more effective than a written report alone, when the reviewer and the author are in the same room (or a video call).

Format:
1. Reviewer presents top 3 findings (10 minutes). Show the code. Explain the risk. Describe the fix.
2. Author responds and clarifies (10 minutes). The author may explain why the code is structured that way, which may adjust the finding.
3. Team discusses (10 minutes). Other team members may have seen similar patterns or know why a pattern exists.

The meeting is not for the reviewer to defend every finding. It is for the team to build a shared understanding of what good looks like.

## Reviewer calibration

Two reviewers looking at the same code should produce similar findings. If they do not, the team has a calibration problem.

Fix this by running periodic calibration sessions: both reviewers review the same code independently, then compare findings. Discrepancies reveal blind spots. This is how a team gets better.

## Making review findings educational

The worst outcome of a review is a list of problems with no path to learning. The best outcome is a developer who says: "I did not know that was a risk, I will write it differently next time."

Write findings that teach. The fix description should explain why the pattern is a risk, not just what to change. A developer who understands the why will not repeat the pattern.

## What this chapter covered

- Phased lens rollout strategy for teams new to structured reviews
- The 30-minute review meeting format
- Reviewer calibration sessions
- How to make findings educational rather than just corrective

## References

- [Apex Best Practices](https://developer.salesforce.com/docs/atlas.en-us.apexcode.meta/apexcode/apex_bestpractices.htm)
- [Salesforce Developer Training](https://developer.salesforce.com/training)