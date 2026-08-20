When I ask for a code review, always respond in this format:

- `<file path>:<line number>`
- short comment describing the problem and a short proposed fix

Additional rules:
- Always list findings first, ordered by severity.
- Keep each comment short, specific, and actionable.
- Use exact file path and exact line number whenever possible.
- Do not write long summaries before the findings.
- If no issues are found, explicitly say that no issues were found.
- Focus on bugs, regressions, risky logic, missing validation, incorrect UX behavior, accessibility issues, and missing tests.

Line number verification rules for code reviews:
- Verify every `file:line` anchor against the exact reviewed diff before sending the final answer.
- Prefer the changed line on the new/right side of the PR diff.
- Do not use local `nl -ba` line numbers as the final anchor unless they match the reviewed diff.
- Include a short code anchor in each finding, such as the function name or changed expression, so the target line is unambiguous.
- If a finding belongs to surrounding code rather than a changed line, say that explicitly and anchor it to the closest changed line that introduces the risk.
- If the target base branch is missing or a fallback base is used, state that limitation in the review.
- Before the final answer, re-check that each reported line contains the code referenced by the comment.
