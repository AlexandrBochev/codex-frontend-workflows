---
name: task-plan
description: Analyze a frontend development task and create a detailed step-by-step implementation plan in Russian before making any code changes. Use when the user provides a task specification, Jira requirements, PM requirements, attached task description, or asks to analyze and plan a frontend task before implementation.
---

# Task Plan

Create a detailed implementation plan for a frontend development task.

This skill is for planning only.

Do not implement the task, modify production code, or start the first implementation step.

## Sources of Requirements

The task specification provided by the user is the primary source of requirements.

The specification may be:

- pasted directly into the prompt;
- attached as a file;
- attached as a screenshot;
- attached as a document;
- copied from Jira or another task tracker;
- written by a Product Manager or another team member;
- accompanied by additional instructions or clarifications from the user.

Read all provided task materials before analyzing the codebase.

Distinguish between:

1. explicit requirements from the task specification;
2. additional instructions from the user;
3. existing project conventions;
4. assumptions inferred from the current implementation.

Do not silently replace, weaken, or reinterpret explicit task requirements because the current implementation works differently.

If the user's explicit instruction conflicts with the attached task specification, treat the user's latest explicit instruction as authoritative and mention the conflict in the plan when relevant.

## Workflow

1. Read all task requirements and attached materials carefully.

2. Read the applicable project instructions:
   - check whether the repository contains `AGENTS.md`;
   - read every `AGENTS.md` that applies to the relevant files or directories;
   - follow project-specific instructions together with the global Codex instructions.

3. Inspect the existing codebase before creating the plan:
   - locate the files, routes, components, hooks, utilities, types, APIs, and tests related to the task;
   - understand the current implementation and data flow;
   - identify existing patterns and abstractions that can be reused;
   - inspect relevant tests when they exist;
   - inspect `package.json`, scripts, formatting, linting, type-checking, testing, and build tooling when relevant;
   - identify whether similar functionality already exists elsewhere in the project.

4. Compare every meaningful requirement from the task specification with the existing implementation.

5. Identify:
   - what needs to change;
   - what should remain unchanged;
   - which files or areas are likely to be affected;
   - dependencies between changes;
   - potential regressions;
   - risky areas;
   - relevant automated checks;
   - places that may require manual browser testing.

6. Verify that every meaningful requirement from the provided specification is represented in the implementation plan.

   Do not omit a requirement simply because the current codebase does not yet have a corresponding implementation.

7. If important information is missing or a requirement is ambiguous:
   - do not invent requirements;
   - do not guess silently;
   - record the missing information explicitly in the plan;
   - explain why it matters;
   - identify which implementation step depends on the missing information;
   - add a clear question to the `Open Questions` section.

8. Create a step-by-step implementation plan.

## Plan File

Save the plan to:

`.codex/IMPLEMENTATION_PLAN.md`

Create the `.codex` directory if it does not exist.

The plan file is a temporary working file for the current task.

Do not add it to git staging and do not commit it unless the user explicitly asks.

If `.codex/IMPLEMENTATION_PLAN.md` already exists for another task, replace its contents with the new task plan.

## Plan Requirements

Write the plan in Russian.

The plan must:

- briefly describe the goal of the task;
- summarize the relevant existing implementation;
- mention important findings discovered during code inspection;
- list affected files or areas when they can be identified;
- cover every meaningful requirement from the provided specification;
- break implementation into small logical steps;
- keep each implementation step focused on one meaningful change;
- put steps in the correct execution order;
- mention relevant validation for each step where useful;
- mention manual browser testing only where it is actually useful;
- clearly list missing information or open questions;
- clearly list potential risks and regression areas.

Use Markdown checkboxes for implementation steps:

`- [ ]`

Do not mark implementation steps as completed during the planning phase.

## Step Size

Prefer small implementation steps.

A step should normally be small enough that it can be:

1. implemented;
2. reviewed;
3. validated;
4. manually tested if necessary;
5. marked completed;

before moving to the next step.

Do not combine unrelated changes into one step.

Avoid overly broad steps such as:

- "Implement the feature";
- "Update frontend";
- "Fix everything";
- "Add tests".

Describe the concrete change that should be made.

## Required Structure

Use this structure:

# Implementation Plan

## Goal

Short description of what needs to be achieved.

## Requirements Summary

Brief summary of the meaningful requirements from the provided task specification.

## Existing Implementation

Short summary of the relevant current implementation and important findings from code inspection.

## Affected Areas

List the files, components, hooks, routes, APIs, tests, or other areas likely to be affected.

If exact files cannot yet be identified, describe the relevant areas instead.

## Implementation Steps

- [ ] Step 1: ...
- [ ] Step 2: ...
- [ ] Step 3: ...

## Validation

List the automated checks that should be run during or after implementation.

Prefer existing project commands when available, such as:

- formatting;
- Prettier;
- lint;
- type checking;
- unit or integration tests;
- build.

Do not invent commands that do not exist in the project.

## Manual Testing

List only manual browser checks that are expected to be useful.

For every manual check, briefly state:

- where to navigate;
- what action to perform;
- what result is expected.

If no manual testing is currently expected, state that explicitly.

## Risks

List likely regression areas, edge cases, or implementation risks.

If no meaningful risks were identified, state that explicitly.

## Open Questions

List missing information, ambiguities, contradictions, or decisions that need clarification.

For each question, briefly explain which part of implementation depends on it.

If nothing important is missing, state that explicitly.

## Final Response

After creating the plan:

- do not start implementation;
- do not modify production code;
- tell the user that the plan has been created;
- mention `.codex/IMPLEMENTATION_PLAN.md`;
- briefly mention important open questions, if any;
- wait for the user to approve the plan or request changes.
