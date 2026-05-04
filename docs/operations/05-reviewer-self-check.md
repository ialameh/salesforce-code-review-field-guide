# O5. Reviewer Self-Check

The final stage is the one most reviewers skip: your own blind spots. Every reviewer has a dominant lens they over-apply and a weak lens they under-apply. This stage makes those biases visible before you publish the report.

## The self-check prompt

After writing your final report,停下来 and answer these questions honestly:

**1. What did I find that I was surprised by?**
Write down the finding that caught you off guard. Surprises usually mean the code had a property you did not expect. That property may also have masked other issues you did not see.

**2. What did I skip or treat lightly?**
Write down every lens you applied cursorily. Did you skip the Integration lens because you ran out of time? Did you treat the Metadata lens lightly because the custom metadata was complex? Whatever you skipped is a gap in the review.

**3. What would I have done differently if the author were in the room?**
If you wrote something you would not say to their face, soften it or remove it. If you did not write something you would say to their face, add it.

**4. What does this codebase do that I do not understand?**
Write down the parts of the system you read but did not fully understand. These are the parts where your findings are least reliable.

**5. Am I treating style issues as equal to production risks?**
Look at your Low-severity findings. Are any of them actually Medium or High in disguise because they affect a critical path? Are any High findings actually style issues that have no production impact?

**6. Did I find anything that would make me reluctant to deploy this code?**
If the answer is yes, the final report must say that clearly. Do not soften it to be polite.

## Common reviewer biases

**Experience bias:** You find what you know. If you are deep in SOQL performance, you will find SOQL issues and miss async failures. Actively look for what you do not know.

**Halo effect:** One strong positive impression of the codebase makes you rate everything higher. Counter this by applying the Strength label sparingly and only for patterns that are genuinely exceptional.

**Anchor bias:** The first finding sets the tone. If the first thing you see is bad, you rate everything as worse. If it is good, you rate everything as better. Read the inventory before you open any class.

**Authority bias:** If the code was written by a senior developer or architect, you are less likely to flag it. Fight this. The code stands on its own.

**Time pressure:** If you are reviewing under a deadline, you will cut corners. Flag what you cut. Tell the reader what was not reviewed.

## Self-check output

Write your answers into the final report under a section called `## Reviewer Self-Check`. This keeps you honest and signals to the reader that you know the review is imperfect.

```md
## Reviewer Self-Check

**Surprised by:** [What caught me off guard]

**Skipped or treated lightly:** [Lenses I did not apply fully]

**Would say differently face-to-face:** [What I softened or omitted]

**Do not fully understand:** [Parts of the system I read but did not grasp]

**Style issues treated as production risks:** [Yes/No, with explanation]

**Reluctant to deploy:** [Yes/No, with explanation]
```

## What this chapter covered

- The five-question self-check prompt
- Six common reviewer biases and how to counter them
- How to document your self-check findings in the final report

## References

- [Apex Developer Best Practices](https://developer.salesforce.com/docs/atlas.en-us.apexcode.meta/apexcode/apex_bestpractices.htm)
- [Trigger Execution Order](https://developer.salesforce.com/docs/atlas.en-us.apexcode.meta/apexcode/apex_triggers_order.htm)