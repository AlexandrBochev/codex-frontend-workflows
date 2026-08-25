---
name: code-review-followup
description: Re-check previous code review findings after another developer has made changes. Verify each original finding against the latest Merge Request diff, report whether it is fixed, partially fixed, or still unresolved, preserve the original review anchor, and do not modify code. Use when the user asks to verify fixes after a previous code review.
---

# Code Review Follow-up

Re-check previously reported code review findings after the reviewed developer has pushed changes.

This skill is for follow-up verification only.

Do not perform a completely new code review unless the user explicitly asks.

Do not modify the reviewed implementation.

Do not fix findings automatically.

Do not stage, commit, push, switch branches, merge, rebase, or modify Git history.

## Preferred Input

Prefer the same Merge Request or Pull Request URL used for the original review.

Example:

`$code-review-followup https://gitlab.example.com/group/project/-/merge_requests/123`

The previous review findings may come from:

- the current conversation;
- existing MR review comments or discussions;
- review text supplied by the user.

If the previous findings cannot be identified reliably:

- do not invent them;
- ask the user to provide the previous review comments or the original review result.

## Core Rules

- Verify previous findings one by one.
- Use the latest source-branch state.
- Refresh remote branch information before verification when possible.
- Preserve the original finding meaning.
- Do not treat a changed line as proof that the problem is fixed.
- Verify the underlying behavior or logic.
- Report whether each finding is:
  - `Исправлено`;
  - `Частично исправлено`;
  - `Не исправлено`.
- Always show the original review anchor.
- Show the new current anchor when the relevant code moved.
- Keep explanations short and concrete.
- Write in Russian.
- Address the developer as `Ты` when a new comment is required.
- Do not modify code.
- Do not automatically start a new full review.

## Workflow

1. Read all applicable instructions:
   - global Codex instructions;
   - project `AGENTS.md`;
   - nested `AGENTS.md` relevant to the reviewed files.

2. Identify the MR or PR being re-checked.

3. Identify the previous review findings.

4. Refresh remote metadata without modifying the working branch.

5. Resolve:
   - latest source branch;
   - target branch;
   - latest diff;
   - latest reviewed code.

6. Verify branch freshness.

7. For every previous finding:
   - locate the original issue;
   - inspect the latest implementation;
   - inspect surrounding code when necessary;
   - determine whether the underlying problem is actually resolved;
   - verify whether the fix introduced a different issue directly related to the original correction.

8. Classify each finding as:
   - `Исправлено`;
   - `Частично исправлено`;
   - `Не исправлено`.

9. Verify all current file and line anchors before responding.

10. Return the result finding by finding.

## Git Safety

Do not alter the reviewed branch.

Do not:

- checkout another branch unnecessarily;
- reset files;
- discard local changes;
- stash automatically;
- merge;
- rebase;
- pull with merge or rebase;
- stage;
- commit;
- push.

Remote refs may be refreshed for accurate review.

Preserve unrelated local user changes.

## Previous Findings Are the Source of Truth

The purpose of this skill is to verify existing review findings.

Do not silently rewrite the original finding into a different requirement.

For every previous finding:

- preserve the original problem being reviewed;
- verify the exact behavior or risk described;
- do not mark it fixed merely because the implementation changed.

Example:

Original finding:

`lastActivityLabel` does not update because current time is not reactive.

A change from `useMemo` to another helper is not automatically a fix.

Verify that the displayed relative time now actually updates over time.

## Status Definitions

### Исправлено

Use `Исправлено` only when:

- the original problem no longer exists;
- the fix addresses the actual root cause;
- the new implementation does not preserve the same bug through a different path.

### Частично исправлено

Use `Частично исправлено` when:

- part of the original problem is fixed;
- an important case remains unresolved;
- the change reduces the risk but does not fully remove it;
- the implementation satisfies only part of the review comment.

Explain exactly what remains.

### Не исправлено

Use `Не исправлено` when:

- the original problem still exists;
- the change does not address the underlying cause;
- the fix is cosmetic or unrelated;
- the same bug can still occur.

Explain briefly why.

## Original Review Anchor

Always include the location under which the original review comment was written.

Use:

`Original: <file path>:<line number>`

Do not replace the original anchor just because the file changed.

The original anchor is important because the user may need to find the existing MR discussion.

If the original line number is known, preserve it exactly.

Do not invent an original line number.

## Current Code Anchor

If the relevant code still exists, also include the current location:

`Current: <file path>:<line number>`

Use the latest source-branch line.

If the code moved to another file or function, point to the new location.

If the relevant code was removed completely and that removal correctly resolves the issue, say so.

If no meaningful current anchor exists, omit `Current`.

## Verifying Current Anchors

Before reporting a current anchor:

1. inspect the latest reviewed source;
2. verify the exact file;
3. verify the exact line;
4. confirm that the line contains the relevant code;
5. confirm that the line belongs to the latest source branch.

Never invent line numbers.

## Validation During Follow-up

Follow-up review is primarily static verification.

Do not automatically run:

- full tests;
- full lint;
- production build;
- complete regression suites.

Run focused validation only when it materially helps determine whether a specific previous finding is fixed.

Examples:

- run an existing focused test related to the finding;
- run a narrow test pattern;
- inspect runtime behavior when the bug cannot be verified statically.

Do not duplicate CI for every follow-up.

If validation is used, mention only the validation that materially affected the conclusion.

## New Problems Introduced by the Fix

Do not turn follow-up into a full fresh review.

However, if the attempted fix introduces a new issue directly related to the correction:

- mention it under the same finding;
- do not hide it;
- explain that the original problem may be fixed but the correction introduced a new regression.

Example:

`Исправлено`, но новый interval теперь не очищается on unmount.

In this case the finding may be classified as:

`Частично исправлено`

if the new problem prevents the correction from being safely considered complete.

## Unrelated New Findings

Do not search broadly for unrelated new defects.

If you happen to notice an unrelated serious issue while verifying the fixes:

- do not mix it into the previous finding status;
- place it in a separate section:

`## Новые замечания`

Only include genuinely meaningful new issues.

Do not add minor or stylistic new findings.

## Required Output Format

For each previous finding use:

### Finding N — Исправлено

`Original: src/path/file.tsx:42`

`Current: src/path/file.tsx:51`

`codeAnchor`

Короткое объяснение, почему проблема исправлена.

---

For a partially fixed finding:

### Finding N — Частично исправлено

`Original: src/path/file.tsx:42`

`Current: src/path/file.tsx:57`

`codeAnchor`

Что исправлено и что ещё осталось.

---

For an unresolved finding:

### Finding N — Не исправлено

`Original: src/path/file.tsx:42`

`Current: src/path/file.tsx:51`

`codeAnchor`

Проблема всё ещё остаётся: короткое объяснение.

## When a New MR Comment Is Needed

If a finding is `Не исправлено` or `Частично исправлено`, provide a short updated comment that can be posted into the MR.

Use:

`Комментарий:`

Then write a concise Russian comment addressing the developer as `Ты`.

Example:

Комментарий:

Ты добавил interval, но `lastActivityLabel` всё ещё вычисляется из первоначального `now`, поэтому значение не обновляется. Передавай в formatter обновляемое текущее время или вычисляй label при каждом tick.

Do not add a new MR comment for findings that are fully fixed unless the user asks.

## Findings Order

Preserve the order of the original review findings when possible.

Do not reorder them by a new severity unless necessary.

The goal is to make comparison with the previous review easy.

## No Remaining Problems

If every previous finding is fixed, finish with:

`Все предыдущие замечания исправлены.`

Do not invent new comments merely to produce additional output.

## Final Verification

Before responding, verify for every finding:

- the original finding was identified correctly;
- the latest source branch was reviewed;
- the status matches the actual current behavior;
- the original anchor is preserved;
- the current anchor is accurate;
- the code anchor matches the relevant code;
- a finding marked `Исправлено` is actually fully resolved;
- unresolved findings contain a concise updated MR comment.

## Final Response

Write in Russian.

Start directly with the finding statuses.

Do not modify code.

Do not start a new full code review automatically.

At the end, optionally include:

`## Итог`

Keep it short.

Mention:

- how many findings were fixed;
- how many remain partially fixed;
- how many remain unresolved;
- any limitation that prevented verification.

Wait for the user to decide whether another review pass is needed.
