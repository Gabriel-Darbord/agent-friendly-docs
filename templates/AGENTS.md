# Agent Guide For <Project>

<One short paragraph: project purpose and ownership boundary.>

## Read First

- Preserve unrelated user changes.
- Do not run destructive or externally mutating commands without approval.
- Do not claim verification unless the relevant checks actually ran.
- Read the task-specific context below before editing.

## Context Loading

- Before <task category>: read `<path/to/module.md>`.
- Before <task category>: read `<path/to/module.md>`.
- Before code review: read `<path/to/code-review.md>`.

## Project Facts

- Source root: `<path>`.
- Tests: `<command>`.
- Lint/static checks: `<command>`.
- Default branch: `<branch>`.
- Generated files: `<path>`; do not edit manually.

## Workflow

1. Inspect relevant code and instructions.
2. Make a scoped change.
3. Add or update focused tests when behavior changes.
4. Run relevant checks.
5. Report files changed, checks run, checks skipped, and remaining risk.

## Safety And Publication

- <Do-not rules and approval requirements.>

## Local Overrides

- Optional personal instructions may live in `<path>`.
- Local overrides are gitignored and must not be required by team workflows.
