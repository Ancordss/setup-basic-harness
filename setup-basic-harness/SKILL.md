---
name: setup-basic-harness
description: "Scaffolds a project coordination harness: progress tracking (current.md, feature_list.json, history.md), subagent docs (Lead, Implementer, Checker + optional extras), and AGENTS.md wiring. Use when starting a new project, saying 'set up harness', 'create basic harness', 'scaffold progress tracking', or 'set up agent coordination'."
---

# Setup Basic Harness

Scaffold a complete agent coordination harness for any project. Creates the progress tracking system, base subagent docs, and wires everything into AGENTS.md.

**Announce at start:** "I'm using the setup-basic-harness skill to scaffold the project coordination harness."

## What Gets Created

```
<project-root>/
  progress/
    current.md          -- Session state tracker
    feature_list.json   -- Planned/in-progress/done features
    history.md          -- Chronological change log
  agents/
    lead.md             -- Coordination and planning agent
    implementer.md      -- Hands-on builder agent
    checker.md          -- Verification and quality agent
    <extra-agents>.md   -- Any project-specific agents requested
  AGENTS.md             -- Created or updated with subagent references
```

## Execution Steps

Follow these steps in order. Do not skip any.

### Step 1: Detect Project Context

Before creating anything, gather context:

1. **Project name**: Check for `package.json` (name field), `Cargo.toml`, `go.mod`, `pyproject.toml`, or fall back to the directory name.
2. **Existing files**: Check if `progress/`, `agents/`, or `AGENTS.md` already exist.
   - If `progress/` exists: warn the user and ask whether to skip, merge, or overwrite.
   - If `agents/` exists: list existing agent docs. Only create missing base agents. Never overwrite existing ones.
   - If `AGENTS.md` exists: append the subagent and progress sections. Never overwrite existing content.
3. **Today's date**: Use for initial entries in `history.md` and `feature_list.json`.

### Step 2: Ask About Extra Agents

After confirming the 3 base agents (Lead, Implementer, Checker), ask:

> "The base harness includes 3 agents: Lead (coordination), Implementer (building), and Checker (verification). Do you need any project-specific agents on top of these?"

Offer common options based on what the project looks like:

| Option | When to suggest | Description |
|---|---|---|
| DevOps Agent | Infra/deployment files detected | Handles CI/CD, monitoring, alerts |
| Code Reviewer | Code repos with PRs | Reviews PRs for quality and architecture |
| API Designer | API-heavy projects | Designs and maintains API contracts |
| Data Agent | DB migrations or data pipelines | Handles schema changes, queries, data flows |
| Custom | Always offer | User names it and describes the role |

If the user declines extras, proceed with just the base 3.

### Step 3: Create Files

Use the templates in `templates/` as the base content. For each file:

1. Replace `{{PROJECT_NAME}}` with the detected project name.
2. Replace `{{DATE}}` with today's date (YYYY-MM-DD).
3. Replace `{{AGENT_LIST}}` with the full list of agents (base + extras).
4. For extra agents, use `templates/extra-agent.md` as the base and fill in the agent-specific details.

**Creation order:**
1. `progress/` directory
2. `progress/current.md`
3. `progress/feature_list.json`
4. `progress/history.md`
5. `agents/` directory (if missing)
6. `agents/lead.md`
7. `agents/implementer.md`
8. `agents/checker.md`
9. Any extra agent files
10. `AGENTS.md` (create or append)

### Step 4: Update AGENTS.md

**If AGENTS.md does not exist**, create it using `templates/AGENTS-new.md`.

**If AGENTS.md already exists**, append the content from `templates/AGENTS-append.md` at the end of the file. Check that a `## Subagents` section does not already exist before appending -- if it does, warn the user and skip.

### Step 5: Confirm

Print a summary of everything created:

```
Harness scaffolded for {{PROJECT_NAME}}:

  progress/current.md          -- Fill this with current work state
  progress/feature_list.json   -- Add planned features here
  progress/history.md          -- First entry logged

  agents/lead.md               -- Coordination agent
  agents/implementer.md        -- Builder agent
  agents/checker.md            -- Quality gate
  [agents/<extra>.md]          -- Any extras

  AGENTS.md                    -- Updated with subagent references

Next: Update progress/current.md with your first task.
```

## Conflict Handling

- **Never overwrite** existing files without explicit user confirmation.
- **Never delete** existing content in AGENTS.md.
- If an agent doc already exists in `agents/`, skip it and note: "Skipped agents/<name>.md -- already exists."
- If `progress/` files already exist, ask: overwrite, merge (keep existing + add missing fields), or skip.

## Templates

All templates are in the `templates/` subdirectory of this skill. Read them at runtime -- do not hardcode their content in this file.

| Template | Purpose |
|---|---|
| `templates/current.md` | Progress tracker template |
| `templates/feature_list.json` | Feature registry template |
| `templates/history.md` | Change log template |
| `templates/lead.md` | Lead agent doc |
| `templates/implementer.md` | Implementer agent doc |
| `templates/checker.md` | Checker agent doc |
| `templates/extra-agent.md` | Template for any additional agent |
| `templates/AGENTS-new.md` | Full AGENTS.md for new projects |
| `templates/AGENTS-append.md` | Section to append to existing AGENTS.md |
