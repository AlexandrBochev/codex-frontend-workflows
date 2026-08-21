---
name: implement-plan-step
description: Implement exactly one next unfinished step from .codex/IMPLEMENTATION_PLAN.md, validate it with focused checks, optionally perform approved manual browser testing in the existing Chrome development tab, update the plan, and stop before the next step. Use when the user asks to start or continue implementation one step at a time.
---

# Implement Plan Step

Implement exactly one next unfinished step from:

`.codex/IMPLEMENTATION_PLAN.md`

This skill is intentionally incremental.

Do not implement multiple plan steps in one run.

Do not continue to the next step automatically.

## Core Rules

- Work on exactly one unfinished implementation step.
- Read the entire implementation plan before changing code.
- Respect all applicable `AGENTS.md` instructions.
- Do not guess when the current step depends on unresolved requirements.
- Preserve unrelated user changes.
- Prefer focused validation during individual steps.
- Use manual browser testing only when it provides meaningful validation.
- Never access or control Chrome before receiving explicit user approval.
- Do not mark a step completed until all validation required for that step has succeeded.
- Stop after the current step is completed, blocked, or waiting for manual testing.

## Workflow

1. Read the applicable project instructions:
   - read the global Codex instructions;
   - check for project `AGENTS.md` files;
   - read every `AGENTS.md` that applies to the files involved in the current step.

2. Read:

   `.codex/IMPLEMENTATION_PLAN.md`

3. Find the first unfinished implementation step marked:

   `- [ ]`

4. Read the entire plan before implementing the step.

   Understand:
   - the overall goal;
   - requirements relevant to the current step;
   - previously completed steps;
   - dependencies between steps;
   - known risks;
   - relevant open questions;
   - functionality intentionally assigned to later steps.

5. Check whether an unresolved question or missing requirement blocks the current step.

   If the current step depends on unresolved information:
   - do not guess;
   - do not silently choose an implementation;
   - do not skip to another step;
   - explain what information is missing;
   - ask the user for clarification;
   - stop.

6. If the project is a Git repository, inspect the current working tree:

   `git status --short`

7. Inspect the existing code relevant to the current step before modifying it.

8. Implement only the current step.

9. Run focused automated validation appropriate for the current step.

10. Inspect the relevant diff and working tree after implementation.

11. Determine whether manual browser testing is useful or required.

12. If manual browser testing is not required and relevant automated validation succeeded:
    - mark the current step completed;
    - report the result;
    - stop.

13. If manual browser testing is required:
    - keep the current step unfinished;
    - provide a short manual testing plan;
    - ask for permission to use Chrome;
    - stop and wait for the user.

14. After successful manual validation:
    - mark the current step completed;
    - report the result;
    - stop.

Do not start the next unfinished step until the user explicitly asks to continue.

## Existing Working Tree

Treat modified or untracked files that existed before the current step as user work.

Do not:
- revert pre-existing changes;
- overwrite them unnecessarily;
- stage them;
- include them in cleanup;
- assume they were created by the current step.

If files relevant to the current step already contain partial implementation:
- inspect the existing changes;
- preserve valid work;
- continue from the current state when appropriate;
- do not recreate working implementation merely because the plan step is still marked unfinished.

After implementation, inspect the working tree and relevant diff again.

Verify that:
- only expected files were changed by the current step;
- unrelated user changes remain untouched;
- no accidental temporary files were introduced.

Do not create commits, push, switch branches, stage files, or modify Git history unless the user explicitly asks.

## Scope

Implement exactly one plan step per user request.

Do not:
- batch several plan steps together;
- start later plan steps early;
- perform unrelated refactoring;
- modify unrelated files;
- change behavior outside the scope of the current step.

Small supporting changes required to complete the current step are allowed.

If completing the step unexpectedly requires a significant change that clearly belongs to another plan step:
- stop;
- explain the dependency;
- do not silently expand the scope.

## Resolving Open Questions

If a blocking open question is answered by the user or verified from project documentation during the current conversation:

- treat the confirmed part as resolved;
- update `.codex/IMPLEMENTATION_PLAN.md`;
- preserve the useful answer in the relevant requirement, implementation step, risk, or decision;
- remove the resolved question from `Open Questions`;
- update the affected implementation step only when the new information materially changes its requirements;
- do not rewrite unrelated parts of the plan.

If the answer is only partial:
- record the confirmed information;
- keep only the unresolved part in `Open Questions`;
- do not block the current step if the remaining uncertainty does not affect it.

After resolving a blocking question, resume the same unfinished implementation step unless the user asks to stop.

Do not repeatedly ask a question that has already been answered and recorded in the plan.

## Automated Validation

Run the most focused validation that gives reasonable confidence in the current step.

Do not automatically run every command listed in the plan's global `Validation` section.

Treat the global `Validation` section primarily as final-task regression validation.

### Validation Decision Rules

Choose validation in this order:

1. Run validation explicitly required by the current implementation step.

2. Run focused tests for the files, functions, components, hooks, actions, or behavior changed by the current step.

3. Run scoped lint and formatting checks when supported by the project.

4. Run focused TypeScript or type validation when available and relevant.

5. Run a production build only when the current change genuinely requires build-level validation.

   Appropriate examples include:
   - adding or changing application routes;
   - changing framework or build configuration;
   - changing server/client component boundaries;
   - changing middleware, proxy, layouts, or other framework-level behavior;
   - changing code whose correctness materially depends on the production compilation process;
   - the current implementation step explicitly requires a build.

   The absence of a dedicated type-checking command is not, by itself, a reason to run a production build.

   For isolated utilities, schemas, hooks, components, tests, query parsers, or similar focused changes, do not use the production build merely as a substitute for a missing `typecheck` script when focused tests, lint, code inspection, or other scoped checks already provide sufficient validation.

   Before running a production build during an individual implementation step, identify the concrete reason why build-level validation is necessary.

   If no concrete reason exists, skip the build.

   If a production build is run, the final response must state that reason explicitly.

6. Do not run the full project test suite, full regression suite, or other project-wide test command during a normal implementation step.

   Commands such as:
   - `npm test`;
   - full Jest without a focused file or pattern;
   - complete regression suites;

   are reserved for:
   - the dedicated final regression step;
   - the final polish workflow;
   - a current implementation step that explicitly requires the full suite.

   The presence of `npm test` in the plan's global `Validation` section is not permission to run it during an individual step.

   Do not run a full test suite "for additional confidence" when focused tests already validate the current step.

   If the current implementation step does not explicitly require full regression testing, do not run it.

Do not run the full `npm test` suite or a full production build during an ordinary isolated step merely because those commands appear in the plan's global `Validation` section.

Example:

If the current step adds an isolated utility and unit tests, normally run:
- the focused unit tests;
- relevant scoped lint or formatting checks.

Do not additionally run the complete project test suite or production build without a specific reason.

If a project-wide check is necessary during an individual step, briefly explain why in the final response.

### Avoid Redundant Validation

Once focused tests and relevant scoped checks have successfully validated the current step, do not add broader checks merely for extra confidence.

Prefer stopping validation when the current change is sufficiently covered.

Do not escalate automatically from:

focused tests → scoped lint → full tests → build.

Each additional validation command must have its own concrete reason.

### Validation Failures

Never claim that a check passed unless it actually ran successfully.

If a relevant check fails because of the current implementation:
- investigate the failure;
- fix it within the current step;
- rerun the relevant check;
- keep the step unfinished until the failure is resolved.

If a check fails because of a pre-existing or unrelated project problem:
- verify that the failure is unrelated to the current step;
- report it clearly;
- do not modify unrelated code merely to make the check green;
- use the most focused available validation to verify the current step.

## Manual Browser Testing

Manual browser testing is required when the current change cannot be confidently validated through code inspection and automated checks alone.

Typical examples:
- visual UI changes;
- navigation;
- interaction behavior;
- forms;
- loading states;
- empty states;
- error states;
- success states;
- responsive behavior;
- focus or keyboard behavior;
- browser-specific behavior;
- functionality that depends on an authenticated application session.

Pure utilities, schemas, data transforms, isolated server actions, or query parsers normally do not require browser testing unless the current step introduces browser-visible behavior.

## Before Opening the Browser

Do not access or control Chrome automatically.

Before receiving explicit user approval, do not:
- open a browser tab;
- open a browser window;
- start browser automation;
- restart Chrome;
- start or restart the development server solely for browser testing.

First finish:
- implementation;
- relevant automated validation.

Then provide a short manual testing plan in Russian.

The manual testing plan is mandatory.

Do not ask for Chrome permission without first showing the testing plan to the user.

The plan must contain concrete checks for the current step.

Each check must include:
- the page or state to open;
- the action to perform;
- the expected result.

Do not replace the testing plan with a generic statement such as:
- "manual testing is required";
- "check the UI";
- "verify the feature";
- "test in Chrome".

For an interactive UI step, include the main user interactions introduced or changed by that step.

Only after showing the manual testing plan may you ask:

`Можно перейти в Chrome и начать ручное тестирование через Chrome plugin?`

Keep it concise and limited to the current implementation step.

For each check describe:
- where to navigate;
- what action to perform;
- what result is expected.

Normally use only a few focused checks.

Then ask exactly:

`Можно перейти в Chrome и начать ручное тестирование через Chrome plugin?`

Stop and wait for explicit confirmation.

Do not mark the current step completed yet.

## Browser Environment

The usual development application is expected to already be open and authenticated in Chrome at:

`http://localhost:3000`

Use this as the default only when the current project does not specify another development URL.

The user may prepare the environment after receiving the manual testing plan and before approving browser access.

This may include:
- starting the development server;
- opening `http://localhost:3000`;
- signing in;
- navigating to the required initial state.

Do not assume the browser is ready until the user confirms that testing may begin.

## Resuming Browser Validation

If the current conversation is already waiting for browser-testing approval for a step that has been implemented and automatically validated:

After the user approves:
- do not restart implementation;
- do not repeat successful automated checks without a specific reason;
- continue directly with the pending browser validation.

The user's Chrome approval remains valid for the current implementation step unless:
- the user asks to stop;
- the required testing scope materially changes;
- a meaningful side effect requires separate confirmation.

## Using Chrome

After explicit user approval:

- use the available Chrome/browser integration;
- use the existing development application tab;
- prefer an existing tab whose origin is `http://localhost:3000`;
- navigate inside the same tab as needed;
- use the existing authenticated session.

Do not:
- open additional tabs;
- open additional browser windows;
- replace the user's existing Chrome session;
- log the user out unnecessarily;
- change accounts unnecessarily;
- reset the browser session.

If the project uses another development URL, use the existing tab for that URL.

If no suitable application tab is available:
- do not create one automatically;
- tell the user that the required tab is not available;
- ask the user to open the application and authenticate if necessary;
- wait for confirmation.

If Chrome integration is unavailable or fails:
- report the concrete problem;
- do not claim that browser testing was performed;
- keep the current plan step unfinished;
- provide the short manual testing instructions so the user can perform them manually.

## During Browser Testing

Test only behavior relevant to the current implementation step.

Do not perform unrelated exploratory testing.

Prefer non-destructive validation.

Do not modify application data unnecessarily.

Do not log out, switch accounts, or change authentication state unless:
- the current test genuinely requires it;
- the user explicitly agrees.

### Meaningful Side Effects

If browser testing requires a meaningful side effect, such as:
- sending a transfer;
- making a payment;
- publishing content;
- deleting data;
- submitting data that affects real users;
- changing permissions;
- performing another irreversible or externally visible action;

do not perform the final action based only on the general Chrome-testing permission.

Explain what action is required and ask for explicit confirmation immediately before performing the meaningful side effect.

Use the least destructive available test path.

### Browser Test Result

If browser testing succeeds:
- mark the current implementation step completed.

If browser testing reveals a problem:
- keep the step unfinished;
- investigate the problem;
- fix it within the scope of the current step;
- rerun only relevant automated validation;
- retest the affected behavior.

Do not start the next implementation step.

## User-Performed Manual Testing

The user may perform the requested manual browser testing themselves instead of allowing Codex to control Chrome.

If the user clearly reports that the requested manual test succeeded, for example:
- "всё работает";
- "проверил, работает";
- "ручное тестирование прошло";
- another unambiguous confirmation;

treat this as successful manual validation for the current step.

In this case:
- do not repeat the same browser test through Chrome integration;
- mark the current step completed;
- mention that manual testing was confirmed by the user;
- stop before the next step.

If the user's response is ambiguous, ask for clarification before marking the step completed.

If the user reports a problem:
- keep the step unfinished;
- investigate and fix the problem within the scope of the current step;
- rerun relevant focused checks;
- provide updated manual testing instructions when another browser check is needed.

## Reopening the Current Completed Step

If the user reports a problem, missing requirement, visual mismatch, or regression related to the step that was just completed, and the next implementation step has not started yet:

- treat it as a continuation of the same implementation step;
- do not start the next plan step;
- change the completed step back from `- [x]` to `- [ ]` while the correction is in progress;
- inspect the user's feedback and the relevant requirement, design, or documentation;
- fix only the issue related to that step;
- run only the focused validation required for the correction;
- require manual browser testing again when the correction affects browser-visible behavior;
- mark the step back to `- [x]` only after the correction is successfully validated.

Do not create a new implementation step for a small correction to the step that was just completed.

If the user's feedback introduces genuinely new scope that was not part of the original task, explain that before changing the plan.

## Updating the Plan

Only mark the current step completed after all validation required for that step succeeds.

Change:

`- [ ] Step ...`

to:

`- [x] Step ...`

Do not:
- alter the meaning of the step merely to make it appear completed;
- mark future steps completed;
- remove unresolved risks or questions that still matter.

If implementation reveals an important new:
- requirement;
- decision;
- risk;
- dependency;
- limitation;

update the relevant part of `.codex/IMPLEMENTATION_PLAN.md` when doing so keeps the working plan accurate.

## When There Are No Remaining Steps

If all implementation steps are already completed:
- do not modify code;
- tell the user there are no unfinished implementation steps;
- recommend proceeding to final validation or polish;
- stop.

## Final Response Without Browser Testing

When the current step is completed without browser testing, briefly report:
- which step was completed;
- what was changed;
- which focused checks were run;
- why a project-wide check was run, if one was necessary;
- any unrelated pre-existing validation failure discovered;
- that the step was marked completed in `.codex/IMPLEMENTATION_PLAN.md`.

Then stop.

Do not start the next step.

## Final Response Before Browser Testing

When browser testing is required:
- briefly state what was implemented;
- report the automated checks that passed;
- provide the short manual testing plan;
- ask permission to use Chrome;
- keep the step unfinished;
- stop and wait for the user.

Do not start the next step.

## Final Response After Browser Testing

When browser testing succeeds, briefly report:
- which step was completed;
- what was verified in Chrome;
- which focused automated checks passed;
- that the step was marked completed in `.codex/IMPLEMENTATION_PLAN.md`.

Then stop.

Do not start the next step.
