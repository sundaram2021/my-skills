---
name: fix-the-issue
description: Fix bugs from existing GitHub issues or urgent hotfixes, verify changes, and ensure all tests and builds pass.
---

# fix-the-issue

Comprehensive workflow for AI agents to investigate, reproduce, fix, and verify bugs from GitHub issues or urgent hotfixes.

## When to use

Use this skill whenever tasked with:
- Investigating and resolving an existing GitHub issue in the codebase.
- Implementing an urgent hotfix for a reported bug or regression.
- Diagnosing, fixing, and thoroughly verifying code issues while ensuring existing functionality remains intact.

## Pre-requisites & Branching Strategy

AI agents must never make edits directly on `main`/`master` or pollute an uncommitted working tree. Before making any code modifications:

1. **Verify Working Tree Cleanliness**:
   - Run `git status` to inspect current branch and working directory state.
   - If uncommitted changes exist, stop and notify the user or safely stash them (`git stash`) so existing work is preserved.
2. **Sync with Base Branch**:
   - Ensure the latest changes from the default branch (`main` or `master`) are fetched before branching (`git pull origin <default-branch>`).
3. **Create and Switch to a Dedicated Branch**:
   - Always isolate all fixes in a new, dedicated branch:
     - **For GitHub issues**: `git checkout -b fix/issue-<issue-number>-<short-slug>`
     - **For hotfixes**: `git checkout -b hotfix/<short-slug>`
   - Use lowercase, kebab-case, and concise descriptors for branch names.
4. **No Automatic Commits**:
   - **Do NOT run `git commit`**. All changes must remain uncommitted on the newly created branch for the user to review via `git diff` / `git status`.

## Instructions

Follow these steps sequentially to investigate, fix, and verify the issue:

### 1. Reproduce / Re-generate the Issue
- Attempt to reproduce (re-generate) the problem reported in the GitHub issue or hotfix description.
- Confirm whether the bug actually exists in the current environment before modifying any code.
- If the issue cannot be reproduced:
  - Verify reproduction steps, environment details, dependencies, and configuration.
  - If still unreproducible, inform the user with findings before making code changes.

### 2. Understand the Issue & Clarify
- Thoroughly analyze the root cause and requirements of the issue.
- If any details, requirements, or expected behaviors are unclear or ambiguous, ask the user clarifying questions at this stage before proceeding.

### 3. Analyze Relevant Code, Patterns & Impact
- Locate the relevant files and code paths involved in the failure.
- Study existing codebase patterns, conventions, and style.
- Evaluate the impact and blast radius of the bug and the potential fix across the application.
- Check if existing tests are present in the codebase. If tests exist, review their setup and coverage; if there are no tests in the project, ignore test checks.

### 4. Implement the Fix
- Fix the issue strictly following the existing code style and conventions—do not introduce new, unfamiliar patterns.
- Maximize the reuse of existing functions, helpers, and utilities already available in the codebase.
- Keep the diff minimal and strictly focused on the bug—avoid unnecessary refactoring or style reformatting.
- If the project has an existing test suite, write tests covering the bug fix and preventing regressions. If tests do not exist in the codebase, skip writing tests.

### 5. Verify (Tests, Lint, Build & CI)
- Run all tests, linting checks, and build commands using the project's configured tooling and scripts.
- If any test or build fails, iterate and fix until clean; do not disable or skip failing tests.
- Ensure the fix does not break any existing functionality or introduce regressions.
- Verify that all CI checks and build pipelines will pass smoothly.
- Clean up any scratch files, temporary reproduction scripts (e.g., `temp_repro.*`), or debug logs to keep the working tree pristine.

### 6. Summary of the Fix
Keep it brief, simple, and avoid over-explaining:
- **What was fixed**: 1–2 simple sentences explaining the fix.
- **Files modified**: Short bullet list of changed files and what was done in each.
- **Tests**: Mention if tests were added/passed, or note that tests don't exist.
- **Branch**: State the branch name where the uncommitted fix is ready for review.

## Agent Guardrails & Best Practices

- **Do not commit or push**: Never run `git commit` or `git push`. Keep all verified modifications uncommitted in the working tree on the dedicated branch so the user has full control over reviewing and committing the code.
- **Never edit default branch directly**: Never perform edits on `main` or `master`. Always create and switch to a dedicated bugfix/hotfix branch first.
- **Respect configured tooling & package managers**: Always detect and use the tooling and package manager configured in the codebase (e.g., if `bun` is configured, use `bun test`, `bun run build`, and `bun run lint` instead of defaulting to `npm`; similarly for `pnpm`, `yarn`, `poetry`, etc.). Never use or mix mismatched package managers.
- **Clean up temporary artifacts**: Always delete scratch reproduction scripts, temporary test files, and debug logs before completing the task.
- **Minimal blast radius**: Do not refactor unrelated code, reformat untouched files, or upgrade unrelated packages.
- **Match existing patterns**: Stick strictly to how the existing codebase is written; do not invent new patterns.
- **Maximize code reuse**: Leverage existing functions, helpers, and modules instead of writing redundant implementations.
- **Test alignment**: Write tests only if an existing test framework and suite already exist in the repository; otherwise, ignore test authoring.
- **Fail fast and ask**: If the bug cannot be reproduced or behavior is ambiguous, ask the user rather than guessing.
