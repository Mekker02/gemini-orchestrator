# gemini-orchestrator

Claude Code skill: **Claude as orchestrator, Gemini CLI as worker agents.**

Claude plans and synthesizes. Gemini researches, reads large contexts, and drafts. One-line install.

## Install

```bash
/plugin install mekker/gemini-orchestrator
```

## Prerequisites

Gemini CLI must be installed:

```bash
npm install -g @google/gemini-cli
gemini --version
```

## Usage

Say any of:
- "use gemini as agent"
- "delegate to gemini"
- "orchestrate with gemini"
- `/gemini-agent`

Claude will decompose your request, dispatch subtasks to Gemini CLI, collect outputs, and synthesize the final result.

## How it works

```
User request
  → Claude decomposes into subtasks
  → gemini -p "<task>" called per subtask
  → Claude collects + synthesizes outputs
  → Claude applies changes (Edit/Write tools)
```

## Task routing

| Task | Agent |
|------|-------|
| Large file analysis | Gemini |
| Web research | Gemini |
| Code draft (first pass) | Gemini |
| Secondary code review | Gemini |
| File edits | Claude |
| Architectural decisions | Claude |
| Tool calls (DB, git) | Claude |
| Final synthesis | Claude |

## Structure

```
.claude-plugin/
  plugin.json
  marketplace.json
skills/
  gemini-orchestrator/
    SKILL.md
```
