---
name: code-review
description: Review code written by another developer using a Merge Request or Pull Request URL when available, or source and target branches as a fallback. Review only the relevant diff, verify branch freshness and exact diff line anchors, report findings in Russian ordered by severity, and do not modify code. Use when the user asks to review someone else's implementation before merge.
---

# Code Review

Perform a code review of changes written by another developer.

This skill is for review only.

Do not modify production code.

Do not fix findings automatically.

Do not stage, commit, push, switch branches, rewrite history, merge, rebase, or otherwise alter the reviewed implementation unless the user explicitly asks.

## Preferred Input

Prefer a Merge Request or Pull Request URL as the review target.

Example:

`$code-review https://gitlab.example.com/group/project/-/merge_requests/123`

A Merge Request or Pull Request URL is preferred because it normally provides:

- source branch;
- target branch;
- current diff;
- task context;
- review discussions;
- merge state.

If no MR or PR URL is available, accept:

- source branch;
- target branch.

Example:

`$code-review`

`Source: feature/AG-123`

`Target: develop`

If only the source branch is provided:

- try to determine the target branch from reliable project context;
- if it cannot be determined confidently, ask the user for the target branch before reviewing.

Do not guess the comparison base.

## Core Rules

- Review code written by another developer.
- Do not edit the reviewed implementation.
- Review the actual source-to-target diff.
- Read surrounding code when needed to understand the change.
- Respect all applicable `AGENTS.md` instructions.
- Verify branch freshness before reviewing.
- Verify every reported line anchor against the reviewed diff.
- Report findings first.
- Order findings by severity.
- Keep comments short, specific, actionable, and ready to paste into the MR.
- Write in Russian.
- Address the developer as `Ты`.
- Do not manufacture findings merely to produce review comments.
- Do not report personal style preferences as defects.
- If no meaningful issues are found, say so explicitly.

## Workflow

1. Read all applicable instructions:
   - global Codex instructions;
   - project `AGENTS.md`;
   - nested `AGENTS.md` relevant to reviewed files.

2. Determine the review target.

   Prefer:
   - MR or PR URL;

   otherwise:
   - source branch;
   - target branch.

3. Inspect the current Git working tree:

   `git status --short`

4. Preserve all existing local user changes.

5. Refresh remote branch information without modifying the working branch when possible.

6. Resolve:
   - source branch;
   - target branch;
   - merge base;
   - reviewed diff.

7. Check whether the source branch is sufficiently up to date with the target branch to review safely.

8. Read the MR, PR, ticket, or task description when available.

9. Inspect the complete changed-file list.

10. Review the diff file by file.

11. Read surrounding implementation when needed to understand:
    - data flow;
    - component responsibilities;
    - API contracts;
    - shared utilities;
    - state lifecycle;
    - tests;
    - existing project patterns.

12. Identify only meaningful findings.

13. Run focused validation only when it materially helps confirm or reject a finding.

14. Verify every finding against the latest reviewed diff.

15. Verify every `file:line` anchor immediately before responding.

16. Return findings in the required format.

## Git Safety

Reviewing must not modify the user's working implementation.

Do not:

- discard changes;
- reset files;
- stash automatically;
- checkout another branch unnecessarily;
- merge target into source;
- rebase;
- pull with merge or rebase;
- stage files;
- commit;
- push.

Remote metadata may be refreshed when necessary for an accurate review.

Prefer non-destructive Git operations.

If the current working tree contains local modifications:

- preserve them;
- do not include them in the reviewed diff unless they are actually part of the requested review target;
- clearly distinguish local unrelated changes from source-branch changes.

## Branch Freshness

Before reviewing, verify that branch information is current.

When possible:

- refresh remote refs;
- identify the latest remote target branch;
- compare the source branch against that target.

Check whether:

- the source branch is behind the target branch;
- the merge base is unexpectedly old;
- conflicts are likely;
- the review is being performed against a fallback or stale base.

If the target branch is missing:

- do not silently substitute another branch;
- ask the user when the intended base cannot be determined.

If a fallback base must be used for a technical reason:

- state that limitation clearly in the final response.

Do not reject a review solely because the source branch is behind target.

Instead:

- review the current diff;
- mention branch staleness when it materially affects confidence or creates likely conflicts or regressions.

## Review Scope

Review only the changes introduced by:

`source branch → target branch`

Do not review the entire repository as if every existing issue belongs to the author.

Use surrounding unchanged code only to understand the consequences of the diff.

A finding should normally be caused by:

- a changed line;
- newly introduced behavior;
- deleted behavior;
- interaction between the diff and existing code.

Do not report unrelated legacy issues.

If the diff exposes or worsens an existing issue:

- report it only when the current changes materially introduce or increase the risk;
- anchor the comment to the closest changed line responsible for the regression.

## Validation During Review

Code review is primarily a static review of the source-to-target diff and relevant surrounding code.

Do not automatically run the project's:

- full test suite;
- full lint suite;
- production build;
- complete regression suite;
- other project-wide validation commands;

for every review.

Run validation only when:

- it is necessary to confirm or reject a specific finding;
- the changed code has a focused existing test that can quickly validate the behavior;
- the user explicitly asks for verification;
- project instructions explicitly require validation during review.

Prefer focused validation over project-wide commands.

Examples of appropriate review validation:

- one focused test file for the changed hook or component;
- a narrow test pattern for the suspected regression;
- scoped lint for a changed file when lint behavior is relevant;
- a type check when a finding depends on TypeScript behavior and a focused command is available.

Do not run a full build or full test suite merely for additional confidence.

Do not turn ordinary code review into a duplicate CI run.

If validation is run:

- mention only checks that materially informed the review;
- do not list unrelated commands just because they were available.

If a check cannot run because of an unrelated environment or project issue:

- do not treat that failure as a finding against the reviewed developer;
- mention the limitation only when it affects review confidence.

## Review Priorities

Focus primarily on defects that could affect users, data, security, maintainability, or future changes.

### Correctness

Look for:

- incorrect logic;
- missing branches;
- wrong conditions;
- incorrect data mapping;
- invalid assumptions;
- off-by-one errors;
- broken sorting;
- broken filtering;
- broken pagination;
- stale derived values;
- incorrect fallback behavior.

### Regressions

Look for:

- existing behavior unintentionally removed;
- shared behavior changed unexpectedly;
- backward compatibility problems;
- URL or state restoration regressions;
- responsive regressions;
- localization regressions.

### Authorization and Security

Look for:

- missing authorization checks;
- client-side authorization treated as a security boundary;
- unsafe direct actions;
- leaking restricted data;
- insufficient validation of privileged operations.

### API and Validation

Look for:

- incorrect request shape;
- incorrect response assumptions;
- missing validation;
- raw API objects leaking into UI;
- enum assumptions that reject valid backend values;
- nullable assumptions that can crash;
- precision loss;
- silent parsing failures;
- incorrect snake_case or camelCase mapping;
- missing error-state handling.

### React and State

Look for:

- stale closures;
- incorrect effect dependencies;
- unnecessary effects;
- effects without cleanup;
- race conditions;
- stale async results;
- duplicate requests;
- duplicate submissions;
- state that can become inconsistent;
- invalid controlled or uncontrolled transitions;
- memoized values that depend on changing external state but never update.

### UX

Look for:

- incorrect loading state;
- misleading empty or error state;
- actions enabled when invalid;
- missing pending protection;
- unexpected resets;
- broken back or forward behavior;
- hidden failures;
- stale visible data;
- misleading success feedback.

### Accessibility

Look for:

- missing accessible names;
- broken keyboard navigation;
- incorrect focus behavior;
- inaccessible custom controls;
- incorrect ARIA state;
- inaccessible error feedback;
- controls that cannot be reached or operated by keyboard.

### Performance

Report performance findings only when there is a concrete meaningful problem.

Examples:

- obvious N+1 requests;
- repeated expensive computation on every render;
- unnecessary repeated network requests;
- severe rendering churn;
- loading significantly more data than required.

Do not report speculative micro-optimizations.

### Tests

Look for:

- important new logic with no meaningful regression test;
- missing coverage for a concrete bug-prone path;
- tests that assert the wrong behavior;
- tests that would pass while the implementation is broken;
- tests that became obsolete because behavior changed.

Do not request tests merely because every changed function lacks its own test.

## What Not to Report

Do not create findings for:

- personal formatting preferences;
- naming preferences that do not materially hurt readability;
- harmless style differences;
- theoretical future abstractions;
- premature DRY suggestions;
- "this could be cleaner" without a concrete risk;
- code outside the reviewed diff that was not affected by the change;
- missing comments when the code is already clear;
- requests to refactor code solely because it is long;
- formatting issues that Prettier or ESLint already handles automatically.

Do not manufacture Low findings to make the review look more complete.

If a concern is too weak to justify a concrete review comment, omit it.

## Severity

Order findings from most important to least important.

Use these severities:

### Critical

Use only when the change can cause severe consequences such as:

- major security exposure;
- destructive data corruption;
- irreversible unintended production action;
- broad application failure.

### High

Examples:

- broken core functionality;
- authorization bypass;
- incorrect financial or data mutation;
- major regression;
- reliable runtime crash on a normal path.

### Medium

Examples:

- incorrect edge-case behavior;
- broken loading or error flow;
- meaningful accessibility regression;
- realistic race condition;
- missing validation that can produce incorrect behavior.

### Low

Examples:

- small but concrete maintainability risk;
- minor UX defect;
- narrowly scoped missing regression test for risky logic.

Do not use severity to inflate minor issues.

If a finding is too weak to justify at least `Low`, omit it.

## Exact Diff Line Anchors

Every finding must include an exact review anchor.

Use:

`<file path>:<line number>`

The line number must correspond to the reviewed diff.

Prefer:

- the changed line on the new or right side of the MR diff.

Do not use a local line number blindly.

Do not use `nl -ba` output as the final review anchor unless it matches the actual reviewed diff line.

Before returning a finding:

1. inspect the final reviewed diff;
2. locate the exact changed line;
3. verify that the reported line contains the code referenced in the finding;
4. verify that the line still exists after the latest source-branch changes.

If the issue belongs to surrounding code rather than a directly changed line:

- anchor it to the closest changed line that introduces or exposes the risk;
- make that relationship clear.

Never invent a line number.

## Code Anchor

Every finding must include a short code anchor.

Use the main:

- variable;
- function;
- component;
- hook;
- condition;
- API field;
- changed expression;

associated with the finding.

Examples:

`selectedCounterpartyId`

`handleSubmit`

`availableBalance`

`lastActivityLabel`

`useEffect(() => ...)`

`response.data`

This lets the user compare the review comment with the exact code under which it should be posted.

Do not write vague comments that cannot be matched to the line.

## Comment Style

Every review comment must be ready to paste directly into the MR.

Write in Russian.

Address the developer as `Ты`.

Keep the tone professional and collaborative.

Each comment should:

- identify the concrete problem;
- explain the real consequence;
- suggest a short direction for fixing it.

Keep the main comment focused on the defect.

Do not write long essays.

Do not over-explain obvious code.

Do not praise code inside findings.

Do not add filler.

Prefer:

`Ты мемоизируешь \`lastActivityLabel\`, хотя результат зависит от текущего времени. Без реактивного источника времени значение не обновится до следующего render/remount. Используй \`useNow\` или interval и рассчитывай label от обновляемого времени.`

Avoid:

`Мне кажется, возможно, потенциально было бы неплохо рассмотреть вариант...`

### Test Suggestions

Do not automatically append a test request to every review comment.

Suggest adding or updating a test only when:

- the bug is realistically regression-prone;
- missing coverage materially contributed to the issue;
- the test is an important part of preventing recurrence;
- the finding itself is specifically about missing or incorrect tests.

If a test suggestion is useful, keep it short and secondary to the actual defect.

## Required Finding Format

For every finding use exactly this structure:

### [Severity] Short finding title

`<file path>:<line number>`

`<short code anchor>`

Комментарий:

<short review comment in Russian, addressing the developer as Ты>

Example:

### [High] Теряется точность суммы

`src/components/transfer-form.tsx:84`

`availableBalance`

Комментарий:

Ты преобразуешь `availableBalance` в `Number`, поэтому значения с высокой decimal precision могут округлиться до отправки. Используй существующий precision-safe decimal helper для сравнения и расчёта remaining balance.

## Findings First

Always start the final response with findings.

Do not put a long summary before them.

If there are findings:

- list all findings first;
- order them by severity;
- within the same severity, order the most important first.

After findings, a very short review summary is allowed.

## No Findings

If no meaningful issues are found, write:

`Существенных проблем в текущем diff не найдено.`

Then briefly mention:

- what was reviewed;
- whether branch freshness was verified;
- any limitation that materially affected the review.

Do not invent minor findings merely to avoid returning an empty review.

## Review Verification Pass

Before sending the final response, perform a final verification pass.

For every finding verify:

- the issue still exists in the latest reviewed source;
- the finding was introduced or materially exposed by this diff;
- severity is appropriate;
- the exact file path is correct;
- the exact line number matches the reviewed diff;
- the line contains the code referenced in the comment;
- the code anchor is correct;
- the suggested fix does not contradict project architecture;
- the comment is concise enough to post directly in the MR.

Remove any finding that does not survive this verification.

## Final Response

Write in Russian.

Start with findings.

Do not modify code.

After the findings, optionally include:

`## Итог`

Keep it concise.

It may include:

- number of findings by severity;
- source and target branches;
- branch freshness status;
- validation that materially informed the review;
- review limitations.

Do not include unrelated CI information.

Do not start fixing findings automatically.

Wait for the user to decide what to do next.
