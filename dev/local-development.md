---
name: local-development
description: Work on software changes in a local Git repository using an approval-driven feature-branch and patch workflow. Preserve an approved baseline, keep tasks isolated, validate changes with the tooling actually available, and produce reviewable patches before merging locally.
---

# Local Development

Use this skill when working on a software project locally where changes should be developed on isolated Git branches, reviewed as patches, and merged into a local baseline only after approval.

The workflow is intentionally project-agnostic. Build systems, SDKs, package caches, linters, test runners, repository conventions, and other tooling are supplied by the task or discovered from the repository. Do not assume any particular language, SDK, package manager, or online connectivity.

## Core model

Maintain one local branch as the current approved development baseline. Each implementation or documentation task starts from that baseline on a fresh feature branch.

The baseline represents the state that has already been reviewed and accepted. Do not merge work into it until the user approves the current task.

A typical lifecycle is:

1. establish or update the local baseline,
2. create a task branch from that baseline,
3. implement and validate one coherent task,
4. return a patch against the baseline for review,
5. apply requested cleanup on the same task branch,
6. return incremental patches relative to the previously reviewed task state when appropriate,
7. merge the task branch into the local baseline only after approval,
8. start the next task from the newly approved baseline.

This keeps review history understandable and prevents unrelated work from accumulating in one branch or patch.

## 1. Establish the local repository

If the user provides a repository archive, snapshot, or other source tree as the development baseline:

- unpack it into a dedicated local working directory,
- initialize a Git repository if one does not already exist,
- create an initial baseline commit containing exactly that supplied state,
- use a stable baseline branch name such as `main` unless the repository already defines another intended branch,
- do not mix exploratory or previous failed work into the new baseline.

If an older local working tree exists from a previous attempt, either move it aside for reference or remove it before initializing the new baseline. Never silently combine the old state with a newly supplied baseline.

When a repository already contains Git history, preserve it unless the user explicitly asks for a fresh local history.

## 2. Read project instructions before implementation

Before changing code or documentation, inspect repository-specific instructions that materially affect the task. Examples include:

- contributor or agent instructions,
- coding-style documents,
- architecture documentation,
- project READMEs,
- build/test instructions,
- task-specific design documents.

Follow those instructions over generic preferences in this skill.

If the project defines authoritative architecture or design documentation for the affected subsystem, read it before implementation so the change is consistent with the existing system model.

## 3. Establish the available toolchain

Determine what development tooling is actually available from the prompt, repository, and local environment.

Examples may include:

- language SDKs or compilers,
- package caches or offline feeds,
- test runners,
- linters or formatters,
- generators,
- native toolchains,
- container runtimes.

Do not assume tooling is installed merely because the repository normally uses it. Conversely, if the user provides an SDK, compiler, package cache, or other development dependency, prefer using the supplied local tooling rather than reaching for remote services.

When practical, validate the unchanged baseline before starting implementation. This helps distinguish pre-existing/environmental failures from regressions introduced by the task.

If part of the normal build cannot run in the supplied environment, identify the limitation precisely and continue with the strongest available validation rather than pretending full validation succeeded.

## 4. Create one branch per coherent task

Before implementation, create a fresh local feature branch from the current approved baseline.

Keep a branch focused on one coherent task or architectural slice. Prefer several reviewable stacked tasks over one branch that mixes unrelated changes.

Examples of good task boundaries include:

- one subsystem refactor,
- one architectural migration stage,
- one new reusable primitive,
- one documentation/planning pass,
- one integration slice with focused tests.

Do not start follow-up implementation work on the same branch merely because it is convenient. Once the current task reaches a reviewable boundary, stop and return it for review.

## 5. Implement iteratively

Work on the task locally and validate continuously.

During implementation:

- preserve repository conventions,
- prefer focused responsibility boundaries over incidental coupling,
- add or update tests alongside behavioral changes,
- keep unrelated cleanup out of the branch unless it is required to make the task coherent,
- periodically review the full branch diff rather than only the most recent edits.

For larger architectural tasks, split work internally into small implementation batches and validate after each meaningful batch, even though the final review patch may contain the entire branch.

## 6. Validate the task branch

Before presenting a task for review, run the strongest relevant validation supported by the available environment.

Depending on the project, this may include:

- targeted unit tests,
- broader regression suites,
- full builds,
- static analysis,
- formatting checks,
- generated-code validation,
- documentation link checks,
- `git diff --check`.

Distinguish clearly between:

- successful validation,
- validation that was not available,
- validation that failed because of an environmental/tooling limitation,
- validation that exposed a real implementation problem.

Do not describe a task as complete while known implementation failures remain.

## 7. Produce the review patch

When the task is ready for review:

- keep the feature branch unmerged,
- commit the task locally if useful for a stable review point,
- generate a Git patch representing the feature branch relative to the current approved baseline,
- verify that the patch applies cleanly to that baseline when practical,
- provide the patch to the user together with a concise summary of the task and validation results.

The review patch should contain only the current task. The baseline itself must remain unchanged until approval.

## 8. Handle review follow-ups incrementally

If the user requests cleanup or corrections:

- remain on the same task branch,
- make only the requested or directly necessary follow-up changes,
- rerun relevant validation,
- return an incremental patch relative to the previously reviewed task state when that makes review easier.

An incremental patch should contain only the delta since the previous patch or review point, not the entire task again, unless the user asks for a refreshed full patch.

Keep the full branch coherent throughout review. Incremental patches are a review convenience, not separate divergent implementations.

## 9. Merge only after approval

Once the user approves the task:

- merge the task branch into the local baseline,
- ensure the baseline points at the complete approved state,
- keep that merged baseline as the starting point for subsequent tasks,
- create the next feature branch from the updated baseline.

Do not merge merely because tests pass. Review approval is the merge boundary in this workflow.

## 10. Recovering local state

If the local Git working tree is lost or reset but the original baseline and previously approved patches are still available, reconstruct the approved state deterministically:

1. restore the original baseline,
2. initialize or restore the local Git repository,
3. reapply approved patches in order,
4. recreate the approved baseline commit/state,
5. continue development from that reconstructed baseline.

Do not guess at previously approved changes or reconstruct them from memory when exact patches are available.

## Patch and branch discipline

Prefer branch and patch names that communicate the task rather than internal implementation trivia.

Examples:

- `feature/transaction-core`
- `refactor/runtime-responsibilities`
- `docs/execution-architecture`

A branch should answer one review question: "Is this task ready to become part of the baseline?"

A patch should be easy to apply, inspect, and reject independently of later tasks.

## Completion criteria

A local development task is ready for review when:

- it was developed from the current approved baseline,
- the branch contains one coherent task,
- repository-specific instructions were followed,
- relevant tests/builds/checks supported by the environment pass,
- known environmental limitations are documented precisely,
- the full branch diff has been reviewed for accidental or unrelated changes,
- a clean patch against the approved baseline has been produced,
- the branch remains unmerged pending user approval.

A task is complete in the local workflow only after the user approves it and it has been merged into the local baseline.
