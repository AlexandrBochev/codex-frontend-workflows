---
name: code-final-polish
description: Perform a final code-quality pass after implementation is complete. Review the finished task for risky code, readability, unnecessary complexity, dead or temporary files, component structure, hook extraction, formatting, regressions, and final task-artifact cleanup without changing approved styles or feature scope. Use when the implementation is finished and the user asks for final code polish, cleanup, or a final quality pass before code review or merge.
---

# Code Final Polish

Perform the final code-quality pass for a completed task.

The feature is already implemented and functionally verified.

The goal of this skill is to improve safety, maintainability, readability, and cleanliness without redesigning the feature or expanding its scope.

## Core Rules

- Review the complete task implementation before changing code.
- Preserve existing behavior unless fixing a concrete bug or regression.
- Do not change approved UI styles.
- Do not redesign the feature.
- Do not add new product functionality.
- Do not perform unrelated refactoring.
- Preserve unrelated user changes.
- Prefer readable and maintainable code over clever or overly compact code.
- Remove temporary or obsolete code only when it is clearly safe.
- Remove the completed implementation plan when it is no longer needed.
- Run final project validation after polishing.
- Do not create commits, push, stage files, switch branches, or modify Git history unless explicitly requested.

## Workflow

1. Read all applicable instructions:
   - global Codex instructions;
   - project `AGENTS.md`;
   - any nested `AGENTS.md` relevant to changed files.

2. If `.codex/IMPLEMENTATION_PLAN.md` exists:
   - read it completely;
   - verify that all implementation steps are completed;
   - understand the intended scope, requirements, risks, and decisions;
   - do not reopen completed product decisions without a concrete reason.

3. Inspect the current Git state:

   `git status --short`

4. Determine the complete set of changes belonging to the current task.

5. Inspect the relevant diff and all changed production and test files.

6. Perform a final code review focused on:
   - correctness;
   - potential regressions;
   - unsafe assumptions;
   - edge cases;
   - error handling;
   - stale state;
   - async behavior;
   - race conditions;
   - duplicate submissions;
   - cleanup of effects, listeners, subscriptions, and timers;
   - accessibility regressions;
   - unnecessary complexity;
   - duplicated logic;
   - dead code;
   - temporary code or files;
   - code readability;
   - component and hook structure;
   - TypeScript safety;
   - test coverage for important logic.

7. Make safe improvements that belong to the current task.

8. Inspect the final diff again.

9. Run the required final validation.

10. If `.codex/IMPLEMENTATION_PLAN.md` exists and all implementation steps are completed:
    - remove the implementation plan;
    - remove the `.codex` directory only if it becomes empty;
    - do not remove other `.codex` files.

11. Inspect the final Git state and diff one last time.

12. Report what was polished and which checks passed.

## Existing Working Tree

Treat any pre-existing modified or untracked files that are unrelated to the current task as user work.

Do not:
- revert unrelated changes;
- overwrite unrelated files;
- delete unrelated untracked files;
- include unrelated files in cleanup;
- stage user changes;
- assume every modified file belongs to the current task.

If ownership of a file is unclear, preserve it unless there is strong evidence that it was created by the current task and is now obsolete.

## Safety Review

Look for potentially dangerous implementation details.

Examples include:

- missing error handling;
- swallowed errors;
- unsafe fallback behavior;
- incorrect assumptions about API data;
- missing validation at important boundaries;
- accidental `any`;
- unsafe type assertions;
- stale closures;
- incorrect effect dependencies;
- effects without cleanup;
- event listeners without cleanup;
- subscriptions without cleanup;
- timers without cleanup;
- requests that can race and overwrite newer state;
- duplicate form submissions;
- state updates after obsolete requests;
- incorrect loading/error state transitions;
- destructive actions without required protection;
- client-side checks incorrectly treated as security boundaries;
- precision loss;
- accidental mutation;
- fragile `null` or `undefined` assumptions;
- missing accessible names;
- broken keyboard interaction;
- incorrect URL state restoration;
- stale cache or refresh assumptions;
- hidden optimistic updates that can diverge from server state.

Fix issues when:
- the correct behavior is clear;
- the fix stays within the current task scope;
- the fix does not redesign approved behavior.

If a potentially serious issue requires an unclear product or architectural decision:
- do not invent the decision;
- report the issue;
- explain why it should not be changed automatically.

## Readability

Make the code easy for another developer to read and understand.

Prefer:
- descriptive variable and function names;
- small focused functions;
- clear control flow;
- explicit conditions;
- meaningful grouping of related logic;
- straightforward TypeScript types;
- project-established patterns;
- predictable file organization.

Avoid:
- clever one-liners that reduce readability;
- deeply nested conditions;
- duplicated branches;
- unnecessary intermediate abstractions;
- excessive indirection;
- comments that merely repeat the code;
- generic names such as `data`, `item`, `value`, or `handler` when a more specific name materially improves understanding.

Use comments only when they explain:
- non-obvious business rules;
- intentional constraints;
- unusual technical decisions;
- behavior that would otherwise be easy to misunderstand.

## Components

Keep React components focused and easy to scan.

If a component contains a large amount of JSX:
- identify meaningful UI sections;
- extract them into smaller components when this makes the parent easier to understand;
- keep closely related UI together;
- avoid splitting trivial markup into unnecessary files.

If a component mixes several unrelated responsibilities:
- separate those responsibilities when the extraction improves readability and maintainability.

Do not split components only to reduce line count.

Prefer meaningful boundaries over arbitrary size limits.

## Hooks and Logic Extraction

If a component contains many:
- state variables;
- side effects;
- event handlers;
- async operations;
- derived state calculations;
- URL synchronization logic;
- form lifecycle logic;
- search or pagination logic;

consider extracting related logic into custom hooks.

Extract a custom hook when it creates a clear logical unit.

A good hook should encapsulate a meaningful responsibility such as:
- form state;
- async search;
- pagination;
- URL synchronization;
- transfer lifecycle;
- data loading;
- keyboard interaction;
- browser-visible state coordination.

Do not create trivial hooks that merely move a few lines without improving understanding.

Do not hide simple local state behind unnecessary abstractions.

## Functions and Utilities

Review long functions and utility modules.

Consider splitting a function when it:
- performs multiple independent responsibilities;
- contains deeply nested branches;
- mixes parsing, validation, side effects, and presentation;
- is difficult to understand without reading it several times.

Prefer small named helpers when the helper communicates a meaningful concept.

Do not extract single-use helpers merely to reduce line count when the original code is already clearer inline.

## Duplication

Look for duplicated logic introduced by the task.

Before creating a new abstraction:
- check whether the project already has a suitable utility, component, hook, schema, constant, or helper;
- prefer existing project patterns when they fit.

Extract duplicated logic only when the common abstraction is genuinely clearer.

Do not generalize code prematurely for hypothetical future reuse.

## Types

Review TypeScript types for clarity and safety.

Check for:
- unnecessary `any`;
- unsafe assertions;
- duplicated interfaces that should come from schemas or existing types;
- overly broad unions;
- optional properties that are actually required;
- nullable values that are not handled;
- API DTO types leaking directly into UI code;
- domain values being converted into lossy types.

Prefer inferred types from validated schemas when that is already a project pattern.

Do not add complex generic abstractions unless they materially improve type safety and readability.

## Async and Effects

Review asynchronous behavior carefully.

Check:
- whether stale requests can overwrite newer results;
- whether loading and error state transitions are consistent;
- whether effects have correct dependency lists;
- whether effects perform work that belongs in event handlers or data-loading abstractions;
- whether cleanup is required;
- whether multiple requests can be triggered unintentionally;
- whether duplicate submits are prevented;
- whether error paths leave stale UI state behind.

Do not rewrite stable async code only to match a personal style preference.

## Dead and Temporary Code

Remove code and files that are clearly no longer needed because of the current task.

Examples:
- temporary debugging logs;
- abandoned implementation files;
- unused components;
- unused hooks;
- unused helpers;
- obsolete constants;
- commented-out old implementation;
- temporary scripts created only during development;
- stale imports;
- unused types;
- temporary fixtures that are no longer required;
- obsolete test helpers created during an earlier version of the implementation.

Do not delete:
- unrelated untracked files;
- existing project files merely because they currently appear unused;
- documentation or tooling files without understanding their purpose.

Before deleting a file, exported symbol, component, hook, utility, type, or constant:
- search the repository for usages;
- confirm that it is no longer required.

## Implementation Plan Cleanup

`.codex/IMPLEMENTATION_PLAN.md` is a temporary working artifact for the task.

If it exists and all implementation steps are completed:
- remove `.codex/IMPLEMENTATION_PLAN.md` during final polish;
- do not keep it merely for historical reference unless the user explicitly asks to preserve it;
- do not stage or commit it;
- verify that no unfinished implementation step remains before deleting it.

After removing the plan:
- inspect `.codex`;
- remove the `.codex` directory only if it is empty;
- preserve any other file inside `.codex`.

If any implementation step is still unfinished:
- do not remove the implementation plan;
- report that final polish cannot be considered fully complete until the plan is resolved.

## Styles

Do not change approved visual styles during final polish.

Do not:
- redesign layout;
- change spacing;
- change typography;
- change colors;
- change component dimensions;
- change responsive behavior;
- change visual hierarchy;
- replace approved UI components for aesthetic reasons.

Style changes are allowed only when required to fix:
- a concrete regression;
- an accessibility problem;
- broken layout caused by the implementation;
- clearly unintended styling introduced by the current task.

If a style change is required for one of these reasons:
- keep it minimal;
- explain it in the final response.

## Tests

Review tests together with production code.

Check that tests:
- verify meaningful behavior rather than implementation details;
- cover important validation and edge cases;
- do not duplicate the same assertion unnecessarily;
- do not contain obsolete expectations;
- remain readable;
- reflect the final behavior after all implementation corrections.

Add or update focused tests when polishing reveals a meaningful uncovered bug or edge case.

Do not create tests solely to increase coverage numbers.

Remove obsolete or redundant tests only when the remaining tests still clearly cover the required behavior.

## Formatting

Inspect the project's formatting configuration.

If Prettier is configured:
- run Prettier or the project's formatting command for all files modified by the current task;
- verify that the final diff contains no formatting-only mistakes.

If there is no dedicated formatting script:
- use the existing Prettier configuration directly when appropriate.

Do not invent formatting rules that conflict with the repository.

## Final Validation

This is the final polish phase, so full regression validation is appropriate.

Inspect `package.json`, project instructions, and existing tooling to determine the available commands.

When available and relevant, run:

1. Prettier or formatting validation;
2. lint;
3. TypeScript or type checking;
4. the complete automated test suite;
5. production build.

Use only actual project commands.

Do not invent commands that do not exist.

If one command already performs another validation step, avoid unnecessary duplicate work when this is clear from project configuration.

If a check fails:
- determine whether the failure was caused by the current task;
- fix task-related failures;
- rerun the relevant check.

If a failure is clearly pre-existing or unrelated:
- do not modify unrelated code merely to make it pass;
- report the failure explicitly.

Never claim that a validation command passed unless it actually ran successfully.

## Manual Browser Testing After Polish

The feature is assumed to have already passed manual browser testing before final polish.

Do not automatically repeat full manual browser testing.

If final polish changes only:
- internal structure;
- component extraction;
- hook extraction;
- naming;
- dead code;
- formatting;
- equivalent refactoring;

automated validation is normally sufficient.

If final polish changes browser-visible behavior or fixes a UI or interaction bug:
- provide a short focused manual testing plan in Russian;
- explain what changed that requires browser verification;
- ask exactly:

`Можно перейти в Chrome и начать ручное тестирование через Chrome plugin?`

Do not access Chrome before explicit approval.

Use the existing authenticated development tab.

Do not open additional tabs or windows.

## Final Diff Review

Before finishing:

1. Run:

   `git status --short`

2. Inspect the final task diff.

3. Verify:
   - no temporary files remain;
   - `.codex/IMPLEMENTATION_PLAN.md` has been removed when all implementation steps are complete;
   - `.codex` has been removed only if it became empty;
   - no accidental debug code remains;
   - no unrelated files were changed by the polish;
   - no approved styles were unintentionally changed;
   - imports and exports are clean;
   - no obviously unused task files remain;
   - tests correspond to the final implementation;
   - no accidental formatting-only noise was introduced.

4. Run:

   `git diff --check`

5. Do not stage or commit changes.

## Final Response

Keep the final response concise and in Russian.

Report:
- what was polished;
- important safety or readability improvements;
- whether components or hooks were extracted;
- what temporary or dead code was removed;
- whether `.codex/IMPLEMENTATION_PLAN.md` was removed;
- which validation commands were run and their results;
- any unrelated pre-existing failures;
- whether manual browser testing is required after the polish.

If no meaningful code changes were necessary, say so explicitly.

Do not start code review automatically.

Wait for the user to request the next workflow.
