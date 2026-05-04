# setup-basic-harness

An agent skill that scaffolds a complete project coordination harness for any codebase -- progress tracking, subagent docs, and AGENTS.md wiring -- in one invocation.

## What it creates

```
your-project/
  progress/
    current.md          -- Session state tracker (what you're working on now)
    feature_list.json   -- Planned, in-progress, and completed features
    history.md          -- Chronological change log
  agents/
    lead.md             -- Coordination and planning agent
    implementer.md      -- Hands-on builder agent
    checker.md          -- Verification and quality agent
    <extras>.md         -- Any project-specific agents you request
  AGENTS.md             -- Created or updated with subagent references
```

## Install

```bash
npx skills add ancordss/setup-basic-harness
```

## Usage

Once installed, the skill triggers when you say things like:

- "set up harness"
- "create basic harness"
- "scaffold progress tracking"
- "set up agent coordination"

The skill will:

1. Detect your project name from `package.json`, `Cargo.toml`, `go.mod`, etc.
2. Check for existing `progress/`, `agents/`, or `AGENTS.md` (never overwrites without asking)
3. Ask if you need extra agents beyond the base 3 (Lead, Implementer, Checker)
4. Generate all files with your project name and today's date filled in
5. Create or update `AGENTS.md` with subagent references and an interaction flow diagram

## The agent pipeline

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

## Customization

The base harness always includes 3 agents. You can add project-specific extras during setup:

| Agent | Suggested for |
|---|---|
| DevOps Agent | Projects with CI/CD, infra, monitoring |
| Code Reviewer | Code repos with PR workflows |
| API Designer | API-heavy projects |
| Data Agent | DB migrations, data pipelines |
| Custom | You name it, you describe it |

## Compatibility

Works with any AI coding agent that supports the skills ecosystem:

- Claude Code
- OpenCode
- Cursor
- Cline
- Codex
- Windsurf
- And more

## License

MIT
