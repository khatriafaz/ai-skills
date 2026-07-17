---
name: sentry-issue-to-pr
description: Investigate a Sentry issue, find its evidence-backed root cause, implement the smallest safe fix, validate it, and open a GitHub pull request. Use whenever the user provides a Sentry issue ID or URL and asks to fix, resolve, remediate, implement, or raise/open/create a PR for it. This is the implementation workflow; use proposal-only Sentry triage instead when the user explicitly asks for analysis without code changes.
compatibility: Requires read access to Sentry issue events and GitHub authentication with permission to push a branch and create pull requests.
---

# Sentry Issue to PR

Own the issue from investigation through pull request creation. Accuracy matters more than speed: do not turn the first plausible stack frame into a speculative patch.

Invoking this skill authorizes scoped code changes, a commit, pushing the working branch, and opening a pull request. It does not authorize changing the Sentry issue, deploying, merging, or modifying unrelated code.

## Input

Expect a Sentry issue ID, short identifier, or URL. If it is missing or cannot uniquely identify an issue, ask for the missing identifier or organization/project context.

Before working, read repository instructions such as `AGENTS.md` and inspect the current branch and working tree. Preserve unrelated changes.

## Workflow

### 1. Establish the evidence

Use available Sentry tooling in read-only mode. Collect:

- issue title, status, severity, project, environment, and permalink
- first seen, last seen, event count, affected users, and releases
- exception type, message, mechanism, and handled state
- complete in-app stack frames
- request or transaction context, tags, breadcrumbs, and relevant extra data
- the latest event and representative events that reveal whether the failure pattern is consistent

Do not resolve, ignore, assign, comment on, or otherwise mutate the Sentry issue.

### 2. Prove the root cause

Map the in-app frames to the local repository and trace the real execution path end to end. Inspect routes, callers, shared helpers, validation, persistence, and external integrations involved in the failure.

Search every caller of a function before changing it. Prefer one correction at the shared broken boundary over guards scattered across individual callers.

Build a concrete evidence chain:

`event input/state -> code assumption -> failing operation -> observed exception`

Compare multiple events when the issue may have more than one cause. Use relevant history or release information when it helps identify a regression. Distinguish the root cause from downstream symptoms and from expected third-party failures.

If available evidence cannot support a root cause with reasonable confidence, stop and report exactly what context is missing. Do not guess merely to produce a patch.

### 3. Reproduce the failure

Derive the smallest reliable reproduction from Sentry evidence and the local code path. Prefer a focused automated regression test when practical; otherwise document a precise manual reproduction.

Record the failing behavior before editing so the same check can verify the fix. Follow existing test patterns rather than introducing a new test framework or fixture system.

### 4. Apply the minimal fix

Change the fewest files and lines needed to correct the proven root cause.

- reuse existing validation, helpers, and error-handling patterns
- fix the shared source of invalid state when possible
- preserve tenant boundaries, authorization, and trust-boundary validation
- handle only states supported by evidence
- avoid unrelated refactors, new abstractions, dependencies, formatting churn, and speculative fallbacks

Add one focused regression test for non-trivial logic when practical. Do not weaken or delete a valid test to make the change pass.

### 5. Validate

Follow the repository's required runtime and commands. Run, at minimum:

- the focused regression or closest relevant test
- tests covering the affected route, service, job, or integration when proportionate
- the repository lint command

Repeat the original reproduction if possible. Review the final diff for unrelated changes, security regressions, tenant isolation, null handling, backward compatibility, and accidental behavior changes.

If a check cannot run or fails for a pre-existing environmental reason, capture the exact command and failure. Do not claim it passed.

### 6. Create the pull request

Once the root cause is supported, the fix is complete, and validation is proportionate:

1. Confirm the diff contains only scoped changes.
2. Commit using the repository's commit convention.
3. Push the current working branch without force-pushing.
4. Open a GitHub pull request against the repository's normal base branch.

Follow these PR conventions while preserving the repository's other applicable conventions:

- format the title as `<SENTRY_ISSUE_ID>: <concise fix title>`
- apply the `sentry-fix` label
- include the exact line `Fixes <SENTRY_ISSUE_ID>` in the body, replacing the placeholder with the issue ID

Also include in the PR body:

- Sentry issue ID and permalink
- evidence-backed root cause
- what changed and why it is the smallest safe fix
- reproduction or regression coverage
- exact validation commands and results
- remaining risk, limitations, or checks that could not run

Do not merge the PR. If authentication, permissions, branch state, missing production context, or failed validation prevents safe completion, stop and report the exact blocker instead of fabricating success.

## Completion response

Return:

- the PR URL
- a one-paragraph root-cause and fix summary
- tests and lint run with results
- any remaining risk or follow-up
