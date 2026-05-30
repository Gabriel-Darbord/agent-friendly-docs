# Manual: Writing Agent-Friendly Repository Documentation

Date: 2026-05-30.

Audience: coding agents asked to document a repository so future agents can
work in it reliably.

Purpose: teach an agent how to design, write, and verify repository
documentation for agents. This manual is standalone and generic: it does not
assume a language, framework, vendor, or agent product.

## 1. Goal

Do not create one giant instruction file. Create a small documentation system
that loads the right context for the task.

A good agent documentation set lets a new agent quickly answer:

- What is this repository responsible for?
- What rules always apply?
- What should I read for this task?
- Which commands verify my change?
- Which files or operations are risky, generated, or off-limits?
- What evidence should I report before saying the work is done?

## 2. Core Principle

Agent documentation is context design.

The goal is not to maximize information. The goal is to make high-priority
instructions hard to miss and detailed instructions easy to find when relevant.

Use a short entrypoint plus focused modules. Put mandatory enforcement in
tools, hooks, CI, permissions, or tests rather than relying only on prose.

## 3. Output Shape

Choose the smallest structure that fits the repository.

| Need | Put It In |
| --- | --- |
| Applies to almost every task | Root entrypoint, usually `AGENTS.md` |
| Applies to one task type | Task module, such as `docs/agents/testing.md` |
| Applies to one subtree | Nested/path-scoped instructions |
| Repeated multi-step workflow | Skill, prompt file, or workflow module |
| Must be enforced | Hook, permission, CI check, or test |
| Needs live external context | Tool, connector, or service integration |
| Personal/local preference | Gitignored local override |
| Tool-specific discovery requirement | Thin adapter file |
| Long reference material | Normal docs linked from a module |

Common files:

- `AGENTS.md`: source of truth for cross-agent repository instructions.
- `docs/agents/*.md`: task modules.
- nested `AGENTS.md` files: subtree-specific rules.
- `CLAUDE.md`, `GEMINI.md`, `.github/copilot-instructions.md`: optional thin
  adapters when a tool needs a different filename.
- `AGENTS.local.md`: optional gitignored local overrides.

Tiny repositories may only need one `AGENTS.md`.

## 4. Workflow For The Documentation Agent

### Step 1: Inspect The Repository

Read:

- existing agent instruction files;
- README and contributor docs;
- build, test, lint, and formatter configuration;
- CI configuration;
- package/workspace manifests;
- scripts used by maintainers;
- generated-file markers;
- release, deployment, migration, or review docs;
- nested package or component docs.

Capture:

- source roots and test roots;
- fast and full verification commands;
- generated or vendored directories;
- risky commands and external side effects;
- monorepo/component boundaries;
- default branch and contribution workflow;
- local setup gotchas;
- stale or conflicting instructions.

Do not infer the workflow only from language or framework names. Read the repo.

### Step 2: Identify Likely Agent Mistakes

Look for places where future agents might:

- edit generated files manually;
- run the wrong tests;
- skip verification;
- run destructive commands;
- confuse monorepo packages;
- mutate external services unintentionally;
- violate architecture or style conventions;
- use the wrong credentials or account;
- duplicate existing abstractions;
- rely on unavailable services;
- ignore local dirty changes.

Only document what prevents real mistakes or shortens real discovery.

### Step 3: Choose The Structure

Use this rule of thumb:

- Put always-on safety and routing in the root.
- Put details in modules.
- Put subtree exceptions near the subtree.
- Put repeated procedures in skills or workflow modules.
- Put hard gates in hooks, CI, permissions, or tests.

If the root grows because of a specialized topic, move that topic to a module
and add a context-loading route.

### Step 4: Draft The Root Entrypoint

The root entrypoint is a compact operating contract and router.

Template:

```markdown
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

## Local Overrides

- Optional personal instructions may live in `<path>`.
- Local overrides are gitignored and must not be required by team workflows.
```

For large repositories, keep the root roughly 100-200 lines when possible.
Shorter is better if routing is clear.

### Step 5: Write Context Loading As A Routing Table

The context-loading section is the key scaling mechanism.

Good:

```markdown
## Context Loading

- Before backend API changes: read `docs/agents/backend.md`.
- Before frontend changes: read `docs/agents/frontend.md`.
- Before database migrations: read `docs/agents/database.md`.
- Before generated client changes: read `docs/agents/codegen.md`.
- Before CI changes: read `docs/agents/ci.md`.
- Before code review: read `docs/agents/code-review.md`.
```

Weak:

```markdown
See the docs directory for more details.
```

Rules:

- Use concrete triggers.
- Use exact paths.
- Say "read", not "consider".
- State order when multiple files apply.
- Do not route every task to every file.
- Keep detailed rules out of the routing section.

### Step 6: Write Task Modules

Task modules carry depth without bloating the root.

Template:

```markdown
# <Task Area> Agent Rules

Applies when: <concrete trigger>.

## Goal

<What this module helps the agent do.>

## Must Do

- <Concrete rule.>
- <Concrete rule.>

## Do Not

- <Concrete prohibition.>
- <Concrete prohibition.>

## Procedure

1. <Step.>
2. <Step.>

## Verification

- Run `<command>` for <case>.
- If not run, report why.

## References

- `<path or URL>`
```

Good module topics:

- testing;
- code review;
- build and CI;
- generated code;
- data migrations;
- release and deployment;
- language or framework conventions;
- security-sensitive areas;
- monorepo package workflow;
- architecture boundaries.

### Step 7: Add Path-Scoped Instructions Only When Needed

Use path-scoped instructions when a subtree has materially different rules:

- separate packages in a monorepo;
- generated or vendored code;
- infrastructure or deployment areas;
- tests with unusual fixtures;
- experimental code;
- language or framework islands.

Keep subtree instructions narrow. Do not repeat root rules unless the local
tool requires it.

### Step 8: Add Tool Adapters Without Duplicating Content

Many agent tools use different filenames or loading rules. Avoid maintaining
several full instruction files.

Recommended strategy:

- make `AGENTS.md` the conceptual source of truth;
- use `CLAUDE.md`, `GEMINI.md`, or Copilot instruction files only when needed;
- make adapters import, symlink, or point to the source of truth when possible;
- if an adapter must contain tool-specific content, keep it minimal;
- periodically check adapters for drift.

### Step 9: Move Enforcement Out Of Prose

Markdown guidance is advisory unless another system enforces it.

Use hooks, CI, permissions, or tests for:

- formatting;
- generated-file freshness;
- secret scanning;
- command blocking;
- required checks;
- protected directories;
- release or deployment gates.

Use docs to explain the rule and where enforcement lives.

## 5. Writing Rules

### Be Concrete

Prefer:

```markdown
- Run `pnpm test --filter @acme/api` after API behavior changes.
- Do not edit `src/generated/`; run `pnpm codegen`.
- Use `ApiErrorResponse` from `src/api/errors.ts` for API errors.
```

Avoid:

```markdown
- Follow best practices.
- Write clean code.
- Test thoroughly.
```

Concrete rules name paths, commands, APIs, ownership, and evidence.

### Be Verifiable

Each important rule should be performable or checkable. If nobody can tell
whether the agent complied, rewrite the rule.

Good rules answer:

- What should the agent do?
- When should it do it?
- How can it verify the result?
- What should it report if blocked?

### Separate Facts, Policies, And Procedures

Facts:

- `Source lives in packages/api/src.`
- `The default branch is main.`

Policies:

- `Do not edit generated files manually.`
- `Publication requires human approval.`

Procedures:

- `Run pnpm codegen, then pnpm test:clients.`

Put stable facts and policies in the root. Put detailed procedures in modules
or skills.

### Front-Load Critical Rules

Put these near the top:

- preserve unrelated changes;
- avoid destructive or externally mutating commands without approval;
- do not claim checks ran when they did not;
- read task-specific modules before editing;
- respect generated and protected files.

### Keep It Current

Do not include:

- long history;
- stale roadmap items;
- full API references;
- generic software advice;
- personal preferences;
- secrets;
- commands the agent cannot safely run;
- rules the team does not follow.

If useful material is long, link to it instead of pasting it.

## 6. Verification Checklist

After writing or changing agent docs, check:

- The root entrypoint exists and is short enough to scan.
- Context-loading routes have exact paths and concrete triggers.
- Commands in the docs exist in scripts, manifests, Makefiles, or CI.
- Paths in the docs exist or are intentionally new.
- Generated, vendored, protected, or risky areas are called out.
- Verification expectations are explicit.
- Local override files are gitignored if introduced.
- Tool-specific adapter files do not duplicate large content.
- Modules do not contradict root rules.
- No secrets or private machine-specific values were added.
- The docs tell agents what to report back.

Run the repository's Markdown checks if available. At minimum, inspect the diff
and check for trailing whitespace.

## 7. Dry-Run Test

Before considering the docs done, simulate three tasks:

1. A simple source change.
2. A change touching a risky, generated, or protected area.
3. A testing, CI, review, release, or deployment task.

Starting only from the root entrypoint, confirm:

- the next file to read is obvious;
- the relevant commands are named;
- boundaries and approvals are clear;
- the expected final evidence is clear.

If the dry run requires guessing, improve routing or module content.

## 8. Maintenance Rules

Update agent docs when:

- an agent repeats a preventable mistake;
- a reviewer gives the same correction more than once;
- commands or workflows change;
- generated-file boundaries change;
- publication, deployment, or external-state policies change;
- modules grow too large;
- tool-specific adapters drift;
- a new teammate would need the same context.

Remove rules when they are obsolete, generic, contradicted by current practice,
or fully replaced by automated enforcement.

Prefer deleting stale detail over explaining its history.

## 9. Common Patterns

### Small Repository

One file can be enough:

```markdown
# Agent Guide

## Read First

- Preserve unrelated changes.
- Run `npm test` before claiming behavior changes are complete.

## Project Facts

- Source: `src/`
- Tests: `test/`
- Build: `npm run build`

## Workflow

1. Inspect relevant code.
2. Make the smallest scoped change.
3. Add or update tests.
4. Run `npm test` or explain why not.
```

### Large Repository

Use a root router plus modules:

```markdown
# Agent Guide

## Read First

- Preserve unrelated changes.
- Use package-local commands where possible.

## Context Loading

- Frontend packages: read `docs/agents/frontend.md`.
- Backend services: read `docs/agents/backend.md`.
- Database changes: read `docs/agents/database.md`.
- CI changes: read `docs/agents/ci.md`.
- Code review: read `docs/agents/code-review.md`.
```

### Generated Code

```markdown
- Do not edit `src/generated/` manually.
- To update generated code, run `pnpm codegen`.
- After code generation, run `pnpm test:generated`.
```

If generated files must be fresh, enforce that in CI.

### External Side Effects

```markdown
- Do not run commands that deploy, delete data, charge accounts, send
  messages, rotate credentials, or mutate shared services without explicit
  approval.
- For local reproductions, use the sandbox commands in
  `docs/agents/sandbox.md`.
```

### Unfamiliar Language Or Framework

When agents often mishandle a language or framework, add a focused module:

- semantic model;
- idiomatic design rules;
- common anti-patterns;
- examples of acceptable patterns;
- task-specific verification;
- authoritative references.

Route to it before source changes in that area.

### Flaky Or Expensive Tests

```markdown
- Preferred fast check: `bundle exec rspec spec/models/foo_spec.rb`.
- Full suite is slow; run it only for shared behavior changes.
- If a known flaky test fails, rerun once and report both results.
- Do not delete or weaken tests to make the suite pass.
```

## 10. Final Response Expected From A Documentation Agent

When you finish documenting a repository, report:

- files created or changed;
- why the structure was chosen;
- how future agents should start from the root file;
- checks run;
- checks not run and why;
- known gaps or recommended future automation.

## 11. Quality Bar

The documentation is good when it is:

- small at the root;
- concrete in commands and paths;
- routed by task;
- honest about verification;
- explicit about boundaries;
- free of duplicated tool-specific drift;
- backed by automation for mandatory controls;
- useful to a fresh agent without a long conversation.

If the result is mostly generic advice, it is not done.

## Source Basis

This manual consolidates practices from OpenAI Codex guidance, the AGENTS.md
format project, GitLab's public agent-doc layout, Anthropic Claude Code
guidance, GitHub Copilot custom-instruction guidance, and Google Jules/Gemini
context-file guidance.
