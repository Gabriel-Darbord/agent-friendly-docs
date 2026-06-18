# Agent-Friendly Docs

Manual and starter templates for documenting repositories so coding agents can
work in them more reliably.

The canonical artifact is:

- `agent-documentation-manual.md`

Hand that file to an agent when asking it to make a repository agent-friendly.
The templates under `templates/` are optional starting points for the files the
manual recommends.

## What This Is For

Use this repository when you want an agent to:

- inspect an unfamiliar repository;
- create or improve an `AGENTS.md`;
- split long agent instructions into task-specific modules;
- document verification commands, safety boundaries, generated-file rules, and
  local override conventions;
- make repository instructions portable across agent tools without duplicating
  the source of truth.

## Suggested Prompt

```text
Read agent-documentation-manual.md, then inspect this repository and create
agent-friendly documentation for it. Keep the root instructions short, route
task-specific details to modules, and verify the documented commands and paths.
```

## Files

- `agent-documentation-manual.md`: standalone manual for agents.
- `templates/AGENTS.md`: compact root entrypoint template.
- `templates/task-module.md`: task-specific module template.
