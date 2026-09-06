# custom-skills

A personal collection of custom skills and prompts for AI coding and study assistants.

Each skill is a self-contained Markdown document with a YAML frontmatter block declaring its `name` and `description`, followed by the workflow, rules, and completion criteria the agent should follow when the skill is invoked.

## Repository layout

```text
dev/
  dotnet-offline.skill
  local-development.skill
  technical-documentation.skill
studying/
  exam-material-extraction.skill
  exam-material-extraction.prompt.md
  exam-material-optimization.skill
  exam-material-optimization.prompt.md
  exam-workspace-bootstrap.skill
```

- `*.skill` files contain the skill definition (frontmatter + workflow instructions).
- `*.prompt.md` files are short companion prompts that invoke a skill for a specific task.

## File format

Every skill starts with a YAML frontmatter block:

```markdown
---
name: <skill-identifier>
description: <one-paragraph description of when to use this skill>
---

# <Skill title>

<workflow, rules, tool strategy, completion criteria>
```

The `description` is written so an agent can decide whether the skill applies to the current task. The body describes the workflow in enough detail that the agent can execute it without further guidance.

## Skills

### dev

- [dev/dotnet-offline.skill](dev/dotnet-offline.skill) — Bootstrap and validate a .NET development environment from a supplied SDK archive and an offline NuGet package archive for development work in isolated/sandboxed environments. Covers isolated `DOTNET_ROOT`, preseeding an isolated global-packages cache from `.nupkg` files, disabling all package sources for restore validation, sanitizing inherited MSBuild environment properties, and diagnosing package-owned native tool permissions.
- [dev/local-development.skill](dev/local-development.skill) — Approval-driven feature-branch and patch workflow for local Git repositories. One coherent task per branch, patch-based review against an approved baseline, incremental patches for follow-ups, and merge only after user approval.
- [dev/technical-documentation.skill](dev/technical-documentation.skill) — Architecture-first technical documentation guidance. Describe the steady-state system, keep implementation detail at the right abstraction level, preserve good existing documentation, and keep related documents internally coherent after code changes.

### studying

- [studying/exam-workspace-bootstrap.skill](studying/exam-workspace-bootstrap.skill) — Bootstrap a course-material workspace for study and exam-prep agents. Inventories every source (including archive members), builds a high-level topic-to-source index, establishes source authority, and derives an exam-focus reference from content evidence and same-professor exam style.
- [studying/exam-material-extraction.skill](studying/exam-material-extraction.skill) — Deterministic, source-grounded, resumable extraction of exam-focused Anki cards from lecture PDFs. Requires visual inspection of every physical page, per-page checkpointing, fail-closed handling on inspection failure, and a final coverage gate over the full page range.
- [studying/exam-material-optimization.skill](studying/exam-material-optimization.skill) — Optimize an existing Anki card set for exam recall using non-slide course material (assignments, exercises, solutions, focus documents, past exams) as enrichment and priority evidence. Preserves source-grounded content, compresses low-yield detail, and keeps every disposition auditable.

## Prompts

Companion prompts are small entry points that invoke a skill for a concrete task:

- [studying/exam-material-extraction.prompt.md](studying/exam-material-extraction.prompt.md) — Runs the extraction skill against a specific `{LECTURE}` PDF and returns the resulting Anki CSV plus coverage summary.
- [studying/exam-material-optimization.prompt.md](studying/exam-material-optimization.prompt.md) — Runs the optimization skill against a previously extracted card set, using supplied non-slide course material as enrichment and priority evidence.

## Usage

These files are agent instructions, not executable code. Point a compatible AI assistant at the relevant `.skill` file (directly or via a companion `.prompt.md`) and let it follow the workflow.
