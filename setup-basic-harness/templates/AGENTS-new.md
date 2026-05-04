# {{PROJECT_NAME}} -- Agent Guidelines

## Subagents

The following agents form the implementation pipeline. Each has a dedicated doc in `agents/` with full instructions.

### Lead (`agents/lead.md`)

Coordination and planning agent. Breaks down feature requests into tasks, assigns them to the Implementer, tracks progress via `progress/current.md` and `progress/feature_list.json`, and makes decisions when agents are blocked. The Lead does not implement or verify -- it plans, delegates, and unblocks.

### Implementer (`agents/implementer.md`)

Hands-on builder agent. Receives well-defined tasks from the Lead and produces working implementations. Hands off completed work to the Checker.

### Checker (`agents/checker.md`)

Verification and quality agent. Reviews every implementation before it is considered done. Checks against acceptance criteria and reports pass/fail with specific findings. Does not fix issues -- sends them back to the Implementer.

{{EXTRA_AGENT_SECTIONS}}

### Agent Interaction Flow

```
Human / Feature Request
        |
        v
      Lead  ---- reads/updates ----> progress/current.md
        |                             progress/feature_list.json
        |                             progress/history.md
        v
   Implementer -- builds --> code / deliverables
        |
        v
     Checker -- verifies --> pass/fail
        |
        +-- fail --> back to Implementer
        +-- pass --> Lead marks done
```

## Progress Tracking

- `progress/current.md` -- Current work state. Read this at the start of every session.
- `progress/feature_list.json` -- All planned, in-progress, and completed features.
- `progress/history.md` -- Log of significant changes.
