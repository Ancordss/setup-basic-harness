---
name: setup-basic-harness
description: "Scaffolds a project coordination harness: progress tracking (current.md, feature_list.json, history.md), subagent docs (Lead, Implementer, Checker + optional extras), and AGENTS.md wiring. Use when starting a new project, saying 'set up harness', 'create basic harness', 'scaffold progress tracking', or 'set up agent coordination'."
---

# Setup Basic Harness

Scaffold a complete agent coordination harness for any project. Creates the progress tracking system, base subagent docs, and wires everything into AGENTS.md.

**Announce at start:** "I'm using the setup-basic-harness skill to scaffold the project coordination harness."

## What Gets Created

The exact directory for agent docs depends on the detected host (see Step 1b). Below shows the general structure:

```
<project-root>/
  progress/
    current.md          -- Session state tracker
    feature_list.json   -- Planned/in-progress/done features
    history.md          -- Chronological change log
  {{AGENTS_DIR}}/
    lead.md             -- Coordination and planning agent
    implementer.md      -- Hands-on builder agent
    checker.md          -- Verification and quality agent
    <extra-agents>.md   -- Any project-specific agents requested
  AGENTS.md             -- Created or updated with subagent references
  CLAUDE.md             -- (Claude Code only) Redirect pointing to AGENTS.md
```

Where `{{AGENTS_DIR}}` resolves to one of:

| Host | Agents directory |
|---|---|
| OpenCode | `.opencode/agents/` |
| Claude Code | `.claude/agents/` |
| Cursor | `.cursor/agents/` |
| Unknown / generic | `agents/` |

## Execution Steps

Follow these steps in order. Do not skip any.

### Step 1a: Detect Project Context

Before creating anything, gather context:

1. **Project name**: Check for `package.json` (name field), `Cargo.toml`, `go.mod`, `pyproject.toml`, or fall back to the directory name.
2. **Existing files**: Check if `progress/`, the agents directory (see Step 1b), or `AGENTS.md` already exist.
   - If `progress/` exists: warn the user and ask whether to skip, merge, or overwrite.
   - If the agents directory exists: list existing agent docs. Only create missing base agents. Never overwrite existing ones.
   - If `AGENTS.md` exists: append the subagent and progress sections. Never overwrite existing content.
3. **Today's date**: Use for initial entries in `history.md` and `feature_list.json`.

### Step 1b: Detect Host and Set Agents Directory

Determine which coding tool is running the skill. Use the following detection logic in order of priority:

1. **Check environment context** -- The system prompt or environment info often identifies the host (e.g., "You are OpenCode", "Claude Code", tool/model metadata).
2. **Check for host-specific config directories** at the project root:
   - `.opencode/` exists → OpenCode
   - `.claude/` exists → Claude Code
   - `.cursor/` exists → Cursor
3. **Check for host-specific config files** at the project root:
   - `opencode.json` or `opencode.yaml` → OpenCode
   - `.cursorrules` → Cursor
4. **Fallback** → generic (use plain `agents/` directory)

Once detected, set `{{AGENTS_DIR}}` to the appropriate path:

| Host detected | `{{AGENTS_DIR}}` value |
|---|---|
| OpenCode | `.opencode/agents` |
| Claude Code | `.claude/agents` |
| Cursor | `.cursor/agents` |
| generic / unknown | `agents` |

Also set `{{HOST_NAME}}` to the detected host name (used in the summary and CLAUDE.md redirect).

**If the host-specific parent directory does not yet exist** (e.g., `.opencode/` for OpenCode), create it before creating the agents subdirectory.

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
4. Replace `{{AGENTS_DIR}}` with the resolved agents directory path from Step 1b.
5. For extra agents, use `templates/extra-agent.md` as the base and fill in the agent-specific details.

**Creation order:**
1. `progress/` directory
2. `progress/current.md`
3. `progress/feature_list.json`
4. `progress/history.md`
5. `{{AGENTS_DIR}}/` directory (create parent + subdirectory if missing)
6. `{{AGENTS_DIR}}/lead.md`
7. `{{AGENTS_DIR}}/implementer.md`
8. `{{AGENTS_DIR}}/checker.md`
9. Any extra agent files in `{{AGENTS_DIR}}/`
10. `AGENTS.md` (create or append) -- always at project root
11. `CLAUDE.md` redirect (Claude Code host only -- see Step 4b)

### Step 4a: Update AGENTS.md

**If AGENTS.md does not exist**, create it using `templates/AGENTS-new.md`.

**If AGENTS.md already exists**, append the content from `templates/AGENTS-append.md` at the end of the file. Check that a `## Subagents` section does not already exist before appending -- if it does, warn the user and skip.

### Step 4b: Create CLAUDE.md Redirect (Claude Code only)

**This step only runs when the detected host is Claude Code.**

Claude Code reads `CLAUDE.md` as its primary instructions file. Since this harness puts all agent coordination logic in `AGENTS.md`, create a `CLAUDE.md` that redirects to `AGENTS.md`.

**If `CLAUDE.md` does not exist**: create it using `templates/CLAUDE-redirect.md`.

**If `CLAUDE.md` already exists**: ask the user how to handle it:

> "A CLAUDE.md file already exists. Since we're using AGENTS.md for agent coordination, how would you like to handle the existing CLAUDE.md?"

Options:
1. **Migrate** -- Append existing CLAUDE.md content into AGENTS.md (under a `## Original CLAUDE.md Content` section), then replace CLAUDE.md with the redirect.
2. **Keep both** -- Leave existing CLAUDE.md as-is and append a note at the top pointing to AGENTS.md for agent coordination.
3. **Skip** -- Do not touch CLAUDE.md at all.

### Step 5: Confirm

Print a summary of everything created:

```
Harness scaffolded for {{PROJECT_NAME}} (detected host: {{HOST_NAME}}):

  progress/current.md          -- Fill this with current work state
  progress/feature_list.json   -- Add planned features here
  progress/history.md          -- First entry logged

  {{AGENTS_DIR}}/lead.md       -- Coordination agent
  {{AGENTS_DIR}}/implementer.md -- Builder agent
  {{AGENTS_DIR}}/checker.md    -- Quality gate
  [{{AGENTS_DIR}}/<extra>.md]  -- Any extras

  AGENTS.md                    -- Updated with subagent references
  [CLAUDE.md]                  -- (Claude Code only) Redirect to AGENTS.md

Next: Update progress/current.md with your first task.
```

## Conflict Handling

- **Never overwrite** existing files without explicit user confirmation.
- **Never delete** existing content in AGENTS.md.
- If an agent doc already exists in `{{AGENTS_DIR}}/`, skip it and note: "Skipped {{AGENTS_DIR}}/<name>.md -- already exists."
- If `progress/` files already exist, ask: overwrite, merge (keep existing + add missing fields), or skip.
- If `CLAUDE.md` already exists (Claude Code host), ask the user before touching it (see Step 4b).

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
| `templates/CLAUDE-redirect.md` | CLAUDE.md redirect for Claude Code host |
